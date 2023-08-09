## Non Critical Issues

|       | Issue                                                                      |
| ----- | :------------------------------------------------------------------------- |
| NC-1  | ADD TO BLACKLIST FUNCTION                                                  |
| NC-2  | GENERATE PERFECT CODE HEADERS EVERY TIME                                   |
| NC-3  | USE A SINGLE FILE FOR ALL SYSTEM-WIDE CONSTANTS                            |
| NC-4  | LARGE MULTIPLES OF TEN SHOULD USE SCIENTIFIC NOTATION                      |
| NC-5  | FOR EXTENDED “USING-FOR” USAGE, USE THE LATEST PRAGMA VERSION              |
| NC-6  | MISSING EVENT FOR CRITICAL PARAMETER CHANGE                                |
| NC-7  | Omissions in events                                                        |
| NC-8  | USE A MORE RECENT VERSION OF SOLIDITY                                      |
| NC-9  | `require()` / `revert()` statements should have descriptive reason strings |
| NC-10 | Event is missing `indexed` fields OR Add indexes to events                 |

### [NC-1] ADD TO BLACKLIST FUNCTION

#### Description:

NFT thefts have increased recently, so with the addition of hacked NFTs to the platform, NFTs can be converted into liquidity. To prevent this, I recommend adding the blacklist function.

Marketplaces such as Opensea have a blacklist feature that will not list NFTs that have been reported theft, NFT projects such as Manifold have blacklist functions in their smart contracts.

Here is the project example; Manifold

Manifold Contract
https://etherscan.io/address/0xe4e4003afe3765aca8149a82fc064c0b125b9e5a#code

#### Recommended Mitigation Steps:

Add to Blacklist function and modifier.

```solidity
     modifier nonBlacklistRequired(address extension) {
         require(!_blacklistedExtensions.contains(extension), "Extension blacklisted");
         _;
     }

```

### [NC-2] GENERATE PERFECT CODE HEADERS EVERY TIME

#### Description:

I recommend using header for Solidity code layout and readability:
https://github.com/transmissions11/headers

```solidity
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```

### [NC-3] USE A SINGLE FILE FOR ALL SYSTEM-WIDE CONSTANTS

#### Description:

There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values).

This will help with readability and easier maintenance for future changes.

Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.

#### **Proof Of Concept**

```solidity
File: src/GaugeController.sol

16:     uint256 public constant WEEK = 7 days;

17:     uint256 public constant MULTIPLIER = 10**18;

```

```solidity
File: src/LendingLedger.sol

10:     uint256 public constant WEEK = 7 days;

```

```solidity
File: src/VotingEscrow.sol

31:     uint256 public constant WEEK = 7 days;

33:     uint256 public constant MULTIPLIER = 10**18;

```

### [NC-4] LARGE MULTIPLES OF TEN SHOULD USE SCIENTIFIC NOTATION

#### Description:

Use (e.g. 1e6) rather than decimal literals (e.g. 100000), for better code readability.

Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.

https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

https://github.com/code-423n4/2022-10-holograph-findings/blob/main/data/Rolezn-Q.md#NC%E2%80%917

#### **Proof Of Concept**

```solidity
File: src/VotingEscrow.sol

37:     Point[1000000000000000000] public pointHistory; // 1e9 * userPointHistory-length, so sufficient for 1e9 users

38:     mapping(address => Point[1000000000]) public userPointHistory;

```

### [NC-5] FOR EXTENDED “USING-FOR” USAGE, USE THE LATEST PRAGMA VERSION

#### Description:

https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/

#### **Proof Of Concept**

```solidity
File: src/GaugeController.sol

2: pragma solidity ^0.8.16;

```

```solidity
File: src/LendingLedger.sol

2: pragma solidity ^0.8.16;

```

```solidity
File: src/VotingEscrow.sol

2: pragma solidity ^0.8.16;

```

### [NC-6] MISSING EVENT FOR CRITICAL PARAMETER CHANGE

#### Description:

Events help non-contract tools to track changes, and events prevent users from being surprised by changes.

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

#### **Proof Of Concept**

```solidity
File: src/LendingLedger.sol

188:     function setRewards(

```

### [NC-7] Omissions in events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters.

The events should include the new value and old value where possible.

#### **Proof Of Concept**

```solidity
File: src/GaugeController.sol

121:         emit NewGauge(_gauge);

131:         emit GaugeRemoved(_gauge);

```

### [NC-8] USE A MORE RECENT VERSION OF SOLIDITY

#### Description:

For security, it is best practice to use the latest Solidity version. For the security fix list in the versions; https://github.com/ethereum/solidity/blob/develop/Changelog.md

#### **Proof Of Concept**

```solidity
File: src/GaugeController.sol

2: pragma solidity ^0.8.16;

```

```solidity
File: src/LendingLedger.sol

2: pragma solidity ^0.8.16;

```

```solidity
File: src/VotingEscrow.sol

2: pragma solidity ^0.8.16;

```

### [NC-9] `require()` / `revert()` statements should have descriptive reason strings

#### Description:

[code4arena](https://code4rena.com/reports/2022-10-holograph/#15-require--revert-statements-should-have-descriptive-reason-strings)[code4arena](https://code4rena.com/reports/2022-10-thegraph#n-05-require-revert-cause-should-be-known)

#### **Proof Of Concept**

```solidity
File: src/GaugeController.sol

51:         require(msg.sender == governance);

```

```solidity
File: src/LendingLedger.sol

42:         require(msg.sender == governance);

```

### [NC-10] Event is missing `indexed` fields OR Add indexes to events

#### Description:

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.Parameters of certain events are expected to be indexed (e.g. ERC20 Transfer/Approval events) so that they’re included in the block’s bloom filter for faster access. Failure to do so might confuse off-chain tooling looking for such indexed events.

https://github.com/crytic/slither/wiki/Detector-Documentation#unindexed-erc20-event-oarameters

#### **Proof Of Concept**

```solidity
File: src/VotingEscrow.sol

21:     event Deposit(address indexed provider, uint256 value, uint256 locktime, LockAction indexed action, uint256 ts);

22:     event Withdraw(address indexed provider, uint256 value, LockAction indexed action, uint256 ts);

```

## Low Issues

|     | Issue                                                   |
| --- | :------------------------------------------------------ |
| L-1 | CRITICAL CHANGES SHOULD USE TWO-STEP PROCEDURE          |
| L-2 | LACK OF INPUT VALIDATION                                |
| L-3 | LOSS OF PRECISION DUE TO ROUNDING                       |
| L-4 | REQUIRE MESSAGES ARE TOO SHORT AND UNCLEAR              |
| L-5 | Timestamp Dependence                                    |
| L-6 | Account existence check for low-level calls             |
| L-7 | UNUSED `RECEIVE()` FUNCTION WILL LOCK ETHER IN CONTRACT |

### [L-1] CRITICAL CHANGES SHOULD USE TWO-STEP PROCEDURE

The critical procedures should be two step process.

Changing critical addresses in contracts should be a two-step process where the first transaction (from the old/current address) registers the new address (i.e. grants ownership) and the second transaction (from the new address) replaces the old address with the new one (i.e. claims ownership). This gives an opportunity to recover from incorrect addresses mistakenly used in the first step. If not, contract functionality might become inaccessible. (see [here](https://github.com/OpenZeppelin/openzeppelin-contracts/issues/1488) and [here](https://github.com/OpenZeppelin/openzeppelin-contracts/issues/2369))

#### **Proof Of Concept**

```solidity
File: src/LendingLedger.sol

188:     function setRewards(
                uint256 _fromEpoch,
            uint256 _toEpoch,
            uint248 _amountPerEpoch
        ) external is_valid_epoch(_fromEpoch) is_valid_epoch(_toEpoch) onlyGovernance {
            for (uint256 i = _fromEpoch; i <= _toEpoch; i += WEEK) {
                RewardInformation storage ri = rewardInformation[i];
                require(!ri.set, "Rewards already set");
                ri.set = true;
                ri.amount = _amountPerEpoch;
            }
        }

```

#### Recommended Mitigation Steps:

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.

### [L-2] LACK OF INPUT VALIDATION

#### Description:

For defence-in-depth purpose, it is recommended to perform additional validation against the amount that the user is attempting to deposit, mint, withdraw and redeem to ensure that the submitted amount is valid.
https://code4rena.com/reports/2022-11-redactedcartel#l-11-lack-of-input-validation

#### **Proof Of Concept**

```solidity
File: src/VotingEscrow.sol

333:         uint256 amountToSend = uint256(uint128(locked_.amount));

```

### [L-3] LOSS OF PRECISION DUE TO ROUNDING

#### **Proof Of Concept**

```solidity
File: src/GaugeController.sol

157:             return (MULTIPLIER * gauge_weight) / total_weight;

231:             slope: (slope * _user_weight) / 10_000,

```

```solidity
File: src/LendingLedger.sol

174:                 cantoToSend += (marketWeight * userBalance * ri.amount) / (1e18 * marketBalance); // (marketWeight / 1e18) * (userBalance / marketBalance) * ri.amount;

```

```solidity
File: src/VotingEscrow.sol

178:             blockSlope = (MULTIPLIER * (block.number - lastPoint.blk)) / (block.timestamp - lastPoint.ts);

208:             lastPoint.blk = initialLastPoint.blk + (blockSlope * (iterativeTime - initialLastPoint.ts)) / MULTIPLIER;

589:                 dTime = ((_blockNumber - point.blk) * (pointNext.ts - point.ts)) / (pointNext.blk - point.blk);

592:             dTime = ((_blockNumber - point.blk) * (block.timestamp - point.ts)) / (block.number - point.blk);

```

### [L-4] REQUIRE MESSAGES ARE TOO SHORT AND UNCLEAR

#### Description:

The correct and clear error description explains to the user why the function reverts, but the error descriptions below in the project are not self-explanatory. These error descriptions are very important in the debug features of DApps like Tenderly. Error definitions should be added to the require block, not exceeding 32 bytes.

#### **Proof Of Concept**

```solidity
File: src/LendingLedger.sol

42:         require(msg.sender == governance);

```

#### Recommended Mitigation Steps:

Error definitions should be added to the require block, not exceeding 32 bytes or we should use custom errors

### [L-5] Timestamp Dependence

#### Description:

Contracts often need access to time values to perform certain types of functionality. Values such as block.timestamp, and block.number can give you a sense of the current time or a time delta, however, they are not safe to use for most purposes.

In the case of block.timestamp, developers often attempt to use it to trigger time-dependent events. As Ethereum is decentralized, nodes can synchronize time only to some degree. Moreover, malicious miners can alter the timestamp of their blocks, especially if they can gain advantages by doing so. However, miners cant set a timestamp smaller than the previous one (otherwise the block will be rejected), nor can they set the timestamp too far ahead in the future. Taking all of the above into consideration, developers cant rely on the preciseness of the provided timestamp.

As for block.number, considering the block time on Ethereum is generally about 14 seconds, it`s possible to predict the time delta between blocks. However, block times are not constant and are subject to change for a variety of reasons, e.g. fork reorganisations and the difficulty bomb. Due to variable block times, block.number should also not be relied on for precise calculations of time.
Reference: https://swcregistry.io/docs/SWC-116

#### **Proof Of Concept**

```solidity
File: src/GaugeController.sol

60:         uint256 last_epoch = (block.timestamp / WEEK) * WEEK;

70:             if (t > block.timestamp) break;

82:             if (t > block.timestamp) time_sum = t;

96:                 if (t > block.timestamp) break;

108:                 if (t > block.timestamp) time_weight[_gauge_addr] = t;

191:         uint256 next_time = ((block.timestamp + WEEK) / WEEK) * WEEK;

224:         uint256 next_time = ((block.timestamp + WEEK) / WEEK) * WEEK;

263:         if (old_slope.end > block.timestamp) {

277:         last_user_vote[msg.sender][_gauge_addr] = block.timestamp;

```

```solidity
File: src/LendingLedger.sol

60:         uint256 currEpoch = (block.timestamp / WEEK) * WEEK;

84:         uint256 currEpoch = (block.timestamp / WEEK) * WEEK;

134:         uint256 currEpoch = (block.timestamp / WEEK) * WEEK;

162:         uint256 currEpoch = (block.timestamp / WEEK) * WEEK;

```

```solidity
File: src/VotingEscrow.sol

73:         pointHistory[0] = Point({bias: int128(0), slope: int128(0), ts: block.timestamp, blk: block.number});

73:         pointHistory[0] = Point({bias: int128(0), slope: int128(0), ts: block.timestamp, blk: block.number});

129:             if (_oldLocked.end > block.timestamp && _oldLocked.delegated > 0) {

131:                 userOldPoint.bias = userOldPoint.slope * int128(int256(_oldLocked.end - block.timestamp));

133:             if (_newLocked.end > block.timestamp && _newLocked.delegated > 0) {

135:                 userNewPoint.bias = userNewPoint.slope * int128(int256(_newLocked.end - block.timestamp));

147:             userNewPoint.ts = block.timestamp;

148:             userNewPoint.blk = block.number;

166:         Point memory lastPoint = Point({bias: 0, slope: 0, ts: block.timestamp, blk: block.number});

166:         Point memory lastPoint = Point({bias: 0, slope: 0, ts: block.timestamp, blk: block.number});

177:         if (block.timestamp > lastPoint.ts) {

178:             blockSlope = (MULTIPLIER * (block.number - lastPoint.blk)) / (block.timestamp - lastPoint.ts);

178:             blockSlope = (MULTIPLIER * (block.number - lastPoint.blk)) / (block.timestamp - lastPoint.ts);

190:             if (iterativeTime > block.timestamp) {

191:                 iterativeTime = block.timestamp;

212:             if (iterativeTime == block.timestamp) {

213:                 lastPoint.blk = block.number;

243:             if (_oldLocked.end > block.timestamp) {

251:             if (_newLocked.end > block.timestamp) {

269:         uint256 unlock_time = _floorToWeek(block.timestamp + LOCKTIME); // Locktime is rounded down to weeks

283:         emit Deposit(msg.sender, _value, unlock_time, LockAction.CREATE, block.timestamp);

294:         require(locked_.end > block.timestamp, "Lock expired");

301:         newLocked.end = _floorToWeek(block.timestamp + LOCKTIME);

315:             require(locked_.end > block.timestamp, "Delegatee lock expired");

320:             emit Deposit(delegatee, _value, newLocked.end, LockAction.DELEGATE, block.timestamp);

322:         emit Deposit(msg.sender, _value, unlockTime, action, block.timestamp);

330:         require(locked_.end <= block.timestamp, "Lock not expired");

348:         emit Withdraw(msg.sender, amountToSend, LockAction.WITHDRAW, block.timestamp);

383:         require(toLocked.end > block.timestamp, "Delegatee lock expired");

399:             emit Deposit(addr, uint256(int256(value)), newLocked.end, action, block.timestamp);

402:             emit Withdraw(addr, uint256(int256(value)), action, block.timestamp);

479:         lastPoint.bias = lastPoint.bias - (lastPoint.slope * int128(int256(block.timestamp - lastPoint.ts)));

488:         require(_blockNumber <= block.number, "Only past block number");

512:             dBlock = block.number - point0.blk;

513:             dTime = block.timestamp - point0.ts;

568:         return _supplyAt(lastPoint, block.timestamp);

573:         require(_blockNumber <= block.number, "Only past block number");

591:         } else if (point.blk != block.number) {

592:             dTime = ((_blockNumber - point.blk) * (block.timestamp - point.ts)) / (block.number - point.blk);

592:             dTime = ((_blockNumber - point.blk) * (block.timestamp - point.ts)) / (block.number - point.blk);

```

### [L-6] Account existence check for low-level calls

#### Description:

Low-level calls `call`/`delegatecall`/`staticcall` return true even if the account called is non-existent (per EVM design). Account existence must be checked prior to calling if needed.

https://github.com/crytic/slither/wiki/Detector-Documentation#low-level-callsn

#### **Proof Of Concept**

```solidity
File: src/LendingLedger.sol

179:             (bool success, ) = msg.sender.call{value: cantoToSend}("");

```

```solidity
File: src/VotingEscrow.sol

346:         (bool success, ) = msg.sender.call{value: amountToSend}("");

```

#### Recommended Mitigation Steps:

In addition to the zero-address checks, add a check to verify that <address>.code.length > 0

### [L-7] UNUSED `RECEIVE()` FUNCTION WILL LOCK ETHER IN CONTRACT

#### Description:

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert.

#### **Proof Of Concept**

```solidity
File: src/LendingLedger.sol

209:     receive() external payable {}

```

#### Recommended Mitigation Steps:

The function should call another function, otherwise it should revert
