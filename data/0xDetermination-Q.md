# QA report for veRWA by 0xDetermination
## [L-01] `LendingLedger.claim()` should use `nonReentrant` modifier
Because `claim()` contains an external call, the `nonReentrant` modifier should be used. (Note that `VotingEscrow.withdraw()` uses the `nonReentrant` modifier.)
### Recommendations
Add the `nonReentrant` modifier to `claim()`.

## [L-02] `LendingLedger.sol` can permanently lock up CANTO
Note that `LendingLedger.sol` is not meant to receive funds from anywhere except the reward distributor, and its `receive()` function is empty. Furthermore, the only way to withdraw funds from the contract is the `claim` function, which gives out appropriate rewards to users. Therefore, any funds sent to `LendingLedger.sol` that are not from the reward distributor will remain permanently locked in the contract.
### Recommendations
`receive()` should revert if funds are sent from an address that is not the reward distributor.

## [NC-01] "epoch" in `VotingEscrow.sol` does not refer to the same concept as "epoch" in the other two contracts
//Give Examples, elaborate on different "epoch" concept
Notice the code snippet from `GaugeController.sol` below (line 60):
```
        uint256 last_epoch = (block.timestamp / WEEK) * WEEK;
```
And this snippet from `LendingLedger.sol` (also on line 60):
```        
        uint256 currEpoch = (block.timestamp / WEEK) * WEEK;
```
Both of these snippets indicate that an "epoch" is a period lasting one week. Now, note the below snippet from `VotingEscrow.sol` (line 146):
```
            userPointEpoch[_addr] = uEpoch + 1;
```
This line runs in the `_checkpoint()` function and is executed every time a user calls `createLock()`, `increaseAmount()`, `withdraw()`, or `delegate()`. Because a user is not restricted to calling these functions once a week, "epoch" in `VotingEscrow.sol` cannot refer to a period of one week. Similarly, `VotingEscrow.globalEpoch` is incremented every time `_checkpoint()` is called. See the below code snippet (lines 211-220):
```         
            //The below code runs every time _checkpoint() is called.
            epoch = epoch + 1; //epoch was instantiated to globalEpoch, now it's incremented
            if (iterativeTime == block.timestamp) {
                lastPoint.blk = block.number;
                break;
            } else {
                pointHistory[epoch] = lastPoint;
            }
        }

        globalEpoch = epoch; //globalEpoch is incremented by 1
```
The difference in the meaning of "epoch" between the contracts is confusing.
### Recommendations
Refactor "epochs" in `VotingEscrow.sol` to something like "update_count".

## [NC-02] Comment typo in `GaugeController.vote_for_gauge_weights()` (Lines 215-220)
Notice below that the first comment indicating a return value is not in the correct position:
```
        (
            ,
            /*int128 bias*/
            int128 slope_, /*uint256 ts*/

        ) = ve.getLastUserPoint(msg.sender);
```
### Recommendations
```diff
        (
+           /*int128 bias*/,
-           /*int128 bias*/
            int128 slope_, /*uint256 ts*/

        ) = ve.getLastUserPoint(msg.sender);
```

## [NC-03] Unnecessary variable declaration in `GaugeController._change_gauge_weight()` (Lines 196-197)
Notice below that `new_sum` is only used to set `points_sum[next_time].bias`.
```
    function _change_gauge_weight(address _gauge, uint256 _weight) internal {
```
<details> <Summary> (Show/Hide extra code) </Summary>

```
        uint256 old_gauge_weight = _get_weight(_gauge);
        uint256 old_sum = _get_sum();
        uint256 next_time = ((block.timestamp + WEEK) / WEEK) * WEEK;

        points_weight[_gauge][next_time].bias = _weight;
        time_weight[_gauge] = next_time;
```
</details>

```
        uint256 new_sum = old_sum + _weight - old_gauge_weight;
        points_sum[next_time].bias = new_sum;
        time_sum = next_time;
    }
```
### Recommendations
```diff
-       uint256 new_sum = old_sum + _weight - old_gauge_weight;
-       points_sum[next_time].bias = new_sum;
+       points_sum[next_time].bias = old_sum + _weight - old_gauge_weight;
        time_sum = next_time;
    }
```

## [NC-04] `GaugeController._change_gauge_weight()` does not need to change the value of time_sum (Line 198)
`_change_gauge_weight()` sets `next_time` equal to the end of the week, then sets `time_sum` equal to `next_time`. This is unnecessary because `_change_gauge_weight()` calls `_get_sum()` earlier in the function, which sets `time_sum` equal to the end of the week. See below inline comments in `_get_sum()`:

```
    function _get_sum() internal returns (uint256) {
        uint256 t = time_sum; //t is a timestamp marking the end of an Epoch/week
        Point memory pt = points_sum[t];
        for (uint256 i; i < 500; ++i) {
            if (t > block.timestamp) break;
            t += WEEK; //t is incremented by 1 week until it equals the end of the current week, then the for loop breaks
            uint256 d_bias = pt.slope * WEEK;
            if (pt.bias > d_bias) {
                pt.bias -= d_bias;
                uint256 d_slope = changes_sum[t];
                pt.slope -= d_slope;
            } else {
                pt.bias = 0;
                pt.slope = 0;
            }
            points_sum[t] = pt;
            if (t > block.timestamp) time_sum = t; //time_sum is always set to t, equal to the end of the current week
        }
        return pt.bias;
    }
```
### Recommendations
```diff
-       time_sum = next_time;
    }
```

## [NC-05] NatSpec/Comments for `VotingEscrow._checkpoint()` params state "null" instead of "default values" (Lines 113-114)
Solidity does not have a null type; the comments should state something like "default values" for accuracy. (Notice that the params referred to are structs, and the global updating function `checkpoint()` calls `_checkpoint` with default-valued structs passed as arguments.)
```
    /// @notice Public function to trigger global checkpoint
    function checkpoint() external {
        LockedBalance memory empty;
        _checkpoint(address(0), empty, empty);
    }
```
```
    /// @notice Records a checkpoint of both individual and global slope
    /// @param _addr User address, or address(0) for only global
    /// @param _oldLocked Old amount that user had locked, or null for global
    /// @param _newLocked new amount that user has locked, or null for global
    function _checkpoint(
        address _addr,
        LockedBalance memory _oldLocked,
        LockedBalance memory _newLocked  
    ) internal {
```
### Recommendations
Change "null" to "default valued LockedBalance"

## [NC-06] NatSpec/Comments for `VotingEscrow._checkpoint()` can be revised for clarity (Line 111)
The @notice comment can change from "Records a checkpoint of both individual and global slope" to "Records a checkpoint of global slope, or both individual and global slope", since no individual checkpoint will be recorded if `_checkpoint` is called from `checkpoint()`.
### Recommendations
Apply stated change

## [NC-07] `VotingEscrow.sol` refers to `IVotingEscrow` for the NatSpec of certain functions, but `IVotingEscrow` does not exist in the repo
### Recommendations
Fill in missing NatSpec; alternatively, add `IVotingEscrow` to the repo and use `@inheritdoc`

## [NC-08] `VotingEscrow.sol` functions `createLock`, `increaseAmount`, and `delegate` have unnecessary `nonReentrant` modifiers (Lines 268, 288, 356)
These functions do not contain any external calls, so reentrancy is not an issue.
### Recommendations
Remove the `nonReentrant` modifiers from the stated functions.

## [NC-09] State variables in `GaugeController.sol` and `VotingEscrow.sol` are undocumented
### Recommendations
In-line comments can be added to the state variables in `GaugeController.sol` and `VotingEscrow.sol` for reader clarity

## [NC-10] `LendingLedger.sol` imports `VotingEscrow`, but it's unused
### Recommendations
```diff
-import {VotingEscrow} from "./VotingEscrow.sol";
```

## [NC-11] `LendingLedger.setRewards` can list the `onlyGovernance` modifier first (Line 192)
Modifiers are executed left-to-right; consider listing the `onlyGovernance` modifier first since it's the most important one.
### Recommendations
```diff
    function setRewards(
        uint256 _fromEpoch,
        uint256 _toEpoch,
        uint248 _amountPerEpoch
-   ) external is_valid_epoch(_fromEpoch) is_valid_epoch(_toEpoch) onlyGovernance {
+   ) external onlyGovernance is_valid_epoch(_fromEpoch) is_valid_epoch(_toEpoch) {
```

## [NC-12] `LendingLedger.claim()` silently fails if the user has already claimed their rewards for the week
Silent failure also occurs if the `_claimUpToTimestamp` argument is less than the `_claimFromTimestamp` argument (as long as both are valid epochs).
### Recommendations
```diff
-       if (claimEnd >= claimStart) { // If the check here fails, the function execution silently ends
+       require(claimEnd >= claimStart, "Invalid claim") // NOTE: This should be a custom error for gas savings
            // This ensures that we only set userClaimedEpoch when a claim actually happened
            for (uint256 i = claimStart; i <= claimEnd; i += WEEK) {
                uint256 userBalance = lendingMarketBalances[_market][lender][i];
                uint256 marketBalance = lendingMarketTotalBalance[_market][i];
                RewardInformation memory ri = rewardInformation[i];
                require(ri.set, "Reward not set yet"); // Can only claim for epochs where rewards are set, even if it is set to 0
                uint256 marketWeight = gaugeController.gauge_relative_weight_write(_market, i); // Normalized to 1e18
                cantoToSend += (marketWeight * userBalance * ri.amount) / (1e18 * marketBalance); // (marketWeight / 1e18) * (userBalance / marketBalance) * ri.amount;
            }
            userClaimedEpoch[_market][lender] = claimEnd + WEEK;
-       }
```