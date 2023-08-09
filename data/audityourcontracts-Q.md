## [01] `setRewards()` should have an event and return a boolean for success/failure.
[`setRewards()`](https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L188) in [`LendingLedger.sol`](https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol) doesn't emit any events when the rewards are set. It's an important function but doesn't emit anything for users to follow or monitor. When I was testing I was able to flip _fromEpoch/_toEpoch and although the rewards are not set as someone calling the function you don't know whether it worked or failed.

## Recommendation
[`setRewards()`](https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L188) in [`LendingLedger.sol`](https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol) should have an event and return a boolean for success or failure.

## [02] `setRewards()` doesn't require _toEpoch > _fromEpoch 
[`setRewards()`](https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L188) in [`LendingLedger.sol`](https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol) checks that _fromEpoch and _toEpoch are valid epochs (`is_valid_epoch`) but doesn't check _toEpoch > _fromEpoch. 

## Recommendation
[`setRewards()`](https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L188) in [`LendingLedger.sol`](https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol) should `require(_toEpoch > _fromEpoch, "Invalid range: valid timestamps, wrong way round");`. [`claim()`](https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L152) has a similar issue. The concern here is some change is made to the functionality of `setRewards()` or `claim()` before deployment and an edge case bug slips in when this require/check would eliminate it.

## [03] `receive()` function allows CANTO/ETH to be sent directly to the contract
The `receive()` function allows anyone to send CANTO/ETH to the contract and if sent in error it cannot be retrieved.

## Recommendation
There could be a payable function like `transferRewards()` that could be payable and used by `onlyGovernance` to transfer CANTO/ETH to the contract. [`setRewards()`](https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L188) could be modified to do this but it has a different focus/purpose. The receive function can then removed and tests can be updated in [`LendingLedger.t.sol`](https://github.com/code-423n4/2023-08-verwa/blob/main/src/test/LendingLedger.t.sol) to remove the `transfer` and use `vm.deal`.

## [04] `_checkpoint()` iterates over 255 weeks when there are 260 weeks in 5 years
The [`_checkpoint()`](https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L538) function iterates over 255 weeks instead of 260 weeks (weeks in 5 years).

## Recommendation
This should be changed to `for (uint256 i = 0; i < 259; i++) {` to incorporate 260 weeks in 5 years.