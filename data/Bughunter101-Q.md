## `GaugeController._change_gauge_weight()` dose not check if `old_sum + _weight > old_gauge_weight`, it may cause DoS or cannot be changed afterwards

https://github.com/code-423n4/2023-08-verwa/blob/main/src/GaugeController.sol#L196

I suggest adding the check `old_sum + _weight > old_gauge_weight` and revert it

## `LendingLedger.whiteListLendingMarket()` did not check whether the market exists

https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L206
`LendingLedger.whiteListLendingMarket()` did not check whether the market exists.May cause some assignments to non-existing markets.

I suggest adding an array to hold the existing market and check it when calling the function

## It may cause `claim()` revert when the contract balance is insufficient or governance is suspended

According to the introduction, contract `LendingLedger` 's CANTO comes from governance
```
Canto governance calls setRewards and sends CANTO to the contract to control how much CANTO is allocated for one epoch.
```
However, when the CANTO in the contract is not enough and the governance is suspended, it will cause users to be unable to claim
I suggest adding fallbacks like depositing into weth to prevent this.When the CANTO in the contract is not enough and the governance is suspended, you can doposite the weth to user.

## `VotingEscrow._checkpoint()` function has an underflow problem caused by division first and then multiplication

As in the function implementation, `userOldPoint.slope` is calculated by `userOldPoint.slope = _oldLocked.delegated / int128(int256(LOCKTIME))`. And the `LOCKTIME = 5 years = 157 784 630 second` .This means that when `_oldLocked.delegated < int128(int256(LOCKTIME)`, the parameter `userOldPoint.slope` is equal to 0,and the `userOldPoint.bias` is also equal to 0 .This calculation is incorrect. The same problem also has parameter `userNewPoint.slope`.

I suggest that the principle of multiplying first and then dividing should be followed.
