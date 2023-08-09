## Summary
G-01 Unnecessary checks.
G-02 Add `_gaude` validation.

### [G-01] Unnecessary checks.
In the functions `_get_sum` and `_get_weight` in `GaugeController` we have the same `if` statements in the `for` loop. One at the beginning and one at the end.

```solidity
if (t > block.timestamp)
```

The first one can be skipped and `break;` when in the second. This will save one iteration of the loop. Change the last line in the loop to:

```solidity
if (t > block.timestamp) {
    time_sum = t;
    break;
}
```

### [G-02] Add `_gaude` validation.
In the `gauge_relative_weight_write` function in `GaugeController` there is no validation if the gauge is existing. If there is a validation at the beginning, the rest of the code will be ignored. Add this to the first line of the function:

```solidity
require(isValidGauge[_gauge], "Invalid gauge address");
```