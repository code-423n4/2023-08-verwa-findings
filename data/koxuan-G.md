# Report


## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | Use assembly to check for `address(0)` | 3 |
| [GAS-2](#GAS-2) | Using bools for storage incurs overhead | 2 |
| [GAS-3](#GAS-3) | Use calldata instead of memory for function arguments that do not get mutated | 2 |
| [GAS-4](#GAS-4) | Use Custom Errors | 38 |
| [GAS-5](#GAS-5) | Don't initialize variables with default value | 21 |
| [GAS-6](#GAS-6) | Long revert strings | 1 |
| [GAS-7](#GAS-7) | Functions guaranteed to revert when called by normal users can be marked `payable` | 4 |
| [GAS-8](#GAS-8) | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 4 |
| [GAS-9](#GAS-9) | Using `private` rather than `public` for constants, saves gas | 6 |
| [GAS-10](#GAS-10) | Use shift Right/Left instead of division/multiplication if possible | 2 |
| [GAS-11](#GAS-11) | Splitting require() statements that use && saves gas | 2 |
| [GAS-12](#GAS-12) | Use != 0 instead of > 0 for unsigned integer comparison | 17 |
### <a name="GAS-1"></a>[GAS-1] Use assembly to check for `address(0)`
*Saves 6 gas per instance*

*Instances (3)*:
```solidity
File: VotingEscrow.sol

126:         if (_addr != address(0)) {

223:         if (_addr != address(0)) {

239:         if (_addr != address(0)) {

```

### <a name="GAS-2"></a>[GAS-2] Using bools for storage incurs overhead
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past. See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27).

*Instances (2)*:
```solidity
File: GaugeController.sol

26:     mapping(address => bool) public isValidGauge;

```

```solidity
File: LendingLedger.sol

15:     mapping(address => bool) public lendingMarketWhitelist;

```

### <a name="GAS-3"></a>[GAS-3] Use calldata instead of memory for function arguments that do not get mutated
Mark data types as `calldata` instead of `memory` where possible. This makes it so that the data is not automatically loaded into memory. If the data passed into the function does not need to be changed (like updating values in an array), it can be passed in as `calldata`. The one exception to this is if the argument must later be passed into another function that takes an argument that specifies `memory` storage.

*Instances (2)*:
```solidity
File: VotingEscrow.sol

72:     constructor(string memory _name, string memory _symbol) {

72:     constructor(string memory _name, string memory _symbol) {

```

### <a name="GAS-4"></a>[GAS-4] Use Custom Errors
[Source](https://blog.soliditylang.org/2021/04/21/custom-errors/)
Instead of using error strings, to reduce deployment and runtime cost, you should use Custom Errors. This would save both deployment and runtime cost.

*Instances (38)*:
```solidity
File: GaugeController.sol

119:         require(!isValidGauge[_gauge], "Gauge already exists");

128:         require(isValidGauge[_gauge], "Invalid gauge address");

212:         require(_user_weight >= 0 && _user_weight <= 10_000, "Invalid user weight");

213:         require(isValidGauge[_gauge_addr], "Invalid gauge address");

221:         require(slope_ >= 0, "Invalid slope");

225:         require(lock_end > next_time, "Lock expires too soon");

241:         require(power_used >= 0 && power_used <= 10_000, "Used too much power");

```

```solidity
File: LendingLedger.sol

37:         require(_timestamp % WEEK == 0 || _timestamp == type(uint256).max, "Invalid timestamp");

110:         require(lendingMarketTotalBalanceEpoch[_market] > 0, "No deposits for this market");

122:         require(lendingMarketBalancesEpoch[_market][_lender] > 0, "No deposits for this lender in this market");

131:         require(lendingMarketWhitelist[lendingMarket], "Market not whitelisted");

136:         require(updatedLenderBalance >= 0, "Lender balance underflow"); // Sanity check performed here, but the market should ensure that this never happens

141:         require(updatedMarketBalance >= 0, "Market balance underflow"); // Sanity check performed here, but the market should ensure that this never happens

159:         require(userLastClaimed > 0, "No deposits for this user");

172:                 require(ri.set, "Reward not set yet"); // Can only claim for epochs where rewards are set, even if it is set to 0

180:             require(success, "Failed to send CANTO");

195:             require(!ri.set, "Rewards already set");

205:         require(lendingMarketWhitelist[_market] != _isWhiteListed, "No change");

```

```solidity
File: VotingEscrow.sol

272:         require(_value > 0, "Only non zero amount");

273:         require(msg.value == _value, "Invalid value");

274:         require(locked_.amount == 0, "Lock exists");

291:         require(_value > 0, "Only non zero amount");

292:         require(msg.value == _value, "Invalid value");

293:         require(locked_.amount > 0, "No lock");

294:         require(locked_.end > block.timestamp, "Lock expired");

314:             require(locked_.amount > 0, "Delegatee has no lock");

315:             require(locked_.end > block.timestamp, "Delegatee lock expired");

329:         require(locked_.amount > 0, "No lock");

330:         require(locked_.end <= block.timestamp, "Lock not expired");

331:         require(locked_.delegatee == msg.sender, "Lock delegated");

347:         require(success, "Failed to send CANTO");

359:         require(locked_.amount > 0, "No lock");

360:         require(locked_.delegatee != _addr, "Already delegated");

382:         require(toLocked.amount > 0, "Delegatee has no lock");

383:         require(toLocked.end > block.timestamp, "Delegatee lock expired");

384:         require(toLocked.end >= fromLocked.end, "Only delegate to longer lock");

488:         require(_blockNumber <= block.number, "Only past block number");

573:         require(_blockNumber <= block.number, "Only past block number");

```

### <a name="GAS-5"></a>[GAS-5] Don't initialize variables with default value

*Instances (21)*:
```solidity
File: GaugeController.sol

227:         uint256 old_dt = 0;

```

```solidity
File: VotingEscrow.sol

122:         int128 oldSlopeDelta = 0;

123:         int128 newSlopeDelta = 0;

176:         uint256 blockSlope = 0; // dblock/dt

185:         for (uint256 i = 0; i < 255; i++) {

189:             int128 dSlope = 0;

200:                 lastPoint.bias = 0;

204:                 lastPoint.slope = 0;

229:                 lastPoint.slope = 0;

232:                 lastPoint.bias = 0;

433:         uint256 min = 0;

436:         for (uint256 i = 0; i < 128; i++) {

452:         uint256 min = 0;

454:         for (uint256 i = 0; i < 128; i++) {

481:             lastPoint.bias = 0;

505:         uint256 dBlock = 0;

506:         uint256 dTime = 0;

538:         for (uint256 i = 0; i < 255; i++) {

540:             int128 dSlope = 0;

559:             lastPoint.bias = 0;

585:         uint256 dTime = 0;

```

### <a name="GAS-6"></a>[GAS-6] Long revert strings

*Instances (1)*:
```solidity
File: LendingLedger.sol

122:         require(lendingMarketBalancesEpoch[_market][_lender] > 0, "No deposits for this lender in this market");

```

### <a name="GAS-7"></a>[GAS-7] Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

*Instances (4)*:
```solidity
File: GaugeController.sol

118:     function add_gauge(address _gauge) external onlyGovernance {

127:     function remove_gauge(address _gauge) external onlyGovernance {

204:     function change_gauge_weight(address _gauge, uint256 _weight) public onlyGovernance {

```

```solidity
File: LendingLedger.sol

204:     function whiteListLendingMarket(address _market, bool _isWhiteListed) external onlyGovernance {

```

### <a name="GAS-8"></a>[GAS-8] `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
*Saves 5 gas per loop*

*Instances (4)*:
```solidity
File: VotingEscrow.sol

185:         for (uint256 i = 0; i < 255; i++) {

436:         for (uint256 i = 0; i < 128; i++) {

454:         for (uint256 i = 0; i < 128; i++) {

538:         for (uint256 i = 0; i < 255; i++) {

```

### <a name="GAS-9"></a>[GAS-9] Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*Instances (6)*:
```solidity
File: GaugeController.sol

16:     uint256 public constant WEEK = 7 days;

17:     uint256 public constant MULTIPLIER = 10**18;

```

```solidity
File: LendingLedger.sol

10:     uint256 public constant WEEK = 7 days;

```

```solidity
File: VotingEscrow.sol

31:     uint256 public constant WEEK = 7 days;

32:     uint256 public constant LOCKTIME = 1825 days;

33:     uint256 public constant MULTIPLIER = 10**18;

```

### <a name="GAS-10"></a>[GAS-10] Use shift Right/Left instead of division/multiplication if possible

*Instances (2)*:
```solidity
File: VotingEscrow.sol

438:             uint256 mid = (min + max + 1) / 2;

458:             uint256 mid = (min + max + 1) / 2;

```

### <a name="GAS-11"></a>[GAS-11] Splitting require() statements that use && saves gas

*Instances (2)*:
```solidity
File: GaugeController.sol

212:         require(_user_weight >= 0 && _user_weight <= 10_000, "Invalid user weight");

241:         require(power_used >= 0 && power_used <= 10_000, "Used too much power");

```

### <a name="GAS-12"></a>[GAS-12] Use != 0 instead of > 0 for unsigned integer comparison

*Instances (17)*:
```solidity
File: GaugeController.sol

93:         if (t > 0) {

155:         if (total_weight > 0) {

```

```solidity
File: LendingLedger.sol

110:         require(lendingMarketTotalBalanceEpoch[_market] > 0, "No deposits for this market");

122:         require(lendingMarketBalancesEpoch[_market][_lender] > 0, "No deposits for this lender in this market");

159:         require(userLastClaimed > 0, "No deposits for this user");

178:         if (cantoToSend > 0) {

```

```solidity
File: VotingEscrow.sol

129:             if (_oldLocked.end > block.timestamp && _oldLocked.delegated > 0) {

133:             if (_newLocked.end > block.timestamp && _newLocked.delegated > 0) {

167:         if (epoch > 0) {

272:         require(_value > 0, "Only non zero amount");

291:         require(_value > 0, "Only non zero amount");

293:         require(locked_.amount > 0, "No lock");

314:             require(locked_.amount > 0, "Delegatee has no lock");

329:         require(locked_.amount > 0, "No lock");

359:         require(locked_.amount > 0, "No lock");

382:         require(toLocked.amount > 0, "Delegatee has no lock");

405:         if (newLocked.amount > 0) {

```

