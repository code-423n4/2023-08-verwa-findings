1/ QUIT value in the LockAction enum is not used anywhere, so remove it. https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L64

2/ natspec incorrect : implementation shows lock becomes inactive either when lock.amount==0 OR lock.end<=block.timestamp (not AND) see https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L287

3/ We have 3 useless lines of code around https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L142-L144 : 
`if (uEpoch == 0) {
                userPointHistory[_addr][uEpoch + 1] = userOldPoint;
}`
remove them because this value is overwritten 4 lines after.