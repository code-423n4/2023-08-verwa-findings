Lines [142-144](https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L142-L144) inside `VotingEscrow._checkpoint` are useless, since the assignment done there is being overwritten on line [149](https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L149):

```solidity
...
if (uEpoch == 0) {
    userPointHistory[_addr][uEpoch + 1] = userOldPoint;
}
...
userPointHistory[_addr][uEpoch + 1] = userNewPoint;
```

From a quick look at [Curve's original implementation](https://github.com/curvefi/curve-dao-contracts/blob/master/contracts/VotingEscrow.vy#L341-L347), `userOldPoint` does not need to be persisted, so my recommendation would be to remove lines 142-144 to avoid confusion.