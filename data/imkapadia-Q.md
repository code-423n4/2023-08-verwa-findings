# [L-01] Missing functionality to withdraw funds that sent mistakenly
## Description
`LendingLedger` has a receive() function, but does not have any withdrawal function. Any Manifest mistakenly sent to this contract would be locked.

## Line of code affected
https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L209

## Recommendation
1. Add a withdraw functionality in a smart contract or
2. Write a `receive()` in a way that send the fund to actual sender (i.e., `msg.sender`) immediately or
3. Remove the `receive()`.

# [L-02] Missing functionality to verify voting power
## Description
`VotingEscrow` contract lacks any mechanism to verify the voting power of users participating in voting activities. Without the ability to confirm the voting power of users, the contract is susceptible to potential abuse and manipulation, as users may claim false voting power, leading to biased voting outcomes.

## Lines of code affected
https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol

## Recommendation
Implement a algorithm within the contract that accurately calculates and verifies the voting power of each user based on established criteria.

# [NC-01] Unused imports

## Description
The following source units are imported but not referenced in the contract:

**LendingLedger.sol:**
```solidity
import {VotingEscrow} from "./VotingEscrow.sol";
```

## Recommendation
Consider removing this imports.