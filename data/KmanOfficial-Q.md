[QA-1] "//See IVotingEscrow for documentation" comment should be removed and description of each function should be added as there isn't an interface for VotingEscrow in this project or IVotingEscrow.sol should be added and utilised.

As this was forked or inspired by the FIAT DAO repo, this comment was most likely from there. It should be  removed and replaced with the function descriptions for more clarity when reading through the contract.

Instances: 

https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L267
https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L286
https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L327
https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L360
https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L477
https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L491
https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L569
https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L576

------------------------------------------------------------------------------------

[QA-2] createLock has an unnecessary nonReentrant Modifier

https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L268

This function has no route or possibility for an arbitrary call to take place that would allow for an reentrancy attack so it is unnecessary despite it not affecting execution flow. It could be removed to save on unnecessary gas execution and for code principle.

