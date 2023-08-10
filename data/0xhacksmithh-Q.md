### [Low-01] `checkpoint_market()` Contradicting With Logics Of `_checkpoint_market()`
function `checkpoint_market()` first verify `_forwardTimestampLimit` and then call `private` function `_checkpoint_market()`

Before making call it ensure that total balance locked in lending market during that epoch should greater than 0.
```solidity
    function checkpoint_market(address _market, uint256 _forwardTimestampLimit)
        external
        is_valid_epoch(_forwardTimestampLimit)
    {
        require(lendingMarketTotalBalanceEpoch[_market] > 0, "No deposits for this market"); 
        _checkpoint_market(_market, _forwardTimestampLimit);
    }
```

But `_checkpoint_market` has functionality to set `lendingMarketTotalBalanceEpoch` even when its 0

```solidity
function _checkpoint_market(address _market, uint256 _forwardTimestampLimit) private {
        uint256 currEpoch = (block.timestamp / WEEK) * WEEK;
        uint256 lastMarketUpdateEpoch = lendingMarketTotalBalanceEpoch[_market];
        uint256 updateUntilEpoch = Math.min(currEpoch, _forwardTimestampLimit);
        if (lastMarketUpdateEpoch == 0) {
            lendingMarketTotalBalanceEpoch[_market] = currEpoch;
        } else if (lastMarketUpdateEpoch < currEpoch) {
            // Fill in potential gaps in the market total balances history
            uint256 lastMarketBalance = lendingMarketTotalBalance[_market][lastMarketUpdateEpoch];
            for (uint256 i = lastMarketUpdateEpoch; i <= updateUntilEpoch; i += WEEK) {
                lendingMarketTotalBalance[_market][i] = lastMarketBalance;
            }
            if (updateUntilEpoch > lastMarketUpdateEpoch) {
                // Only update epoch when we actually checkpointed to avoid decreases
                lendingMarketTotalBalanceEpoch[_market] = updateUntilEpoch;
            }
        }
    }
```

So `checkpoint_market()` Should allow function call even when `lendingMarketTotalBalanceEpoch[_market] == 0`

Same `Above issue with` `checkpoint_lender()` and `_checkpoint_lender()`
And `checkpoint_lender()` should not fail when `lendingMarketBalancesEpoch[_market][_lender] == 0`

```
File: src/LendingLedger.sol
https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L117
https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L90-L92

https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L130
https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L66-L68
```