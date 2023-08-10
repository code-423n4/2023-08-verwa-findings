# Contract files should define a locked compiler version

It's crucial for contracts to be deployed using the same compiler version and settings with which they were extensively tested. By securing the pragma version, we can prevent inadvertent deployment with an older compiler version that could lead to bugs, adversely impacting the contract's functionality.

### Referenced sections:
- [GaugeController.sol Line 2](https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/GaugeController.sol#L2)
- [LendingLedger.sol Line 2](https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/LendingLedger.sol#L2)
- [VotingEscrow.sol Line 2](https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L2)
