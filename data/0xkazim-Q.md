# Findings Summary
 
| ID     | Title                                                             | Severity | Status |
| ------ | -----------------------  -----------------------------------      | -------- | ------ |
| [L-01] | require missed for the _claimUpToTimestamp != current epoch       | low      | TBD    |
| [L-02] | setting the duration lock to 5 years when add amount not motivated| low      | TBD    |
| [L-03] | set msg.sender to payable is better                               | low      | TBD    |
| [L-04] | lock time is not exactly 5 years                                  | Low      | TBD    |
| [N-1] | wrong comment in the checks functions                              | Low      | TBD    |



# [L-01] require missed for the _claimUpToTimestamp != current epoch


**Impact:**
the function `claim` only allow to claim rewards for the past epoch not the current epoch but there is no check to prevent that, this may lead to set the epoch to the current epoch which is not allowed and cause unwanted behavior.

## POC

the function claim is implemented like this which not prevent claimed epoch to equal to the current epoch

```solidity 
file: src/contracts/lendingLedger.sol

function claim(
        address _market,
        uint256 _claimFromTimestamp,
        uint256 _claimUpToTimestamp
    )
        external
        is_valid_epoch(_claimFromTimestamp)
        is_valid_epoch(_claimUpToTimestamp)
    {
        address lender = msg.sender;
        uint256 userLastClaimed = userClaimedEpoch[_market][lender];
        require(userLastClaimed > 0, "No deposits for this user");
        _checkpoint_lender(_market, lender, _claimUpToTimestamp);
        //@audit require missed for the _claimUpToTimestamp != current epoch
        _checkpoint_market(_market, _claimUpToTimestamp);
        uint256 currEpoch = (block.timestamp / WEEK) * WEEK;
        uint256 claimStart = Math.max(userLastClaimed, _claimFromTimestamp);
        uint256 claimEnd = Math.min(currEpoch - WEEK, _claimUpToTimestamp);
        uint256 cantoToSend;
        if (claimEnd >= claimStart) {
            // This ensures that we only set userClaimedEpoch when a claim actually happened
            for (uint256 i = claimStart; i <= claimEnd; i += WEEK) {
                uint256 userBalance = lendingMarketBalances[_market][lender][i];
                uint256 marketBalance = lendingMarketTotalBalance[_market][i];
                RewardInformation memory ri = rewardInformation[i];
                require(ri.set, "Reward not set yet"); // Can only claim for epochs where rewards are set, even if it is set to 0
                uint256 marketWeight = gaugeController
                    .gauge_relative_weight_write(_market, i); // Normalized to 1e18
                cantoToSend +=
                    (marketWeight * userBalance * ri.amount) /
                    (1e18 * marketBalance); // (marketWeight / 1e18) * (userBalance / marketBalance) * ri.amount;
            }
            userClaimedEpoch[_market][lender] = claimEnd + WEEK;
        }
        if (cantoToSend > 0) {
            (bool success, ) = msg.sender.call{value: cantoToSend}("");
            require(success, "Failed to send CANTO");
        }
    }
```
https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/LendingLedger.sol#L152-L182

## Recommendations
add check to prevent the current epoch to be equal to the claimed epoch.



# [L-02] setting the duration lock to 5 years when add amount not motivated


**Impact:**
setting the lock time to 5 years again is not motivated way for users to lock more amounts in the contract, lets say someone want to increase lock amount after 4 years passed in this case s/he may not call`increaseAmount` because of the reset of the lock time to 5 years again, this will be more like punshment for users than motivation to lock more amount.
## POC

the function `increaseAmount` is implemented like this:

```solidity 
file: src/contracts/votingEscrow.sol

 function increaseAmount(uint256 _value) external payable nonReentrant {
        //here we get the users locked data
        LockedBalance memory locked_ = locked[msg.sender];
        // Validate inputs
        require(_value > 0, "Only non zero amount");
        require(msg.value == _value, "Invalid value");
        require(locked_.amount > 0, "No lock");
        require(locked_.end > block.timestamp, "Lock expired");
        // Update lock
        address delegatee = locked_.delegatee;
        //get the unlock time for the users
        uint256 unlockTime = locked_.end;
        LockAction action = LockAction.INCREASE_AMOUNT;
        // copy the old locked data to the newLocked variable data
        LockedBalance memory newLocked = _copyLock(locked_);
        //increase the amount for the newLocked data which contains the old data too!
        newLocked.amount += int128(int256(_value));
        //audit
        /**
        why we set the newLocked.end to the 5 years again ? lets imagine that we increase lock after 1 year passed 
        and the line below will set the lock time to the 5 years again  
        */
        // update the newLock.end to 5 years again
        newLocked.end = _floorToWeek(block.timestamp + LOCKTIME);
        // if we decide to delegate vote power to ourself!
        if (delegatee == msg.sender) {
            // Undelegated lock
            action = LockAction.INCREASE_AMOUNT_AND_DELEGATION;
            newLocked.delegated += int128(int256(_value));
            //here we set the storage locked data to the newLocked data which the end is set to 5 years again!
            locked[msg.sender] = newLocked;
            _checkpoint(msg.sender, locked_, newLocked);

            //if we delegate vote power to someone else !
        } else {
            // Delegated lock, update sender's lock first
            locked[msg.sender] = newLocked;
            _checkpoint(msg.sender, locked_, newLocked);
            // Then, update delegatee's lock and voting power (checkpoint)
            locked_ = locked[delegatee];
            require(locked_.amount > 0, "Delegatee has no lock");
            require(locked_.end > block.timestamp, "Delegatee lock expired");
            //set the delagte lock to the newLocked
            newLocked = _copyLock(locked_);
            newLocked.delegated += int128(int256(_value));
            locked[delegatee] = newLocked;
            _checkpoint(delegatee, locked_, newLocked);
            emit Deposit(
                delegatee,
                _value,
                newLocked.end,
                LockAction.DELEGATE,
                block.timestamp
            );
        }
        emit Deposit(msg.sender, _value, unlockTime, action, block.timestamp);
    }
```
## Recommendations
recommend to do something that benefit both side(protocol and users) by set the increase time to 2x the users increase amount(1 year become 2 years) or add function to increase lock time seperatly

# [L-03] set msg.sender to payable is better


**Impact:**
we send native tokens to msg.sender which it can be EOA and contracts, and it recommend to set it to payable so they can recieve native tokens. 

## POC

the function `withdraw` is implemented like this:

```solidity 
file: src/contracts/votingEscrow.sol

function withdraw() external nonReentrant {
        LockedBalance memory locked_ = locked[msg.sender];
        // Validate inputs
        require(locked_.amount > 0, "No lock");
        require(locked_.end <= block.timestamp, "Lock not expired");
        require(locked_.delegatee == msg.sender, "Lock delegated");
        // Update lock
        uint256 amountToSend = uint256(uint128(locked_.amount));
        LockedBalance memory newLocked = _copyLock(locked_);
        newLocked.amount = 0;
        newLocked.end = 0;
        newLocked.delegated -= int128(int256(amountToSend));
        newLocked.delegatee = address(0);
        locked[msg.sender] = newLocked;
        newLocked.delegated = 0;
        // oldLocked can have either expired <= timestamp or zero end
        // currentLock has only 0 end
        // Both can have >= 0 amount
        _checkpoint(msg.sender, locked_, newLocked);
        // Send back deposited tokens
        //@audit set message sender to payable because we send native tokens!
        (bool success, ) = msg.sender.call{value: amountToSend}("");
        require(success, "Failed to send CANTO");
        emit Withdraw(
            msg.sender,
            amountToSend,
            LockAction.WITHDRAW,
            block.timestamp
        );
    }
```
https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L326

and the `claim` function :

```
function claim(
        address _market,
        uint256 _claimFromTimestamp,
        uint256 _claimUpToTimestamp
    )
        external
        is_valid_epoch(_claimFromTimestamp)
        is_valid_epoch(_claimUpToTimestamp)
    {
        address lender = msg.sender;
        uint256 userLastClaimed = userClaimedEpoch[_market][lender];
        require(userLastClaimed > 0, "No deposits for this user");
        _checkpoint_lender(_market, lender, _claimUpToTimestamp);
        _checkpoint_market(_market, _claimUpToTimestamp);
        uint256 currEpoch = (block.timestamp / WEEK) * WEEK;
        uint256 claimStart = Math.max(userLastClaimed, _claimFromTimestamp);
        uint256 claimEnd = Math.min(currEpoch - WEEK, _claimUpToTimestamp);
        uint256 cantoToSend;
        if (claimEnd >= claimStart) {
            // This ensures that we only set userClaimedEpoch when a claim actually happened
            for (uint256 i = claimStart; i <= claimEnd; i += WEEK) {
                uint256 userBalance = lendingMarketBalances[_market][lender][i];
                uint256 marketBalance = lendingMarketTotalBalance[_market][i];
                RewardInformation memory ri = rewardInformation[i];
                require(ri.set, "Reward not set yet"); // Can only claim for epochs where rewards are set, even if it is set to 0
                uint256 marketWeight = gaugeController
                    .gauge_relative_weight_write(_market, i); // Normalized to 1e18
                cantoToSend +=
                    (marketWeight * userBalance * ri.amount) /
                    (1e18 * marketBalance); // (marketWeight / 1e18) * (userBalance / marketBalance) * ri.amount;
            }
            userClaimedEpoch[_market][lender] = claimEnd + WEEK;
        }
        if (cantoToSend > 0) {
            (bool success, ) = msg.sender.call{value: cantoToSend}("");
            require(success, "Failed to send CANTO");
        }
    }
```
https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/LendingLedger.sol#L152-L182
## Recommendations
add payable to the msg.sender.

# [L-04] lock time is not exactly 5 years


**Impact:**
the `LOCKTIME` is not exactly 5 years but its 4.99 year or 59.9 months, the protocol really focus on 4 years exactly so it should be set to `1827 days` which it equal to  years 

## POC

the function `withdraw` is implemented like this:

```solidity 
    uint256 public constant LOCKTIME = 1825 days;

```
## Recommendations
change the `LOCKTIME` to `1827 days`


# [N-01] wrong comment in the checks functions


**Impact:**
the checks function in the `ledgerLender.sol` have wrong comment which may lead to misunderstanding when call it.
## POC

the function `withdraw` is implemented like this:

```solidity 
//@audit it should be set to (this will be used --> the current epoch will be used)
     /// @param _forwardTimestampLimit Until which epoch (provided as timestamp) should the update be applied. If it is higher than the current epoch timestamp, this will be used.
    function checkpoint_lender( ...)

```
same thing for the `checkpoint_market`
## Recommendations
change the comment to the right one


