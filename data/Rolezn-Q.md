## QA Summary<a name="QA Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW-QA&#x2011;1) | Double type casts create complexity within the code | 7 |
| [LOW&#x2011;2](#LOW-QA&#x2011;2) | `lendingMarketWhitelist` can whitelist `address(0)` | 1 |

Total: 7 contexts over 1 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Condition will not revert when `block.timestamp` is `==` to the compared variable | 9 |
| [NC&#x2011;2](#NC&#x2011;2) | Constant redefined elsewhere | 5 |
| [NC&#x2011;3](#NC&#x2011;3) | `block.timestamp` is already used when emitting events, no need to input timestamp | 6 |
| [NC&#x2011;4](#NC&#x2011;4) | Contracts are not using their OZ Upgradeable counterparts | 3 |
| [NC&#x2011;5](#NC&#x2011;5) | Omissions in Events | 1 |
| [NC&#x2011;6](#NC&#x2011;6) | Zero as a function argument should have a descriptive meaning | 25 |
| [NC&#x2011;7](#NC&#x2011;7) | `LOCKTIME` should be at least 1826 days | 25 |

Total: 49 contexts over 6 issues

## Low Risk Issues

### <a href="#qa-summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> Double type casts create complexity within the code

Double type casting should be avoided in Solidity contracts to prevent unintended consequences and ensure accurate data representation. Performing multiple type casts in succession can lead to unexpected truncation, rounding errors, or loss of precision, potentially compromising the contract's functionality and reliability. Furthermore, double type casting can make the code less readable and harder to maintain, increasing the likelihood of errors and misunderstandings during development and debugging. To ensure precise and consistent data handling, developers should use appropriate data types and avoid unnecessary or excessive type casting, promoting a more robust and dependable contract execution.

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: GaugeController.sol

222: uint256 slope = uint256(uint128(slope_));

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/GaugeController.sol#L222

```solidity
File: VotingEscrow.sol

333: uint256 amountToSend = uint256(uint128(locked_.amount));

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L333

```solidity
File: VotingEscrow.sol

483: return uint256(uint128(lastPoint.bias));
561: return uint256(uint128(lastPoint.bias));

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L483

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L561



```solidity
File: VotingEscrow.sol

523: return uint256(uint128(upoint.bias));

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L523

</details>

### <a href="#qa-summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> `lendingMarketWhitelist` can whitelist `address(0)`

There is no restriction into setting `address(0)` to lendingMarketWhitelist, which can be unintended behaviour.

#### <ins>Proof Of Concept</ins>

```solidity
File: LendingLedger.sol

function whiteListLendingMarket(address _market, bool _isWhiteListed) external onlyGovernance {
        require(lendingMarketWhitelist[_market] != _isWhiteListed, "No change");
        lendingMarketWhitelist[_market] = _isWhiteListed;
    }
```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/LendingLedger.sol#L204


## Non Critical Issues


### <a href="#qa-summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Condition will not revert when `block.timestamp` is `==` to the compared variable


The condition does not revert when block.timestamp is equal to the compared `>` or `<` variable. For example, if there is a check for `block.timestamp > expireTime` then there should be a check for cases where `block.timestamp` is `==` to `expireTime`
 
#### <ins>Proof Of Concept</ins>

```solidity
File: GaugeController.sol

70: if (t > block.timestamp) break;
```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/GaugeController.sol#L70

```solidity
File: GaugeController.sol

96: if (t > block.timestamp) break;
```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/GaugeController.sol#L96

```solidity
File: GaugeController.sol

82: if (t > block.timestamp) time_sum = t;
```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/GaugeController.sol#L82

```solidity
File: GaugeController.sol

108: if (t > block.timestamp) time_weight[_gauge_addr] = t;
```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/GaugeController.sol#L108

```solidity
File: GaugeController.sol

263: if (old_slope.end > block.timestamp) {
```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/GaugeController.sol#L263

```solidity
File: VotingEscrow.sol

177: if (block.timestamp > lastPoint.ts) {
```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L177

```solidity
File: VotingEscrow.sol

190: if (iterativeTime > block.timestamp) {
```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L190

```solidity
File: VotingEscrow.sol

243: if (_oldLocked.end > block.timestamp) {
```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L243

```solidity
File: VotingEscrow.sol

251: if (_newLocked.end > block.timestamp) {
```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L251



### <a href="#qa-summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Constant redefined elsewhere

Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A <a href="https://medium.com/coinmonks/gas-cost-of-solidity-library-functions-dbe0cedd4678">cheap way</a> to store constants in a single location is to create an `internal constant` in a `library`. If the variable is a local cache of another contract's value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don't get out of sync.

#### <ins>Proof Of Concept</ins>

```solidity
File: GaugeController.sol

16: uint256 public constant WEEK = 7 days;
17: uint256 public constant MULTIPLIER = 10**18;

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/GaugeController.sol#L16-L17

```solidity
File: LendingLedger.sol

10: uint256 public constant WEEK = 7 days;

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/LendingLedger.sol#L10

```solidity
File: VotingEscrow.sol

31: uint256 public constant WEEK = 7 days;
33: uint256 public constant MULTIPLIER = 10**18;

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L31

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L33



### <a href="#qa-summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> block.timestamp is already used when emitting events, no need to input timestamp

#### <ins>Proof Of Concept</ins>

```solidity
File: VotingEscrow.sol

283: emit Deposit(msg.sender, _value, unlock_time, LockAction.CREATE, block.timestamp);

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L283

```solidity
File: VotingEscrow.sol

320: emit Deposit(delegatee, _value, newLocked.end, LockAction.DELEGATE, block.timestamp);
322: emit Deposit(msg.sender, _value, unlockTime, action, block.timestamp);

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L320

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L322



```solidity
File: VotingEscrow.sol

348: emit Withdraw(msg.sender, amountToSend, LockAction.WITHDRAW, block.timestamp);

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L348

```solidity
File: VotingEscrow.sol

399: emit Deposit(addr, uint256(int256(value)), newLocked.end, action, block.timestamp);
402: emit Withdraw(addr, uint256(int256(value)), action, block.timestamp);

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L399

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L402




### <a href="#qa-summary">[NC&#x2011;20]</a><a name="NC&#x2011;20"> Generate perfect code headers for better solidity code layout and readability

It is recommended to use pre-made headers for Solidity code layout and readability:
https://github.com/transmissions11/headers

```solidity
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```


#### <ins>Proof Of Concept</ins>


```solidity
File: VotingEscrow.sol

11: VotingEscrow.sol

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L11




### <a href="#qa-summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> Contracts are not using their OZ Upgradeable counterparts

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

#### <ins>Proof of Concept</ins>

```solidity
File: GaugeController.sol

5: import {Math} from "@openzeppelin/contracts/utils/math/Math.sol";

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/GaugeController.sol#L5

```solidity
File: LendingLedger.sol

6: import {Math} from "@openzeppelin/contracts/utils/math/Math.sol";

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/LendingLedger.sol#L6

```solidity
File: VotingEscrow.sol

4: import {ReentrancyGuard} from "@openzeppelin/contracts/security/ReentrancyGuard.sol";

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L4



#### <ins>Recommended Mitigation Steps</ins>

Where applicable, use the contracts from `@openzeppelin/contracts-upgradeable` instead of `@openzeppelin/contracts`.
See https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/tree/master/contracts for list of available upgradeable contracts


### <a href="#qa-summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters
The following events should also add the previous original value in addition to the new value.

#### <ins>Proof Of Concept</ins>


```solidity
File: GaugeController.sol

20: event NewGauge(address indexed gauge_address);
```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/GaugeController.sol#L20




#### <ins>Recommended Mitigation Steps</ins>
The events should include the new value and old value where possible.



### <a href="#qa-summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Zero as a function argument should have a descriptive meaning

Consider using descriptive constants or an enum instead of passing zero directly on function calls, as that might be error-prone, to fully describe the caller's intention.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: GaugeController.sol

221: require(slope_ >= 0, "Invalid slope");

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/GaugeController.sol#L221

```solidity
File: LendingLedger.sol

110: require(lendingMarketTotalBalanceEpoch[_market] > 0, "No deposits for this market");

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/LendingLedger.sol#L110

```solidity
File: LendingLedger.sol

122: require(lendingMarketBalancesEpoch[_market][_lender] > 0, "No deposits for this lender in this market");

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/LendingLedger.sol#L122

```solidity
File: LendingLedger.sol

136: require(updatedLenderBalance >= 0, "Lender balance underflow");
141: require(updatedMarketBalance >= 0, "Market balance underflow");

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/LendingLedger.sol#L136

https://github.com/code-423n4/2023-08-verwa/tree/main/src/LendingLedger.sol#L141



```solidity
File: LendingLedger.sol

159: require(userLastClaimed > 0, "No deposits for this user");

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/LendingLedger.sol#L159

```solidity
File: VotingEscrow.sol

105: return (0, 0, 0);

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L105

```solidity
File: VotingEscrow.sol

166: Point memory lastPoint = Point({bias: 0, slope: 0, ts: block.timestamp, blk: block.number});
175: Point memory initialLastPoint = Point({bias: 0, slope: 0, ts: lastPoint.ts, blk: lastPoint.blk});

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L166

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L175



```solidity
File: VotingEscrow.sol

272: require(_value > 0, "Only non zero amount");
291: require(_value > 0, "Only non zero amount");
281: _checkpoint(msg.sender, LockedBalance(0, 0, 0, address(0)), locked_);

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L272

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L291

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L281

</details>

### <a href="#qa-summary">[NC&#x2011;7]</a><a name="NC&#x2011;7"> `LOCKTIME` should be at least 1826 days

Taking into account that leap years occur every 4 years, `LOCKTIME` should at least be 1826 days.

#### <ins>Proof Of Concept</ins>

```solidity
File: VotingEscrow.sol

uint256 public constant LOCKTIME = 1825 days;
```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L32