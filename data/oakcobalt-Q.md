### Low- 1. Unnecessary condition in _checkpoint()
In VotingEscrow.sol `_checkpoint()`, there is a if statement for `uEpoch==0` this code is not needed. This is because user history will start from `uEpoch + 1`. When `uEpoch==0`, userOldPoint will be 0. And there is no need to write to storge a zero value, and then re-write it in the same function after.

```solidity
//VotingEscrow.sol - _checkpoint()
...
            uint256 uEpoch = userPointEpoch[_addr];
|>          if (uEpoch == 0) {
                userPointHistory[_addr][uEpoch + 1] = userOldPoint;
            }

            userPointEpoch[_addr] = uEpoch + 1;
            userNewPoint.ts = block.timestamp;
            userNewPoint.blk = block.number;
            userPointHistory[_addr][uEpoch + 1] = userNewPoint;
```
Recommendation: 
Delete the if statement.