## Title: Low -  Incorrect require variable in checkpoint_lender

## Github Links

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L114-L124

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L51-L78

## Impact

The `checkpoint_lender` function has a require statement that checks that the lender has deposits >0 in this market. 
However, the variable used in the require statement, `lendingMarketBalancesEpoch[_market][_lender]`, returns the Epoch when the last update happened, **not** the balance of the user in this market. 


## Tools Used

Manual review

## Recommendations

Change the require statement:
```diff
- require(lendingMarketBalancesEpoch[_market][_lender] > 0, "No deposits for this lender in this market");
+ require(lendingMarketBalances[_market][_lender][_forwardTimestampLimit] > 0, "No deposits for this lender in this market");;
```



