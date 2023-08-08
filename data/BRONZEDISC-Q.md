## QA
---

### Layout Order [1]

- The best-practices for layout within a contract is the following order: state variables, events, modifiers, constructor and functions.

https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol

```solidity
// place events after state variables.
21:    event Deposit(address indexed provider, uint256 value, uint256 locktime, LockAction indexed action, uint256 ts);
22:    event Withdraw(address indexed provider, uint256 value, LockAction indexed action, uint256 ts);
23:    event Unlock();
```

https://github.com/code-423n4/2023-08-verwa/blob/main/src/GaugeController.sol

```solidity
// place events after state variables.
20:    event NewGauge(address indexed gauge_address);
21:    event GaugeRemoved(address indexed gauge_address);
```

---

### Function Visibility [2]

- Order of Functions: Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. Functions should be grouped according to their visibility and ordered: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private. Within a grouping, place the view and pure functions last.

https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol

```solidity
// place these private state variables for last.
55:    function _checkpoint_lender(
83:    function _checkpoint_market(address _market, uint256 _forwardTimestampLimit) private {

/// place this receive right after the constructor
209:    receive() external payable {}
```

https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol

```solidity
// place all these internal functions for last since there are no private ones.
115:    function _checkpoint(
390:    function _delegate(
412:    function _copyLock(LockedBalance memory _locked) internal pure returns (LockedBalance memory) {
424:    function _floorToWeek(uint256 _t) internal pure returns (uint256) {
431:    function _findBlockEpoch(uint256 _block, uint256 _maxEpoch) internal view returns (uint256) {
451:    function _findUserBlockEpoch(address _addr, uint256 _block) internal view returns (uint256) {
533:    function _supplyAt(Point memory _point, uint256 _t) internal view returns (uint256) {
```

https://github.com/code-423n4/2023-08-verwa/blob/main/src/GaugeController.sol

```solidity
// place internal variables right after public ones.
66:    function _get_sum() internal returns (uint256) {

// place private variables for last.
91:    function _get_weight(address _gauge_addr) private returns (uint256) {
152:    function _gauge_relative_weight(address _gauge, uint256 _time) private view returns (uint256) {
```

---

### natSpec missing [3]

Some functions are missing @params or @returns. Specification Format.” These are written with a triple slash (///) or a double asterisk block(/** ... */) directly above function declarations or statements to generate documentation in JSON format for developers and end-users. It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). These comments contain different types of tags:
- @title: A title that should describe the contract/interface @author: The name of the author (for contract, interface) 
- @notice: Explain to an end user what this does (for contract, interface, function, public state variable, event) 
- @dev: Explain to a developer any extra details (for contract, interface, function, state variable, event) 
- @param: Documents a parameter (just like in doxygen) and must be followed by parameter name (for function, event)
- @return: Documents the return variables of a contract’s function (function, public state variable)
- @inheritdoc: Copies all missing tags from the base function and must be followed by the contract name (for function, public state variable)
- @custom…: Custom tag, semantics is application-defined (for everywhere)

https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol

```solidity
8:  contract LendingLedger {
28:    struct RewardInformation {
41:    modifier onlyGovernance() {
46:    constructor(address _gaugeController, address _governance) {
```

https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol

```solidity
44:    struct Point {
50:    struct LockedBalance {
58:    enum LockAction {

// @param missing
288:    function increaseAmount(uint256 _value) external payable nonReentrant {
356:    function delegate(address _addr) external nonReentrant {
390:    function _delegate(

// @params and @return missing
412:    function _copyLock(LockedBalance memory _locked) internal pure returns (LockedBalance memory) {
473:    function balanceOf(address _owner) public view returns (uint256) {
487:    function balanceOfAt(address _owner, uint256 _blockNumber) public view returns (uint256) {
572:    function totalSupplyAt(uint256 _blockNumber) public view returns (uint256) {

// @return missing
424:    function _floorToWeek(uint256 _t) internal pure returns (uint256) {
431:    function _findBlockEpoch(uint256 _block, uint256 _maxEpoch) internal view returns (uint256) {
451:    function _findUserBlockEpoch(address _addr, uint256 _block) internal view returns (uint256) {
565:    function totalSupply() public view returns (uint256) {
```

https://github.com/code-423n4/2023-08-verwa/blob/main/src/GaugeController.sol

```solidity
20:    event NewGauge(address indexed gauge_address);
21:    event GaugeRemoved(address indexed gauge_address);
39:    struct Point {
44:    struct VotedSlope {
50:    modifier onlyGovernance() {

// @params missing
66:    function _get_sum() internal returns (uint256) {
```

---

### State variable and function names [4]

- Variables should be named according to their specifications
- private and internal `variables` should preppend with `underline`
- private and internal `functions` should preppend with `underline`
- constant state variables should be UPPER_CASE


https://github.com/code-423n4/2023-08-verwa/blob/main/src/GaugeController.sol

```solidity
// private and internal `variables` should preppend with `underline`
33:    mapping(address => uint256) time_weight;
35:    mapping(uint256 => Point) points_sum;
36:    mapping(uint256 => uint256) changes_sum;
```

---

### Version [5]

- Pragma versions should be standardized and avoid floating pragma `( ^ )`.

https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol

```solidity
// lock this a single version by removing the ^, slightly safer
2:  pragma solidity ^0.8.16;
```

https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol

```solidity
// lock this a single version by removing the ^, slightly safer
2:  pragma solidity ^0.8.16;
```

https://github.com/code-423n4/2023-08-verwa/blob/main/src/GaugeController.sol

```solidity
// lock this a single version by removing the ^, slightly safer
2:  pragma solidity ^0.8.16;
```

---

### Observations [6]

https://github.com/code-423n4/2023-08-verwa/blob/main/src/GaugeController.sol

```solidity
// define the visibility explicitly, in this case internal
33:    mapping(address => uint256) time_weight;
35:    mapping(uint256 => Point) points_sum;
36:    mapping(uint256 => uint256) changes_sum;
```