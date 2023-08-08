[L-1] loop unnecessarily does 1 calculation when i = lastUserUpdateEpoch

i is the same as lastUserUpdateEpoch, which is the retrieved value.
```solidity
            uint256 lastUserBalance = lendingMarketBalances[_market][_lender][lastUserUpdateEpoch];
            for (uint256 i = lastUserUpdateEpoch; i <= updateUntilEpoch; i += WEEK) {
                lendingMarketBalances[_market][_lender][i] = lastUserBalance;
            }
```

Recommendation
```solidity
            uint256 lastUserBalance = lendingMarketBalances[_market][_lender][lastUserUpdateEpoch];
+++             for (uint256 i = lastUserUpdateEpoch+1; i <= updateUntilEpoch; i += WEEK) {
---             for (uint256 i = lastUserUpdateEpoch; i <= updateUntilEpoch; i += WEEK) {
                lendingMarketBalances[_market][_lender][i] = lastUserBalance;
            }
```