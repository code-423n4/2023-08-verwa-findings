
## QA-1 - Unused enum options
Two of the enum options in `LockAction` are unused and can be removed.
```
enum LockAction {
    CREATE,
    INCREASE_AMOUNT,
    INCREASE_AMOUNT_AND_DELEGATION,
    INCREASE_TIME, // @audit-issue qa unused
    WITHDRAW,
    QUIT, //@audit-issue qa unused
    DELEGATE,
    UNDELEGATE
}
```

## QA-2 - `VotingEscrow#decimals` can be constant
`VotingEscrow#decimals` is currently a storage variable hardcoded to 18. It can be made constant.

## QA-3 - Missing events
Several key functions are missing event emissions:
- `GaugeController#_change_gauge_weight()`
- `GaugeController#vote_for_gauge_weights()`
- All functions in `LendingLedger`
    - `sync_ledger()`
    - `claim()`
    - `whitelistLendingMarket()`
    - `setRewards()`
- all `_checkpoint()` functions (discretionary but would recommend adding)

## QA-4 - `GaugeController#last_user_vote` is unnecessary 
This mapping can be removed as it was only used for the voting delay in the original Curve contract, which was not included in this implementation.
- `GaugeController#L277`: `last_user_vote[msg.sender][_gauge_addr] = block.timestamp;`

## QA-5 - Small (dust) amounts of stake will be locked for the full `LOCKTIME` and not counted towards voting 
Lock amounts smaller than LOCKTIME will round down to 0 in the following line and will result in a slope and bias of 0:
`VotingEscrow#134-135`:
```solidity
userNewPoint.slope = _newLocked.delegated / int128(int256(LOCKTIME));
userNewPoint.bias = userNewPoint.slope * int128(int256(_newLocked.end - block.timestamp));
```

## QA-6 Unsafe casting
There are many instances where a value or timestamp is casted from `int128(int256(uint256))`, which can overflow for large values. This is low risk as the current max supply of CANTO is 1 billion and timestamps will not be greater than uint32.max for many years.

## QA-7 There is no guarantee that the `LendingLedger` has enough rewards to pay out what governance sets
`LendingLedgers#setRewards()` allows governance to set the amount of rewards to be distributed for an epoch. However, it is not enforced that the contract actually has enough funds to match the distribution configuration.

## QA-8 Code simplification
`LendingLedger#constructor`:
```
constructor(address _votingEscrow, address _governance) {
    votingEscrow = VotingEscrow(_votingEscrow);
    governance = _governance; // TODO: Maybe change to Oracle

    //@audit-issue qa time_sum can just be assigned directly here
    uint256 last_epoch = (block.timestamp / WEEK) * WEEK;
    time_sum = last_epoch; 
}
```
