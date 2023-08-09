# QA Report

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
There are _ instances of this:

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