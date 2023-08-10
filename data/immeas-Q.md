# QA report

## Summary

| id | title |
| --- | --- |
| [L-01](#l-01-governance-cannot-be-changed) | `governance` cannot be changed |
| [NC-01](#nc-01-weird-formatting) | weird formatting |
| [NC-02](#nc-02-natspec-not-properly-highlighted) | natspec not properly highlighted |

## Low

### L-01 `governance` cannot be changed

In both [`LendingLedger`](https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L13) and [`GaugeController`](https://github.com/code-423n4/2023-08-verwa/blob/main/src/GaugeController.sol#L25) there is a privileged role, `governance`, that can perform certain functions. This role is written upon construction. The issue is that it cannot be changed. There are plenty of reasons this could be needed, the project might want to move their governance to a new multisig/timelock or from a EOA to multisig or from a multisig to a timelock.

#### Recommendation
Consider adding a way to change `governance`, preferably a two step process.

## Non critical / Informational

### NC-01 weird formatting

https://github.com/code-423n4/2023-08-verwa/blob/main/src/GaugeController.sol#L215-L220
```solidity
File: GaugeController.sol

215:        (
216:            ,
217:            /*int128 bias*/
218:            int128 slope_, /*uint256 ts*/
219:
220:        ) = ve.getLastUserPoint(msg.sender);
```

This is weirdly formatted which makes it hard to read, consider condensing it and either move the comments to where the variables would have been or remove them:

```solidity
        ( , int128 slope_, ) = ve.getLastUserPoint(msg.sender);
```
or
```solidity
        VotingEscrow ve = votingEscrow;
        (
        	/*int128 bias*/,
            int128 slope_,
            /*uint256 ts*/
        ) = ve.getLastUserPoint(msg.sender);
```

This makes it easier to read.

### NC-02 natspec not properly highlighted

In `VotingEscrow.sol` there are some natspec comments that lack an extra `/` with makes them not highight properly:

```solidity
File: VotingEscrow.sol

267:    // See IVotingEscrow for documentation

286:    // See IVotingEscrow for documentation
287:    // @dev A lock is active until both lock.amount==0 and lock.end<=block.timestamp

422.    // @dev Floors a timestamp to the nearest weekly increment
423:    // @param _t Timestamp to floor

428:    // @dev Uses binarysearch to find the most recent point history preceeding block
429:    // @param _block Find the most recent point history before this block
430:    // @param _maxEpoch Do not search pointHistories past this index

448:    // @dev Uses binarysearch to find the most recent user point history preceeding block
449:    // @param _addr User for which to search
450:    // @param _block Find the most recent point history before this block
```

Consider adding an extra `/` to add proper highlighting to the natspec.
