# QA Report

| QA     | Issues                                                                                          | Instances |
|--------|-------------------------------------------------------------------------------------------------|-----------|
| [N-01] | Public variables only used within contract can be changed to private visibility                 | 30        |
| [N-02] | Variables that are unchanging should be marked constant or immutable if assigned in constructor | 7         |
| [N-03] | Missing event emission for critical storage changes in functions                                | 11        |
| [N-04] | Remove redundant code and comments to improve code readability and maintainability              | 6         |
| [N-05] | Consider using delete instead of assigning address(0) to clear values                           | 1         |
| [N-06] | Require statements should provide an error message in case condition fails                      | 2         |
| [N-07] | Modify code to improve code readability and maintainability                                     | 1         |
| [N-08] | Return values not handled in functions                                                          | 5         |
| [N-09] | Duplicated require/if statements should be refactored                                           | 1         |
| [L-01] | Require check incorrectly implemented                                                           | 1         |
| [L-02] | Loss of precision due to division occurring before multiplication                               | 9         |
| [L-03] | Slope can round down to zero if numerator is smaller than denominator                           | 1         |
| [L-04] | LOCKTIME does not consider for 2 or more extra days introduced due to leap years                | 1         |
| [L-05] | Consider skipping the epoch for which rewards are already set                                   | 1         |

**Total Non-Critical issues: 64 instances across 9 issues**
**Total Low-Severity issues: 13 instances across 5 issues**
**Total issues: 77 instances across 14 issues**

## [N-01] Public variables only used within contract can be changed to private visibility

Public variables that are only used within the contract can be marked private.
There are 30 instances of this:

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L26C1-L28C34

There are 3 instances here:
```solidity
File: src/VotingEscrow.sol
26:     string public name;
27:     string public symbol;
28:     uint256 public decimals = 18;
```

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L36C1-L41C53

There are 6 instances here:
```solidity
File: src/VotingEscrow.sol
36:     uint256 public globalEpoch;
37:     Point[1000000000000000000] public pointHistory; // 1e9 * userPointHistory-length, so sufficient for 1e9 users
38:     mapping(address => Point[1000000000]) public userPointHistory;
39:     mapping(address => uint256) public userPointEpoch;
40:     mapping(uint256 => int128) public slopeChanges;
41:     mapping(address => LockedBalance) public locked;
```

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L13C1-L27C1
https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L32

There are 9 instances here:
```solidity
File: src/LendingLedger.sol
13:     address public governance;
14:     GaugeController public gaugeController;
15:     mapping(address => bool) public lendingMarketWhitelist;
16:     /// @dev Lending Market => Lender => Epoch => Balance
17:     mapping(address => mapping(address => mapping(uint256 => uint256))) public lendingMarketBalances; // cNote balances of users within the lending markets, indexed by epoch
18:     /// @dev Lending Market => Lender => Epoch
19:     mapping(address => mapping(address => uint256)) public lendingMarketBalancesEpoch; // Epoch when the last update happened
20:     /// @dev Lending Market => Epoch => Balance
21:     mapping(address => mapping(uint256 => uint256)) public lendingMarketTotalBalance; // Total balance locked within the market, i.e. sum of lendingMarketBalances for all
22:     /// @dev Lending Market => Epoch
23:     mapping(address => uint256) public lendingMarketTotalBalanceEpoch; // Epoch when the last update happened
24: 
25:     /// @dev Lending Market => Lender => Epoch
26:     mapping(address => mapping(address => uint256)) public userClaimedEpoch; // Until which epoch a user has claimed for a particular market (exclusive this value)
32:     mapping(uint256 => RewardInformation) public rewardInformation;
```

https://github.com/code-423n4/2023-08-verwa/blob/9a2e7be003bc1a77b3b87db31f3d5a1bcb48ed32/src/GaugeController.sol#L24C1-L38C1

There are 12 instances here:
```solidity
File: src/GaugeController.sol
24:     VotingEscrow public votingEscrow;
25:     address public governance;
26:     mapping(address => bool) public isValidGauge;
27:     mapping(address => mapping(address => VotedSlope)) public vote_user_slopes;
28:     mapping(address => uint256) public vote_user_power;
29:     mapping(address => mapping(address => uint256)) public last_user_vote;
30: 
31:     mapping(address => mapping(uint256 => Point)) public points_weight;
32:     mapping(address => mapping(uint256 => uint256)) public changes_weight;
33:     mapping(address => uint256) time_weight;
34: 
35:     mapping(uint256 => Point) points_sum;
36:     mapping(uint256 => uint256) changes_sum;
37:     uint256 public time_sum;
```

## [N-02] Variables that are unchanging should be marked constant or immutable if assigned in constructor

Variables than do not change can be marked constant and those that do not change after assignment during construction time can be marked immutable
There are 7 instances of this:

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L26C1-L28C34

There are 3 instances here:
Vsriables name and symbol can be marked immutable and decimals can be marked constant.
```solidity
File: src/VotingEscrow.sol
26:     string public name;
27:     string public symbol;
28:     uint256 public decimals = 18;
```

https://github.com/code-423n4/2023-08-verwa/blob/9a2e7be003bc1a77b3b87db31f3d5a1bcb48ed32/src/GaugeController.sol#L24C1-L25C31

There are 2 instances here:
Variables can be made immutable since they do not change after construction time.
```solidity
File: src/GaugeController.sol
24:     VotingEscrow public votingEscrow;
25:     address public governance;
```

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L13C1-L15C1

There are 2 instances here:
Variables can be made immutable since they do not change after construction time.
```solidity
File: src/LendingLedger.sol
13:     address public governance;
14:     GaugeController public gaugeController;
```

## [N-03] Missing event emission for critical storage changes in functions

Functions that make storage updates or critical configuration updates are missing events and/or their emissions.
There are 11 instances of this:

1. [_checkPoint()](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L115)
2. [ _checkpoint_lender()](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L55)
3. [_checkpoint_market()](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L83)
4. [checkpoint_market()](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L106)
5. [checkpoint_lender()](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L117)
6. [sync_ledger()](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L129)
7. [claim()](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L152)
8. [setRewards()](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L188)
9. [whiteListLendingMarket()](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L204)
10. [_change_gauge_weight()](https://github.com/code-423n4/2023-08-verwa/blob/9a2e7be003bc1a77b3b87db31f3d5a1bcb48ed32/src/GaugeController.sol#L188)
11. [vote_for_gauge_weights()](https://github.com/code-423n4/2023-08-verwa/blob/9a2e7be003bc1a77b3b87db31f3d5a1bcb48ed32/src/GaugeController.sol#L211C14-L211C36)

## [N-04] Remove redundant code and comments to improve code readability and maintainability

Redundant code logic and comments serving no purpose in the codebase can be removed to improve code readability and maintainability.

There are 6 instances of this:

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L212

We do not need to check if `_user_weight >= 0` in the require statement below since the input `_user_weight` is taken as an uint256 parameter, thus it will always be greater than or equal to 0. If the function is called with `_user_weight` less than zero, the function reverts automatically due to it not being an uint256. Thus, the first check can be removed.
```solidity
File: src/GaugeController.sol
212: require(_user_weight >= 0 && _user_weight <= 10_000, "Invalid user weight");
```

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L142C1-L144C14

The if block below can be removed since Line 143 is being overwritten on [Line 149 here](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L149). The uEpoch is 0 only when the user is creating a lock for the first time. Thus, since it is being overwritten anyway, the if block can be removed.
```solidity
File: src/VotingEscrow.sol
142:             if (uEpoch == 0) {
143:                 userPointHistory[_addr][uEpoch + 1] = userOldPoint;
144:             }
```

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L340

Line 340 below can be removed since it serves no purpose in the code. It is not related to the call to [_checkPoint() on Line 344](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L344) as well.
```solidity
File: src/VotingEscrow.sol
340:  newLocked.delegated = 0;
```
Note: Although `_checkpoint` theoretically reads it below, `_newLocked.end > block.timestamp` is never true in this case (as the end is 0 since it was [updated in the withdraw() function](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L336)), thus should be safe to remove
```
            if (_newLocked.end > block.timestamp && _newLocked.delegated > 0) {
                userNewPoint.slope = _newLocked.delegated / int128(int256(LOCKTIME));
                userNewPoint.bias = userNewPoint.slope * int128(int256(_newLocked.end - block.timestamp));
            }
```

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L48

TODO statement should be solved and removed.
```solidity
File: src/LendingLedger.sol
48: governance = _governance; // TODO: Maybe change to Oracle
```

https://github.com/code-423n4/2023-08-verwa/blob/9a2e7be003bc1a77b3b87db31f3d5a1bcb48ed32/src/GaugeController.sol#L59

TODO statement should be solved and removed.
```solidity
File: src/GaugeController.sol
59: governance = _governance; // TODO: Maybe change to Oracle
```

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L4

Import can be removed since it is not used anywhere in the LendingLedger.sol contract.
```solidity
File: src/LendingLedger.sol
4: import {VotingEscrow} from "./VotingEscrow.sol";
```

## [N-05] Consider using delete instead of assigning address(0) to clear values

**(Note: This instance is missing in the [[N-31] bot finding](https://gist.github.com/code423n4/b1a7bd4b13a4b0c1a62e25dc56eb6dac#nc-31-consider-using-delete-instead-of-assigning-zerofalse-to-clear-values))**

The delete keyword more closely matches the semantics of what is being done (deletion of lock), and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic.

There is 1 instance of this:

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L338

```solidity
File: src/VotingEscrow.sol
338: newLocked.delegatee = address(0);
```

## [N-06] Require statements should provide an error message in case condition fails

An error message should be added to provide meaning on failure of condition in require statements.

There are 2 instances of this:

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L42

A short error message like `!auth` should be provided for clarity on failure.
```solidity
File: src/LendingLedger.sol
42: require(msg.sender == governance);
```

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L51

A short error message like `!auth` should be provided for clarity on failure.
```solidity
File: src/GaugeController.sol
51: require(msg.sender == governance);
```

## [N-07] Modify code to improve code readability and maintainability

Code should be refactored to improve code readability and maintainability. This removes any additional redundant code as well.

There is 1 instance of this issue:

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L60C1-L62C1

Instead of this:
```solidity
File: src/GaugeController.sol
60:         uint256 last_epoch = (block.timestamp / WEEK) * WEEK;
61:         time_sum = last_epoch;
```
Use this:
```solidity
File: src/GaugeController.sol
60:         time_sum = (block.timestamp / WEEK) * WEEK;
```

## [N-08] Return values not handled in functions

There are 5 instances here:

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L180C1-L181C20

The _get_weight() and _get_sum() functions return uint256 values. But they are not handled here.
There are 2 instances here:
```solidity
File: src/GaugeController.sol
180:         _get_weight(_gauge);
181:         _get_sum();
```

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L136

_get_sum() returns an uint256 value, which is not handled here.
There is 1 instance here:
```solidity
File: src/GaugeController.sol
136: _get_sum();
```

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L142C1-L143C20

The _get_weight() and _get_sum() functions return uint256 values. But they are not handled here.
There are 2 instances here:
```solidity
File: src/GaugeController.sol
142:         _get_weight(_gauge);
143:         _get_sum();
```

## [N-09] Duplicated require/if statements should be refactored

**(Note: The instance below is not included in the [[N-10] bot finding](https://gist.github.com/code423n4/b1a7bd4b13a4b0c1a62e25dc56eb6dac#nc-10-duplicated-requireif-statements-should-be-refactored))**

There is 1 instance of this:

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L128
https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L213C69-L213C69
```solidity
File: src/GaugeController.sol
128: require(isValidGauge[_gauge], "Invalid gauge address"); //@audit Duplicate on Line 213
213: require(isValidGauge[_gauge_addr], "Invalid gauge address");
```

## [L-01] Require check incorrectly implemented

There is 1 instance of this issue:

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L294

The check below is implemented as follows with the `>` operator:
```solidity
File: src/VotingEscrow.sol
294: require(locked_.end > block.timestamp, "Lock expired");
```
But the [`@dev` tag on Line 287](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L287) mentions it should use `>=` operator:
```solidity
File: src/VotingEscrow.sol
287: // @dev A lock is active until both lock.amount==0 and lock.end<=block.timestamp
```
Solution:
```solidity
File: src/VotingEscrow.sol
294: require(locked_.end >= block.timestamp, "Lock expired");
```

## [L-02] Loss of precision due to division occurring before multiplication

**(Note: This is different from the [[L-08] bot finding](https://gist.github.com/code423n4/b1a7bd4b13a4b0c1a62e25dc56eb6dac#l-08-loss-of-precision-on-division)**

Performing multiplication before division is generally better to avoid loss of precision because Solidity integer division might truncate. See [here](https://github.com/crytic/slither/wiki/Detector-Documentation#divide-before-multiply)

Most of the instances below are timing-related, thus it is important to ensure that any operations that determine time are as precise as possible so that any reads and writes from/to storage are synced and correct.
There are 9 instances of this:

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L425
```solidity
File: src/VotingEscrow.sol
425: return (_t / WEEK) * WEEK;
```

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L60C10-L60C10
```solidity
File: src/GaugeController.sol
60: uint256 last_epoch = (block.timestamp / WEEK) * WEEK;
```

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L153
```solidity
File: src/GaugeController.sol
153: uint256 t = (_time / WEEK) * WEEK;
```

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L191
```solidity
File: src/GaugeController.sol
191: uint256 next_time = ((block.timestamp + WEEK) / WEEK) * WEEK;
```

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L224
```solidity
File: src/GaugeController.sol
224: uint256 next_time = ((block.timestamp + WEEK) / WEEK) * WEEK;
```

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L60
```solidity
File: src/LendingLedger.sol
60: uint256 currEpoch = (block.timestamp / WEEK) * WEEK;
```

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L84
```solidity
File: src/LendingLedger.sol
84: uint256 currEpoch = (block.timestamp / WEEK) * WEEK;
```

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L134
```solidity
File: src/LendingLedger.sol
134: uint256 currEpoch = (block.timestamp / WEEK) * WEEK;
```

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L162
```solidity
File: src/LendingLedger.sol
162: uint256 currEpoch = (block.timestamp / WEEK) * WEEK;
```

## [L-03] Slope can round down to zero if numerator is smaller than denominator

**(Note: The instance below is not included in the [[L-08] bot finding](https://gist.github.com/code423n4/b1a7bd4b13a4b0c1a62e25dc56eb6dac#l-08-loss-of-precision-on-division))**

Solidity doesn't support fractions, so divisions by large numbers could result in the quotient being zero. To avoid this, it's recommended to require a minimum numerator amount to ensure that it is always greater than the denominator.

There is 1 instance of this:

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L231

If `slope * _user_weight` is less than 10_000, it can lead to rounding to zero and incorrect evaluations in the statements following it.
```solidity
File: src/GaugeController.sol
231:  slope: (slope * _user_weight) / 10_000,
```

## [L-04] LOCKTIME does not consider for 2 or more extra days introduced due to leap years

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L32

The current LOCKTIME considers that all 5 years are 365 days long. We could let this by if there was only leap year that the lock comes across during its lifespan of 5 years. But that is not the case here. 

Let's take three examples:
1. Suppose a lock is created in 2024, the end time for it would be 2029. Since 2024 to 2029 includes two leap years (2024 and 2028), this adds 2 extra days. But since the locktime considers each year to be 365 days long, we skip these 2 extra days. (Note: It is highly likely such an issue occurs as 2024 is next year and with the launch of this fresh codebase, there are high chances of locks being created in 2024).

2. Suppose a lock is created in 2023, the end time for it would be 2028. Since 2023 to 2028 includes one leap year (2024, the lock ends at start of 2028). this adds 1 extra day. We can let this by but if the lock is extended by increaseAmount() function, the introduction of a second leap year (2028) is possible. In that case, two extra days are not added to the lifespan of the lock. 

3. If any lock created is extended with increaseAmount() function, that will introduce an additional leap year (i.e. an extra day) which is not accounted for in the lifespan of the contract. This adds up if the user keeps on extending their lock.

Solution:
If you go to see technically, each year is 365 days and 6 hours long. That is why we have a leap year every 4 years (6 * 4 = 24 hours = 1 day) since the 6 hours are accounted in the leap year. The solution to this problem would be to change the LOCKTIME from 1825 days to 1828 days. This is because 1825/5 = 365 days but 1828/5 = 365.6 (i.e. 1828 days accounts for the 6 hours in each year, thus now making each year 365 days and 6 hours long rather than 365 days only). Note that there is no rounding error occurring here since when creating a lock, we are just adding the days to block.timestamp, which is then rounded down to weeks.  
```solidity
File: src/VotingEscrow.sol
32: uint256 public constant LOCKTIME = 1825 days; //@audit make this 1828 days 
```

## [L-05] Consider skipping the epoch for which rewards are already set

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L195

Here is the discussion between the sponsor and I to gain some context before reading my solution:

Warden:
```
Hi, over here do you think it would make sense to just "continue" the for loop rather than revert if rewards are already set? That way it might be easy for the governance to set the rewards as well I think.
https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L195
```

Sponsor:
```
I revert there on purpose because if there is a governance proposal to set rewards for epochs X to X + Y, I think it should either set it for all epochs or fail (and require a new one). Only setting it for a few could be a bit confusing imo, but would of course also work
```

Warden:
```
hmm what I was thinking is if we set rewards for epochs X to X + Y, there might be an epoch X + 1 which might have rewards already set and there is no way to set it's ri.set to false. Therefore the governance might need to set the rewards as X to X and X + 2 to X + Y to go around X + 1.  I think it is highly unlikely something like this would occur but the case is still valid I believe. Lmk what you think about this.
```

Sponsor:
```
Yes exactly, in such a scenario I would prefer two separate proposals with [X, X] and [X + 2, X + Y] because I think the danger with one proposal for [X, X + Y] is that voters expect that the rewards will be set for the whole range
```

Warden:
```
hmm your right that makes sense from the voters perspective for ease of understandability and I believe is the way to go. Just had an additional point that if there is more than 1 epoch for which rewards are set then I believe it might be time consuming and a bit heavy work-wise on the voter's side. Again the likelihood for this is low.
```

Solution:

If ri.set is false it can lead to reversion of all previous storage updates. This can prevent the governance from setting rewards per epoch in the range fromEpoch to toEpoch. Although this reversion is the desired behaviour (i.e. what the sponsor expects - discussed with sponsor) and can be solved with two separate proposals to go around the epoch (rewards for which are already set), I think it might be time consuming and a bit heavy work-wise on the governance's side if there is more than 1 epoch for which rewards are already set. That is why I believe using the continue keyword is much better for ease of operation. Additionally, an event can be emitted for an epoch for which rewards are already set in order to make the governance aware of them. **(Note: This event emission is necessary as voters are expecting that the rewards will be set for the whole range - as mentioned by the sponsor above)**

Instead of this:
```solidity
File: src/LendingLedger.sol
188:     function setRewards(
189:         uint256 _fromEpoch,
190:         uint256 _toEpoch,
191:         uint248 _amountPerEpoch
192:     ) external is_valid_epoch(_fromEpoch) is_valid_epoch(_toEpoch) onlyGovernance {
193:         for (uint256 i = _fromEpoch; i <= _toEpoch; i += WEEK) {
194:             RewardInformation storage ri = rewardInformation[i];
195:             require(!ri.set, "Rewards already set");
196:             ri.set = true;
197:             ri.amount = _amountPerEpoch;
198:         }
199:     }
```
Use this (changes made on lines 195,196,197,198):
```solidity
File: src/LendingLedger.sol
188:     function setRewards(
189:         uint256 _fromEpoch,
190:         uint256 _toEpoch,
191:         uint248 _amountPerEpoch
192:     ) external is_valid_epoch(_fromEpoch) is_valid_epoch(_toEpoch) onlyGovernance {
193:         for (uint256 i = _fromEpoch; i <= _toEpoch; i += WEEK) {
194:             RewardInformation storage ri = rewardInformation[i];
195:             if(!ri.set) {
196:               emit RewardAlreadySetForEpoch(i); //@audit this can make the sponsor aware of the epochs for which rewards are already set before continuing              
197:               continue;
198:             }
199:             ri.set = true;
200:             ri.amount = _amountPerEpoch;
201:         }
202:     }
```