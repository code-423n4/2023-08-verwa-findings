lendingMarketTotalBalance isn't updated for first call to `_checkpoint_market` in markets created in first epoch

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L87C9-L88C65

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L91

## Impact

This bricks the ability to update the market balance because when the function tries to update the lendingMarketTotalBalance it uses the value of `lastMarketBalance = lendingMarketTotalBalance[_market][lastMarketUpdateEpoch]` and since the value in `lendingMarketTotalBalance[_market][lastMarketUpdateEpoch]` is never updated in the first call to `checkpoint_market` for markets where `lastMarketUpdateEpoch == 0` this query will return 0 and subsequently always set the market’s total balance to 0.

Any calculation of rewards for users participating in a market will therefore cause a divide by 0 revert in `LendingLedger::claim` making users unable to receive rewards.

Anyone can call the `checkpoint_market` function to do this and only spend the gas costs associated with the function call.

## Proof of Concept

1. Alice calls `checkpoint_market` for a market created in the first epoch (within the span of the first week that protocol is deployed) that has never been checkpointed
2. Since the market has never been checkpointed the assignment on L85 gives `uint256 lastMarketUpdateEpoch = lendingMarketTotalBalanceEpoch[_market] = 0`
3. This triggers the if statement on L87: https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L87C9-L88C65 which sets `lendingMarketTotalBalanceEpoch[_market] = currEpoch`
4. When Alice calls `checkpoint_market` again `lastMarketUpdateEpoch` is then set to `currEpoch` from the previous call
5. Because the mapping `lendingMarketTotalBalance[_market][lastMarketUpdateEpoch]` was never updated for `currEpoch`, when `lastMarketBalance` is set on L91, it gets set to the default value of 0 https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L91:

```solidity
uint256 lastMarketBalance = lendingMarketTotalBalance[_market][lastMarketUpdateEpoch]

```

1. This results in the updates of `lendingMarketTotalBalance` for each epoch to all be set to 0 for each epoch up to `updateUntilEpoch` https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L92C13-L94C14
2. Anyone who then tries to call `checkpoint_market` to update the market balance will be able to do so but the balance for all subsequent epochs will be 0 set to 0.

## Tools Used

Manual Review

## Recommended Mitigation Steps

The if statement on L87 should be made to stand alone and the else-if on L89 should be removed so that the first call to `_checkpoint_market` also updates `lendingMarketTotalBalance`: