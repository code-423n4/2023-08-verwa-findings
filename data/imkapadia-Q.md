## Description
`LendingLedger` has a receive() function, but does not have any withdrawal function. Any Manifest mistakenly sent to this contract would be locked.

## Line of code affected
https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L209

## Recommendation
1. Add a withdraw functionality in a smart contract or
2. Write a `receive()` in a way that send the fund to actual sender (i.e., `msg.sender`) immediately or
3. Remove the `receive()`.