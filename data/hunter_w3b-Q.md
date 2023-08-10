## [L-01] Possible wrong calculation in `GaugeController::_change_gauge_weight` Function

The `_change_gauge_weight` function when updating the gauge weights. If the new gauge weight provided as input `_weight` is greater than the old gauge weight retrieved from `_get_weight(_gauge)`, an wrong calculation could occur during the calculation of the `new_sum` variable. This could lead to unexpected and potentially incorrect results in subsequent calculations.

https://github.com/code-423n4/2023-08-verwa/blob/main/src/GaugeController.sol#L188-L199

```solidity
File: src/GaugeController.sol

    function _change_gauge_weight(address _gauge, uint256 _weight) internal {
        uint256 old_gauge_weight = _get_weight(_gauge);
        uint256 old_sum = _get_sum();
        uint256 next_time = ((block.timestamp + WEEK) / WEEK) * WEEK;

        points_weight[_gauge][next_time].bias = _weight;
        time_weight[_gauge] = next_time;

        uint256 new_sum = old_sum + _weight - old_gauge_weight;
        points_sum[next_time].bias = new_sum;
        time_sum = next_time;
    }
```

### Recommendation

It is recommended to add a validation check that ensures the new gauge weight `_weight` is not greater than the old gauge weight `old_gauge_weight` before performing the calculation of `new_sum`.

This can be achieved by adding the following validation check before the calculation:

```solidity

require(_weight <= old_gauge_weight, "New gauge weight cannot be greater than old gauge weight");

```

## [L-02] Potential Timestamp Manipulation in `GaugeController::checkpoint_gauge`

The `checkpoint_gauge` function doesn't use or update the state of the `_gauge` parameter, and it only calls the internal functions `_get_weight` and `_get_sum`. If an external attacker is able to manipulate the timestamp of the blockchain, they could potentially manipulate the result of these functions, affecting the gauge weights and sum calculations. This could lead to inaccurate data.

https://github.com/code-423n4/2023-08-verwa/blob/main/src/GaugeController.sol#L141-L144

```solidity
    function checkpoint_gauge(address _gauge) external {
        _get_weight(_gauge);
        _get_sum();
    }
```

## [L-03] Possible Lock Duration Exploit in `GaugeController::vote_for_gauge_weights` Function

The `vote_for_gauge_weights` function in the GaugeController contract might be vulnerable to a lock duration exploit under the following circumstances:

1. The `_gauge_addr` parameter corresponds to a valid gauge address.
2. The `msg.sender` holds voting power and intends to change the pool weights for `_gauge_addr`.
3. The `_user_weight` parameter is set to a non-zero value (greater than or equal to 0.01% and less than or equal to 100%).
4. The user's voting power is locked for a duration that is shorter than the `_user_weight-scaled` time until the next checkpoint.

An attacker could potentially manipulate their lock duration to exploit the changing weights calculation. By using a lock duration that is shorter than the time it takes for the `_user_weight-scaled` weight change to occur, the attacker could claim more influence than their actual locked voting power, leading to improper governance decisions.

https://github.com/code-423n4/2023-08-verwa/blob/main/src/GaugeController.sol#L211-L278

```solidity

function vote_for_gauge_weights(address _gauge_addr, uint256 _user_weight) external {
    // ...
    uint256 next_time = ((block.timestamp + WEEK) / WEEK) * WEEK;
    require(lock_end > next_time, "Lock expires too soon");
    // ...
}

```

https://github.com/code-423n4/2023-08-verwa/blob/main/src/GaugeController.sol#L224-L225

## [L-04] Inaccurate Initialization of `time_sum` in GaugeController Constructor

In the constructor of the GaugeController contract, the `time_sum` variable is initialized using the current block's timestamp rounded to the nearest week. This initialization approach may lead to potential inaccuracies in calculating historical gauge weights, especially if the contract is deployed close to the start or end of a week.
The rounding of the current timestamp to the nearest week might result in an initial `time_sum` value that does not align with the actual starting point of historical data. If interactions with the contract are not frequent or if the deployment timing is not synchronized with the week boundary, it may cause discrepancies in historical weight calculations and adversely affect the contract's intended functionality.

https://github.com/code-423n4/2023-08-verwa/blob/main/src/GaugeController.sol#L57-L62

```solidity
    constructor(address _votingEscrow, address _governance) {
        votingEscrow = VotingEscrow(_votingEscrow);
        governance = _governance; // TODO: Maybe change to Oracle
        uint256 last_epoch = (block.timestamp / WEEK) * WEEK;
        time_sum = last_epoch;
    }
```

## [L-05] Potential Inaccurate Slope Calculation `VotingEscrow::_checkpoint` function

If the calculations of slopes, biases, and historical points are not synchronized accurately. This could lead to inaccurate voting power calculations and compromise the reliability of the contract's voting and locking mechanisms.

Expected Behavior:
The VotingEscrow contract's calculations, including slopes, biases, and historical data synchronization, should be accurate and consistent with the intended functionality. Users' voting power should be calculated correctly based on their locked balances and historical records.

Actual Behavior:
Due to the intricate nature of the `_checkpoint` function's calculations and potential interdependencies, there is a risk of inaccurate slope calculation and historical data synchronization. This could lead to voting power discrepancies, compromised historical data, and unexpected behaviors in the contract's functioning.

https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L115-L260

```solidity
    function _checkpoint(
        address _addr,
        LockedBalance memory _oldLocked,
        LockedBalance memory _newLocked
    ) internal {
        Point memory userOldPoint;
        Point memory userNewPoint;
        int128 oldSlopeDelta = 0;
        int128 newSlopeDelta = 0;
        uint256 epoch = globalEpoch;

        if (_addr != address(0)) {
            // Calculate slopes and biases
            // Kept at zero when they have to
            if (_oldLocked.end > block.timestamp && _oldLocked.delegated > 0) {
                userOldPoint.slope = _oldLocked.delegated / int128(int256(LOCKTIME));
                userOldPoint.bias = userOldPoint.slope * int128(int256(_oldLocked.end - block.timestamp));
            }
            if (_newLocked.end > block.timestamp && _newLocked.delegated > 0) {
                userNewPoint.slope = _newLocked.delegated / int128(int256(LOCKTIME));
                userNewPoint.bias = userNewPoint.slope * int128(int256(_newLocked.end - block.timestamp));
            }

            // Moved from bottom final if statement to resolve stack too deep err
            // start {
            // Now handle user history
            uint256 uEpoch = userPointEpoch[_addr];
            if (uEpoch == 0) {
                userPointHistory[_addr][uEpoch + 1] = userOldPoint;
            }

            userPointEpoch[_addr] = uEpoch + 1;
            userNewPoint.ts = block.timestamp;
            userNewPoint.blk = block.number;
            userPointHistory[_addr][uEpoch + 1] = userNewPoint;

            // } end

            // Read values of scheduled changes in the slope
            // oldLocked.end can be in the past and in the future
            // newLocked.end can ONLY by in the FUTURE unless everything expired: than zeros
            oldSlopeDelta = slopeChanges[_oldLocked.end];
            if (_newLocked.end != 0) {
                if (_newLocked.end == _oldLocked.end) {
                    newSlopeDelta = oldSlopeDelta;
                } else {
                    newSlopeDelta = slopeChanges[_newLocked.end];
                }
            }
        }

        Point memory lastPoint = Point({bias: 0, slope: 0, ts: block.timestamp, blk: block.number});
        if (epoch > 0) {
            lastPoint = pointHistory[epoch];
        }
        uint256 lastCheckpoint = lastPoint.ts;

        // initialLastPoint is used for extrapolation to calculate block number
        // (approximately, for *At methods) and save them
        // as we cannot figure that out exactly from inside the contract
        Point memory initialLastPoint = Point({bias: 0, slope: 0, ts: lastPoint.ts, blk: lastPoint.blk});
        uint256 blockSlope = 0; // dblock/dt
        if (block.timestamp > lastPoint.ts) {
            blockSlope = (MULTIPLIER * (block.number - lastPoint.blk)) / (block.timestamp - lastPoint.ts);
        }
        // If last point is already recorded in this block, slope=0
        // But that's ok b/c we know the block in such case

        // Go over weeks to fill history and calculate what the current point is
        uint256 iterativeTime = _floorToWeek(lastCheckpoint);
        for (uint256 i = 0; i < 255; i++) {
            // Hopefully it won't happen that this won't get used in 5 years!
            // If it does, users will be able to withdraw but vote weight will be broken
            iterativeTime = iterativeTime + WEEK;
            int128 dSlope = 0;
            if (iterativeTime > block.timestamp) {
                iterativeTime = block.timestamp;
            } else {
                dSlope = slopeChanges[iterativeTime];
            }
//...

```

## [L-06] Unchecked Loop in `LendingLedger::_checkpoint_lender` Function

The `_checkpoint_lender` function in the provided code contains a vulnerability that could potentially lead to a denial-of-service attack by exploiting an unchecked loop. This vulnerability arises due to the loop's potential to consume an excessive amount of gas when processing a large range of epochs, thus causing significant computational resource consumption and impacting the functionality of the contract.
In the `_checkpoint_lender` function, a loop is used to fill potential gaps in the user balances history for a given lending market and lender. The loop iterates over a range of epochs between the last user update epoch and the `_forwardTimestampLimit`.

However, there is no gas consumption control mechanism within the loop, which could be exploited by an attacker depositing a large value for `_forwardTimestampLimit`. This could span a substantial number of epochs, causing the loop to execute a significant number of times and leading to excessive gas consumption, potentially resulting in a denial-of-service attack.

https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L55-L78

```solidity
    function _checkpoint_lender(
        address _market,
        address _lender,
        uint256 _forwardTimestampLimit
    ) private {
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
