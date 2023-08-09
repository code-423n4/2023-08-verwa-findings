## [L-01] Missing checks for zero address for **_market** parameter

### Imapct

Functions in the context below are able to be called with address of zero for **_market** parameter.
This will lead to wrong keep tracking of users liquidity for certain market

### Context

[LendingLedger.sol#L55](https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L55)

[LendingLedger.sol#L83](https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L83)

[LendingLedger.sol#L106](https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L106)

[LendingLedger.sol#L117](https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L117)

[LendingLedger.sol#L152](https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L152)

[LendingLedger.sol#L204](https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L204)


## Proof of Concept
Missing check for address of zero for **_market**

```solidity
 function _checkpoint_lender(
        address _market,
        address _lender,
        uint256 _forwardTimestampLimit
    ) private {
        //  -------> Missing require statement to check _market <-------
        uint256 currEpoch = (block.timestamp / WEEK) * WEEK;

        uint256 lastUserUpdateEpoch = lendingMarketBalancesEpoch[_market][_lender];
        uint256 updateUntilEpoch = Math.min(currEpoch, _forwardTimestampLimit);
        if (lastUserUpdateEpoch == 0) {
            // Store epoch of first deposit
            userClaimedEpoch[_market][_lender] = currEpoch;
            lendingMarketBalancesEpoch[_market][_lender] = currEpoch;
        } else if (lastUserUpdateEpoch < currEpoch) {
            // Fill in potential gaps in the user balances history
            uint256 lastUserBalance = lendingMarketBalances[_market][_lender][lastUserUpdateEpoch];
            for (uint256 i = lastUserUpdateEpoch; i <= updateUntilEpoch; i += WEEK) {
                lendingMarketBalances[_market][_lender][i] = lastUserBalance;
            }
            if (updateUntilEpoch > lastUserUpdateEpoch) {
                lendingMarketBalancesEpoch[_market][_lender] = updateUntilEpoch;
            }
        }
    }
```

## Tools Used
Manual review

## Recommended Mitigation Steps
Add require to check **_market** parameter to be different from zero address.

```solidity
require(_market !== address(0), "_market cannot be zero address");
```

**Example**
```solidity
 function _checkpoint_lender(
        address _market,
        address _lender,
        uint256 _forwardTimestampLimit
    ) private {
        require(_market != address(0), "_market cannot be zero address"); <--- change
        uint256 currEpoch = (block.timestamp / WEEK) * WEEK;

        uint256 lastUserUpdateEpoch = lendingMarketBalancesEpoch[_market][_lender];
        uint256 updateUntilEpoch = Math.min(currEpoch, _forwardTimestampLimit);
        if (lastUserUpdateEpoch == 0) {
            // Store epoch of first deposit
            userClaimedEpoch[_market][_lender] = currEpoch;
            lendingMarketBalancesEpoch[_market][_lender] = currEpoch;
        } else if (lastUserUpdateEpoch < currEpoch) {
            // Fill in potential gaps in the user balances history
            uint256 lastUserBalance = lendingMarketBalances[_market][_lender][lastUserUpdateEpoch];
            for (uint256 i = lastUserUpdateEpoch; i <= updateUntilEpoch; i += WEEK) {
                lendingMarketBalances[_market][_lender][i] = lastUserBalance;
            }
            if (updateUntilEpoch > lastUserUpdateEpoch) {
                lendingMarketBalancesEpoch[_market][_lender] = updateUntilEpoch;
            }
        }
    }
```


## [L-02] Missing checks for zero address for **_lender** parameter 

### Imapct
Functions in the context below are able to be called with address of zero for **_lender** parameter.

[LendingLedger.sol#L55](https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L55)

[LendingLedger.sol#L117](https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L117)

[LendingLedger.sol#L129](https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L129)

## Proof of Concept
Missing check for address of zero for **_lender**


```solidity
function _checkpoint_lender(
        address _market,
        address _lender,
        uint256 _forwardTimestampLimit
    ) private {
        //  -------> Missing require statement to check _lender <-------
        uint256 currEpoch = (block.timestamp / WEEK) * WEEK;

        uint256 lastUserUpdateEpoch = lendingMarketBalancesEpoch[_market][_lender];
        uint256 updateUntilEpoch = Math.min(currEpoch, _forwardTimestampLimit);
        if (lastUserUpdateEpoch == 0) {
            // Store epoch of first deposit
            userClaimedEpoch[_market][_lender] = currEpoch;
            lendingMarketBalancesEpoch[_market][_lender] = currEpoch;
        } else if (lastUserUpdateEpoch < currEpoch) {
            // Fill in potential gaps in the user balances history
            uint256 lastUserBalance = lendingMarketBalances[_market][_lender][lastUserUpdateEpoch];
            for (uint256 i = lastUserUpdateEpoch; i <= updateUntilEpoch; i += WEEK) {
                lendingMarketBalances[_market][_lender][i] = lastUserBalance;
            }
            if (updateUntilEpoch > lastUserUpdateEpoch) {
                lendingMarketBalancesEpoch[_market][_lender] = updateUntilEpoch;
            }
        }
    }
```

## Tools Used
Manual review

## Recommended Mitigation Steps
Add require to check **_lender** parameter to be different from zero address.

```solidity
require(_lender_ !== address(0), "Invalid lender address");
```

**Example**

```solidity
function _checkpoint_lender(
        address _market,
        address _lender,
        uint256 _forwardTimestampLimit
    ) private {
        require(_lender != address(0), "Invalid lender address"); <--- change
        uint256 currEpoch = (block.timestamp / WEEK) * WEEK;

        uint256 lastUserUpdateEpoch = lendingMarketBalancesEpoch[_market][_lender];
        uint256 updateUntilEpoch = Math.min(currEpoch, _forwardTimestampLimit);
        if (lastUserUpdateEpoch == 0) {
            // Store epoch of first deposit
            userClaimedEpoch[_market][_lender] = currEpoch;
            lendingMarketBalancesEpoch[_market][_lender] = currEpoch;
        } else if (lastUserUpdateEpoch < currEpoch) {
            // Fill in potential gaps in the user balances history
            uint256 lastUserBalance = lendingMarketBalances[_market][_lender][lastUserUpdateEpoch];
            for (uint256 i = lastUserUpdateEpoch; i <= updateUntilEpoch; i += WEEK) {
                lendingMarketBalances[_market][_lender][i] = lastUserBalance;
            }
            if (updateUntilEpoch > lastUserUpdateEpoch) {
                lendingMarketBalancesEpoch[_market][_lender] = updateUntilEpoch;
            }
        }
    }
```