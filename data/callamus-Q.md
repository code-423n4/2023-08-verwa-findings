1. Return values of  'LendingLedger.sol' are not checked.

In Solidity, it's crucial to check the return values because not all ERC20 token implementations revert on failure. Notably, example in claim function, If these methods fail silently and the contract doesn't verify their return value, the contract might continue execution as if the tokens were transferred successfully, leading to incorrect balances, loss of canto, or other unexpected behaviors. Therefore, the return values of these methods should always be checked, ensuring a failed canto transfer operation correctly halts the execution of the contract.

https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol
https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L179


Also in GaugeController.sol in the following functions,this functions modify states. It's important to check the return value to confirm that the state was updated as intended;

1.vote_for_gauge_weights - Allocates voting power for changing pool weights
https://github.com/code-423n4/2023-08-verwa/blob/main/src/GaugeController.sol#L211
2.add_gauge -adds gauges
https://github.com/code-423n4/2023-08-verwa/blob/main/src/GaugeController.sol#L118
3.remove_gauge -removes gauges
https://github.com/code-423n4/2023-08-verwa/blob/main/src/GaugeController.sol#L127

In votingescrow.sol also,this functions modify contract's states but have no return checks to ensure that the desired operation was successfully performed.;


1._checkpoints
https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L115
2. _increaseamount
https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L288C7-L288C7
3.withdraw
https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L326C17-L326C17
4.delegate
https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L356C18-L356C18
5_copylock 
https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L412
6.createlock
https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L268
