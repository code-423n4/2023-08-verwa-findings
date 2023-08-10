1. `LockedBalance.amount` can be `uint256`

[VotingEscrow.sol#L51](https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L51):
```solidity
struct LockedBalance {
    int128 amount;
    uint256 end; // when is the lock ending.
    int128 delegated;
    address delegatee;
}
```

Here `amount` is takeen as int128 and in its every usage it's either casted from or to uint256. Since `amount` takes up the full storage slot, the benefit of slot packing isn't applied here. Hence, by using uint256 you save gas, but also avoid the risk of overflowing or underflowing during typecasting down from uint256 to int128.