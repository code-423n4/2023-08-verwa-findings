# Findings Summary

| ID     | Title                                        | Severity     |
| ------ | -------------------------------------------- | ------------ |
| [L-01] | The increaseAmount will refresh locked.end   | Low          |
| [N-01] | The Deposit event is triggered incorrectly   | Non-Critical |

# Detailed Findings

# [L-01] The increaseAmount will refresh newLocked.end

## Description

```solidity
    newLocked.amount += int128(int256(_value));
    newLocked.end = _floorToWeek(block.timestamp + LOCKTIME);
```

With a lockup period of up to 5 years for protocols, if increaseAmount refresh `locke.end` each time, this will greatly reduce the user's intention to do this in the mid to late period.    

## Recommendations

Don't refresh `locked.end` in increaseAmount

# [N-01] The Deposit event is triggered incorrectly

## Description

```solidity
        emit Deposit(delegatee, _value, newLocked.end, LockAction.DELEGATE, block.timestamp);
    }
    emit Deposit(msg.sender, _value, unlockTime, action, block.timestamp);
```

1. if delegatee != msg.sender, the Deposit event is triggered twice
2. For the outer Deposit event, unlockTime should be `newLocked.end`

## Recommendations

Fix the issues
 