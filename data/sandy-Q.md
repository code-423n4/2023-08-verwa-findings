# Summary

## Low Risk Issues
|Id|Title| Instances
|:--:|:-------|:-----:
|L-01| ``TODO`` should be implemented. | 2
|L-02| Provide proper documentation | 8


## [L-01] ``TODO`` should be implemented.
Instances:
1. https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L48C1-L48C66
2. https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L59

There is ``TODO`` in the constructor for changing the ``governance`` address to Oracle but it has not been implemented in both instances. It is better to implement these changes.

## [L-02] Provide proper documentation.
In the ``src/VotingEscrow.sol`` there are several instances of 
```
// See IVotingEscrow for documentation
``` 
before many functions but there is no ``IVotingEscrow`` provided for this audit. Hence, most of this functions are audited without proper documentation resulting in auditors getting less context of the functions. Provide proper documentation for a better audit.

Instances:
1. https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L267
2. https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L286
3. https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L325
4. https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L355
5. https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L472
6. https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L564
7. https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L571
8. https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L486

## Non-Critical Issues
|Id|Title| Instances
|:--:|:-------|:-----:
|NC-01| Unused import | 1

## [NC-01] Unused import
https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L4

The ``VotingEscrow`` import in ``src/LendingLedger.sol`` is unused throughout the whole contract and can be removed.
