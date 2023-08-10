#### `_get_sum` call at end of `GaugeController.vote_for_gauge_weight` is a noop

`_get_sum` only runs a loop iteration if `time_sum` <= block.timestamp. 

```solidity
// _get_sum()
uint256 t = time_sum;
Point memory pt = points_sum[t];
for (uint256 i; i < 500; ++i) {
	if (t > block.timestamp) break;
```

And it the return value is unused.

```solidity
// vote_for_gauge_weight
_get_sum(); // @audit: unused return value
```

Since we executed `_get_sum` earlier during the function execution, we set `time_sum` as > block.timestamp.

```solidity
// _get_sum
if (t > block.timestamp) time_sum = t;
```

As a result, calling `_get_sum` [here](https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/GaugeController.sol#L272) is a noop.


#### Intra-epoch lock timing can result in +/- 1 epoch of rewards

`LOCKTIME` is 1825 days, which amounts to 260.7142857142857 epochs.

```solidity
// VotingEscrow.sol
uint256 public constant LOCKTIME = 1825 days;
```

Creating a lock sets your unlock time as the `_floorToWeek` value of 1825 days from the current timestamp. 

```solidity
// createLock
uint256 unlock_time = _floorToWeek(block.timestamp + LOCKTIME); // Locktime is rounded down to weeks
```

As a result, creating a lock in the last ~30% of an epoch results in an extra epoch considering to the first ~70%.

Increasing the locktime to 1827 days would be cleanly divisible by epochs, resulting in the same amount of epochs regardless of lock timing. This is also still well aligned with 5 years when you consider leap years, since any 5 year period has at least one leap year, if not two, 5 years would be at least 1826 days, if not 1827 depending on timing.