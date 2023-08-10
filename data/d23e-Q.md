### Lack of emergency pause [LendingLedger.sol]

There is a lack of a feature in the contract that allows for the pausing or stopping of certain functionalities in case of emergencies, such as the exploitation of a vulnerability by malicious actors, or regulatory requirements. In the case of an emergency or when an unknown vulnerability is discovered, this mechanism provides the ability to stop the contract from further operation thereby limiting potential damage.


### Unhandled failure for call in `claim()` [LendingLedger.sol]

The `claim()` function fails to handle the case where transferring the CANTO rewards fails [(ref. to code)](https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/LendingLedger.sol#L179-L180). If such failure happens, the whole transaction is reverted.  Using a low-level function like `address.call()` can lead to problems. If the recipient contract doesn’t have a fallback function defined, consumes more than the 2300 gas (the stipend given to a fallback function when `call.value()` is used) or throws an exception, the operation will fail and hence the whole transaction will revert.
```
To fix this problem, we should add some error handling code, such as :
if (cantoToSend > 0) {
    try {
        (bool success, ) = msg.sender.call{value: cantoToSend}("");
        require(success, "Failed to send CANTO");
    } catch {
        // Handle the failure accordingly
    }
}
```
Alternatively, a safer method for the value transfer such as the `transfer()` function or the `send()` function should be used instead of the `call()` function.

### Integer overflow/underflow in `sync_ledger()` [LendingLedger.sol]

The function `sync_ledger()` in this smart contract is vulnerable to an underflow attack ([ref. 1](https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/LendingLedger.sol#L135) and [ref. 2](https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/LendingLedger.sol#L140C37-L140C38)) in a very specific situation, for instance when the contract doesn't properly validate its inputs or there is a bug in the state of `lendingMarketBalances`. The vulnerability is present when the number used to initialize the balance is larger than what int256 can hold (this would not occur in a standard behaviour of the contract, but it may happen because of a bug or other kind of failure). Let's set `lendingMarketBalances[marketAddress][lenderAddress][currentEpoch]` to 1 plus the maximum positive value of an uint256, this would overflow in the casting and become negative. If `_delta` is also a large negative number, an underflow occurs, and then `updatedLenderBalance` becomes some extremely large positive (and unexpected) value greater than zero due to underflow. This would be also possible for `updatedMarketBalance`, where the same `require` statement is not sufficient.

### Duplicated assignment in `withdraw()` [VotingEscrow.sol]

The `newLocked.delegated` is  [duplicated](https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L337-L340). With a correct functioning of the code logic `newLocked.delegated` should be equal to 0 in any case.

### Lack of sanity check for zero address delegation in `delegate()` [VotingEscrow.sol]

In the [`delegate(address _addr)`](https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L356) function, there is no sanity check to ensure that the address being delegated to is not the zero address, potentially leading to the loss of a user's voting power.

### Transaction order dependency vulnerability [VotingEscrow.sol]
Transactions that contain some function calls such as [`withdraw()`](https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L326C14-L326C22), [`increaseAmount()`](https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L288), [`delegate()`](https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L356) are vulnerable to transaction order dependency. If multiple transactions are sent by an user calling some of the different functions stated before, the miner of the block can change their order of execution and that can lead to unexpected results to the user, such as amounts delegation of higher or lower amounts. To fix this, nonces can be added to enforce that the transactions corresponding to a single user are executed in order.

### Inaccuracies in timestamps and timing of locks [VotingEscrow.sol]

The smart contract uses `block.timestamp` to manage the timing of lock and unlock events. This could be manipulated by a miner to result in inaccurate timer setting. The miner has a certain degree of freedom in manipulating the block.timestamp (and effects can be noticeable in the change of a week, for instance, where the miner may choose if the timestamp corresponds to the previous or next week). In any case, this vulnerability may not be critically harmful as the degree of freedom is limited. As recommendation, we should avoid relying solely on block.timestamp for time-keeping, and instead use multiple sources, or rather the block number.
