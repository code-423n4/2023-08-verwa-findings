# Non criticals
## [NC-01] Redundant code

CHECK => [LendingLedger.sol#L63](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L63)

CHECK => [LendingLedger.sol#L86](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L86)

Both lines are calculating `updateUntilEpoch` before the `if-else` clause which is just used on the `else` one. Consider moving that calculation to the `else` clause to be consistent with the expected "workflow" (that is, don't do unnecessary things) 