# Low
## Setting high CANTO rewards per epoch from malicious voters in governance

CANTO's governance participants can call the [LendingLedger.setRewards]((https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L188-L199)) function with very high CANTO rewards per epoch, which could lead to the distribution of an enormous amount of rewards. If [LendingLedger.setRewards]((https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L188-L199)) was called with incorrect rewards for a specific epoch, the changes cannot be reversed.
- [LendingLedger.sol#L188-L199](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L188-L199)
