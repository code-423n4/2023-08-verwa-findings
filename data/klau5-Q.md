# Assign a value to a variable that already has the same value stored

## Impact

Assign a value to a variable that already has the same value stored

## Proof of Concept

In the `_checkpoint_lender` function, the `lastUserBalance` is already stored in `lendingMarketBalances[_market][_lender][lastUserUpdateEpoch]`.

Since `i` in the for-loop starts from `lastUserUpdateEpoch`, `lendingMarketBalances[_market][_lender][i] = lastUserBalance;` meas `lendingMarketBalances[_market][_lender][lastUserUpdateEpoch] = lastUserBalance;` 

This is meaningless because it’s trying to store the same value as the already stored value.

```solidity
uint256 lastUserBalance = lendingMarketBalances[_market][_lender][lastUserUpdateEpoch];
for (uint256 i = lastUserUpdateEpoch; i <= updateUntilEpoch; i += WEEK) {
    lendingMarketBalances[_market][_lender][i] = lastUserBalance;
}
```

[https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/LendingLedger.sol#L70-L73](https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/LendingLedger.sol#L70-L73)

Same issue happens at `_checkpoint_market` function.

```solidity
uint256 lastMarketBalance = lendingMarketTotalBalance[_market][lastMarketUpdateEpoch];
for (uint256 i = lastMarketUpdateEpoch; i <= updateUntilEpoch; i += WEEK) {
    lendingMarketTotalBalance[_market][i] = lastMarketBalance;
}
```

[https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/LendingLedger.sol#L91-L94](https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/LendingLedger.sol#L91-L94)

## Tools Used

vscode

## Recommended Mitigation Steps

Add `WEEK` when initializing `i`

- `for (uint256 i = lastUserUpdateEpoch; i <= updateUntilEpoch; i += WEEK) {` → `for (uint256 i = lastUserUpdateEpoch + WEEK; i <= updateUntilEpoch; i += WEEK) {`
- `for (uint256 i = lastMarketUpdateEpoch; i <= updateUntilEpoch; i += WEEK) {` → `for (uint256 i = lastMarketUpdateEpoch + WEEK; i <= updateUntilEpoch; i += WEEK) {`