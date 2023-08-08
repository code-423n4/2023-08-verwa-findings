# Report


## Non Critical Issues


| |Issue|Instances|
|-|:-|:-:|
| [NC-1](#NC-1) | Missing checks for `address(0)` when assigning values to address state variables | 2 |
| [NC-2](#NC-2) | Constants in comparisons should appear on the left side | 8 |
| [NC-3](#NC-3) | delete keyword can be used instead of setting to 0 | 13 |
| [NC-4](#NC-4) | Lines are too long | 6 |
| [NC-5](#NC-5) |  `require()` / `revert()` statements should have descriptive reason strings | 2 |
| [NC-6](#NC-6) | TODO Left in the code | 2 |
| [NC-7](#NC-7) | Event is missing `indexed` fields | 2 |
| [NC-8](#NC-8) | Functions not used internally could be marked external | 5 |
### <a name="NC-1"></a>[NC-1] Missing checks for `address(0)` when assigning values to address state variables

*Instances (2)*:
```solidity
File: GaugeController.sol

59:         governance = _governance; // TODO: Maybe change to Oracle

```

```solidity
File: LendingLedger.sol

48:         governance = _governance; // TODO: Maybe change to Oracle

```

### <a name="NC-2"></a>[NC-2] Constants in comparisons should appear on the left side
Constants should appear on the left side of comparisons, to avoid accidental assignment

*Instances (8)*:
```solidity
File: LendingLedger.sol

37:         require(_timestamp % WEEK == 0 || _timestamp == type(uint256).max, "Invalid timestamp");

64:         if (lastUserUpdateEpoch == 0) {

87:         if (lastMarketUpdateEpoch == 0) {

```

```solidity
File: VotingEscrow.sol

104:         if (uepoch == 0) {

142:             if (uEpoch == 0) {

274:         require(locked_.amount == 0, "Lock exists");

475:         if (epoch == 0) {

492:         if (userEpoch == 0) {

```

### <a name="NC-3"></a>[NC-3] delete keyword can be used instead of setting to 0
It's clearer and reflects the intention of the programmer

*Instances (13)*:
```solidity
File: GaugeController.sol

78:                 pt.bias = 0;

79:                 pt.slope = 0;

104:                     pt.bias = 0;

105:                     pt.slope = 0;

```

```solidity
File: VotingEscrow.sol

200:                 lastPoint.bias = 0;

204:                 lastPoint.slope = 0;

229:                 lastPoint.slope = 0;

232:                 lastPoint.bias = 0;

335:         newLocked.amount = 0;

336:         newLocked.end = 0;

340:         newLocked.delegated = 0;

481:             lastPoint.bias = 0;

559:             lastPoint.bias = 0;

```

### <a name="NC-4"></a>[NC-4] Lines are too long
Recommended by solidity docs to keep lines to 120 characters or lesser

*Instances (6)*:
```solidity
File: LendingLedger.sol

17:     mapping(address => mapping(address => mapping(uint256 => uint256))) public lendingMarketBalances; // cNote balances of users within the lending markets, indexed by epoch

21:     mapping(address => mapping(uint256 => uint256)) public lendingMarketTotalBalance; // Total balance locked within the market, i.e. sum of lendingMarketBalances for all

26:     mapping(address => mapping(address => uint256)) public userClaimedEpoch; // Until which epoch a user has claimed for a particular market (exclusive this value)

136:         require(updatedLenderBalance >= 0, "Lender balance underflow"); // Sanity check performed here, but the market should ensure that this never happens

141:         require(updatedMarketBalance >= 0, "Market balance underflow"); // Sanity check performed here, but the market should ensure that this never happens

174:                 cantoToSend += (marketWeight * userBalance * ri.amount) / (1e18 * marketBalance); // (marketWeight / 1e18) * (userBalance / marketBalance) * ri.amount;

```

### <a name="NC-5"></a>[NC-5]  `require()` / `revert()` statements should have descriptive reason strings

*Instances (2)*:
```solidity
File: GaugeController.sol

51:         require(msg.sender == governance);

```

```solidity
File: LendingLedger.sol

42:         require(msg.sender == governance);

```

### <a name="NC-6"></a>[NC-6] TODO Left in the code
TODOs may signal that a feature is missing or not ready for audit, consider resolving the issue and removing the TODO comment

*Instances (2)*:
```solidity
File: GaugeController.sol

59:         governance = _governance; // TODO: Maybe change to Oracle

```

```solidity
File: LendingLedger.sol

48:         governance = _governance; // TODO: Maybe change to Oracle

```

### <a name="NC-7"></a>[NC-7] Event is missing `indexed` fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*Instances (2)*:
```solidity
File: VotingEscrow.sol

21:     event Deposit(address indexed provider, uint256 value, uint256 locktime, LockAction indexed action, uint256 ts);

22:     event Withdraw(address indexed provider, uint256 value, LockAction indexed action, uint256 ts);

```

### <a name="NC-8"></a>[NC-8] Functions not used internally could be marked external

*Instances (5)*:
```solidity
File: GaugeController.sol

204:     function change_gauge_weight(address _gauge, uint256 _weight) public onlyGovernance {

```

```solidity
File: VotingEscrow.sol

473:     function balanceOf(address _owner) public view returns (uint256) {

487:     function balanceOfAt(address _owner, uint256 _blockNumber) public view returns (uint256) {

565:     function totalSupply() public view returns (uint256) {

572:     function totalSupplyAt(uint256 _blockNumber) public view returns (uint256) {

```


## Low Issues


| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) | Divide before Multiplication | 10 |
| [L-2](#L-2) | Empty Function Body - Consider commenting why | 1 |
### <a name="L-1"></a>[L-1] Divide before Multiplication
Unnecessary loss of precision caused by divide before multiplication

*Instances (10)*:
```solidity
File: GaugeController.sol

60:         uint256 last_epoch = (block.timestamp / WEEK) * WEEK;

153:         uint256 t = (_time / WEEK) * WEEK;

191:         uint256 next_time = ((block.timestamp + WEEK) / WEEK) * WEEK;

224:         uint256 next_time = ((block.timestamp + WEEK) / WEEK) * WEEK;

```

```solidity
File: LendingLedger.sol

60:         uint256 currEpoch = (block.timestamp / WEEK) * WEEK;

84:         uint256 currEpoch = (block.timestamp / WEEK) * WEEK;

134:         uint256 currEpoch = (block.timestamp / WEEK) * WEEK;

162:         uint256 currEpoch = (block.timestamp / WEEK) * WEEK;

174:                 cantoToSend += (marketWeight * userBalance * ri.amount) / (1e18 * marketBalance); // (marketWeight / 1e18) * (userBalance / marketBalance) * ri.amount;

```

```solidity
File: VotingEscrow.sol

425:         return (_t / WEEK) * WEEK;

```

### <a name="L-2"></a>[L-2] Empty Function Body - Consider commenting why

*Instances (1)*:
```solidity
File: LendingLedger.sol

209:     receive() external payable {}

```

