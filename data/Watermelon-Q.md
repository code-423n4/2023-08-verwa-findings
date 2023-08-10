## Title
Unsafe Integer Cast Causes Incorrect `amount` and `delegated`

## Locations
* https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L276
* https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L278
* https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L300
* https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L305
* https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L317

## Description
Type casting refers to changing a variable of one data type into another. The code contains an unsafe cast between integer types, which may result in unexpected truncation or sign flipping of the value. For example in the `createLock()` function, if the `_value` exceeds the maximum value of type(int128), which is `2**127 - 1`, the accounting will become inaccurate. Consequently, the amount added to `locked_.amount` will be less than the actual token amount transferred from the user. As a result, the user won't be able to withdraw the tokens they transferred, leading to their tokens being trapped within the contract indefinitely.

## Scenario
1. Bob attempts to lock `2**128` tokens. He invokes `createLock(2**128, unlockTime)`.
2. However, this operation does not affect the `locked.amount` variable; it remains at 0. This is due to the fact that the casting int128(int256(2**128)) results in truncation to 0. Consequently, the locked amount remains unaffected despite the tokens being transferred. 

## Recommendation
It is recommended to check the bounds of integer values before casting. Alternatively, consider using the `SafeCast` library from OpenZeppelin to perform safe type casting and prevent undesired behavior.

Reference: https://github.com/OpenZeppelin/openzeppelin-contracts/blob/cf86fd9962701396457e50ab0d6cc78aa29a5ebc/contracts/utils/math/SafeCast.sol