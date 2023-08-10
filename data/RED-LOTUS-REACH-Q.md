# Low Risk and Noncritical Issues

- [Low Risk and Noncritical Issues](#low-risk-and-noncritical-issues)
- [Casting between types makes values negative for huge input values](#casting-between-types-makes-values-negative-for-huge-input-values)
- [bias and slope can be made negative with huge deposits thus manipulating total voting power](#bias-and-slope-can-be-made-negative-with-huge-deposits-thus-manipulating-total-voting-power)
- [Inconsistent Lock Durations](#inconsistent-lock-durations)
- [Unnecessary Modifiers Cost Gas](#unnecessary-modifiers-cost-gas)
- [Many cases of precision loss from multiply-after-divide](#many-cases-of-precision-loss-from-multiply-after-divide)
- [Inconsistent Use of NatSpec](#inconsistent-use-of-natspec)
- [Unnecessary or Uncomplemented receive function](#unnecessary-or-uncomplemented-receive-function)
- [Old version of OpenZeppelin Contracts used](#old-version-of-openzeppelin-contracts-used)
- [Error Messages don’t match intent](#error-messages-don-t-match-intent)
- [Function Names don’t match intent](#function-names-don-t-match-intent)
- [Dead Code](#dead-code)
    + [Unused assignment](#unused-assignment)
    + [Unused Enums](#unused-enums)
- [TODOs included in code/comments](#todos-included-in-code-comments)
- [Reference to non-existent documentation](#reference-to-non-existent-documentation)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>


# Casting between types makes values negative for huge input values

Invariants to the VotingEscrow contract can be broken.  While an enormous quantity is necessary, greater than the total supply of $CANTO, it’s worth mentioning due to the fact that invariants in the system can be broken, if only in testing environments.

Invariants affected in `VotingEscrow.sol`:

- `LockedBalance.delegated` must always be > 0
    - can be made negative
- `LockedBalance.amount` must always be > 0
    - can be made negative
- `Point.slope` must always be ≥ 0
    - can be made negative
- `Point.bias` must always be ≥ 0
    - can be made negative

Cases in `VotingEscrow.sol` where casting creates vulnerable attack surfaces:

- From increaseAmount in VotingLock.sol, line 317:

[https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L317](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L317) 

```solidity
newLocked.delegated += int128(int256(_value));
```

- From increaseAmount in VotingLock.sol, line 305:

[https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L305](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L305)

```solidity
newLocked.delegated += int128(int256(_value));
```

- From createLock in VotingLock.sol, line 276:

[https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L276](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L276)

```solidity
locked_.amount += int128(int256(_value));
```

- From createLock in VotingLock.sol, line 278:

[https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L278](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L278)

```solidity
locked_.delegated += int128(int256(_value));
```

- From increaseAmount in VotingLock.sol, line 300:

[https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L300](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L300)

```solidity
newLocked.amount += int128(int256(_value));
```

- From increaseAmount in VotingLock.sol, line 317:

[https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L317](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L317)

```solidity
newLocked.delegated += int128(int256(_value));
```

## Risk

Debugging and auditing this code is made more complex due to the inclusion of this casting, which puts the protocol more at risk than had they been unused.

It's not fully clear why the types for `amount` and `delegated`  of the `LockedBalance` struct are of type `int128`.  This could introduce unforseen edge cases due to the copious casting.  All assignments from function arguments are of type `uint256` (coming from `createLock()` and `increaseAmount()`), making the usage more peculiar and inconsistent. Similarly, `int128` is used for `bias` and `slope` of the `Point` struct, and in all places where they are used for calculations, they must be checked for being negative and are subsequently reset to `0` in the case of being negative. The suspicion is that since the origin of the `ve` system came from the original Curve contracts, the implicit assumptions of that system were kept wholesale.

## Mitigation

Use unsigned integers for all financial calculations so that all casting is removed and remove the subsequent unnecessary "resets" to `0`. Instead, for places where the "resets" are used, define pre-conditions  that ensure negative-valued outcomes are not permissible in calls to `createLock()` and `increaseLock()`.

# bias and slope can be made negative with huge deposits thus manipulating total voting power

Similar to the previous “Casting between types makes values negative for huge input values”, huge inputs can allow for attacks on the protocol, if only in a testing environment.  

In `VotingEscrow.sol` the code in question is from `_checkpoint()`, lines 129 - 136:

[https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L129-L136](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L129-L136)

```solidity
// When a huge deposit is made, the casting of _value in createLock can allow delegated to overflow into negative a value...
if (_oldLocked.end > block.timestamp && _oldLocked.delegated > 0) {
    // ...these can become negative as a result
    userOldPoint.slope = _oldLocked.delegated / int128(int256(LOCKTIME));
    userOldPoint.bias = userOldPoint.slope * int128(int256(_oldLocked.end - block.timestamp));
}
if (_newLocked.end > block.timestamp && _newLocked.delegated > 0) {
    // ...these can become negative as a result
    userNewPoint.slope = _newLocked.delegated / int128(int256(LOCKTIME));
    userNewPoint.bias = userNewPoint.slope * int128(int256(_newLocked.end - block.timestamp));
}
```

## Risk

Control over the sign of `bias` and `slope` allows for the control of decay of voting power, because at any point they become < 0, they are reset to 0, giving the theoretical attacker the ability to temporarily stem the decay of their voting power.

## Mitigation

Similar to “Casting between types makes values negative for huge input values”, bypass the need to cast (and automatically hard-lock values to 0) by using unsigned types with guards for relevant pre-conditions that ensure negative-valued outcomes are not permissible in calls to `createLock()` and `increaseLock()`.

# Inconsistent Lock Durations

Locks for veRWA are set for 5 years, given no further actions on a lock are performed.  This 5 year period is an important duration, because it also determines the calculations for the decay in voting power a lock has.  In `**VotingEscrow.sol**`, the 5 year lock period is measured as 1825 days.  

Places where the 1785 day measurement is used:

- in `_checkpoint()`, line 185

```solidity
for (uint256 i = 0; i < 255; i++) {
	iterativeTime = iterativeTime + WEEK; 

	...

	if (iterativeTime == block.timestamp) {
	    lastPoint.blk = block.number;
	    break;
	} ...
}
```

- in `_supplyAt()`, line 538

```solidity
function _supplyAt(..., uint256 _t) ... {
	for (uint256 i = 0; i < 255; i++) {
		iterativeTime = iterativeTime + WEEK; 
	
		...
	
		if (iterativeTime == _t) {
		    break;
		}
	}
}
```

## Risk

There is a possibility that the protocol faces a case where locked funds are untouched and no new funds are locked across  a 5 year period.  In this “last users standing” scenario, the protocol theoretically is still responsible for calculating the final voting weights, allowing investors to maximize the utility of their locked funds regardless of the relevancy of the protocol.

Surprisingly, the maximum span the protocol can actually measure is less than 5 years, it is 255 weeks, which equates to 1785 days.  For those final 40 days and afterwards, for example, weights will be miscalculated, and, especially depending on the size of the funds locked, returns on those funds or supply totals will be miscalculated.

## Mitigation

Increase the number of iterations to fully calculate weights through the 5 year period.

# Unnecessary Modifiers Cost Gas

Reentrancy is a common root-cause for exploits in smart contracts.  To avoid the specific case of basic reentrancy, a `nonReentrant` modifier is often used.  In the case of a handful of functions in **`VotingEscrow.sol`,** this modifier is unnecessary, because the cause of reentrancy - calling into external contracts - is also missing.  While they are not a risk, they do cause extra computation, and thus raise gas costs, which may be of some notice especially during high network congestion.

Locations where they exist:

- Line 268:

[https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L268](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L268)

```solidity
function createLock(uint256 _value) external payable nonReentrant {
```

- Line 288:

[https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L288](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L288)

```solidity
function increaseAmount(uint256 _value) external payable nonReentrant {
```

- Line 326:

[https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L326C5-L326](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L326C5-L326C48)

```solidity
function withdraw() external nonReentrant {
```

- Line 356:

[https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L356](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L356)

```solidity
function delegate(address _addr) external nonReentrant {
```

## Risk

No identified risk, since no external contract calls are made in these functions. However there is the cost of computation from running the modifiers during each call, thus increasing gas costs.

## Mitigation

Remove the modifiers from the function definitions.

# Many cases of precision loss from multiply-after-divide

While there wasn’t enough time to explore possible attack vectors for these cases, it’s apparent that there is definite loss in precision while calculating important values in the system.  

- From `_checkpoint()` in `VotingEscrow.sol`, lines 129 - 132:

[https://github.com/code-423n4/2023-08-verwa/blob/9a2e7be003bc1a77b3b87db31f3d5a1bcb48ed32/src/VotingEscrow.sol#L129-L132](https://github.com/code-423n4/2023-08-verwa/blob/9a2e7be003bc1a77b3b87db31f3d5a1bcb48ed32/src/VotingEscrow.sol#L129-L132)

```solidity
if (_oldLocked.end > block.timestamp && _oldLocked.delegated > 0) {
    userOldPoint.slope = _oldLocked.delegated / int128(int256(LOCKTIME));
    userOldPoint.bias = userOldPoint.slope * int128(int256(_oldLocked.end - block.timestamp));
}
```

- From `_checkpoint()` in `VotingEscrow.sol`, lines 133 - 136:

[https://github.com/code-423n4/2023-08-verwa/blob/9a2e7be003bc1a77b3b87db31f3d5a1bcb48ed32/src/VotingEscrow.sol#L133-L136](https://github.com/code-423n4/2023-08-verwa/blob/9a2e7be003bc1a77b3b87db31f3d5a1bcb48ed32/src/VotingEscrow.sol#L133-L136)

```solidity
if (_newLocked.end > block.timestamp && _newLocked.delegated > 0) {
	userNewPoint.slope = _newLocked.delegated / int128(int256(LOCKTIME));
	userNewPoint.bias = userNewPoint.slope * int128(int256(_newLocked.end - block.timestamp));
}
```

- From `_checkpoint()` in `VotingEscrow.sol`, lines 176 - 218

[https://github.com/code-423n4/2023-08-verwa/blob/9a2e7be003bc1a77b3b87db31f3d5a1bcb48ed32/src/VotingEscrow.sol#L176-L218](https://github.com/code-423n4/2023-08-verwa/blob/9a2e7be003bc1a77b3b87db31f3d5a1bcb48ed32/src/VotingEscrow.sol#L176-L218)

```solidity
uint256 blockSlope = 0; // dblock/dt
if (block.timestamp > lastPoint.ts) {
    blockSlope = (MULTIPLIER * (block.number - lastPoint.blk)) / (block.timestamp - lastPoint.ts);
}

// Go over weeks to fill history and calculate what the current point is
uint256 iterativeTime = _floorToWeek(lastCheckpoint);
for (uint256 i = 0; i < 255; i++) {
     ...

    // PRECISION LOSS HERE: blockslope was originally calculated w/ a division, now it's multiplied further
    lastPoint.blk = initialLastPoint.blk + (blockSlope * (iterativeTime - initialLastPoint.ts)) / MULTIPLIER;

     ...
}
```

- From `_checkpoint_lender()` in LendingLedger.sol, line 60:

[https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L60](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L60)

```solidity
uint256 currEpoch = (block.timestamp / WEEK) * WEEK;
```

## Risk

Precision loss in critical calculations can make the risk/reward for investors less appealing.  In critical cases there could be possible losses involved when these discrepancies are exploited.

## Mitigation

Reorder operations to ensure division happens before multiplication in the listed instances.

# Inconsistent Use of NatSpec

NatSpec is a boon to all Solidity developers. Not only does it provide a structure for developers to document their code within the code itself, it encourages the practice of documenting code. When future developers read code documented with NatSpec, they are able to increase their capacity to understand, upgrade, and fix code. Without code documented with NatSpec, that capacity is hindered.

The veRWA codebase does have a high level of documentation with NatSpec. However there are numerous instances of functions missing NatSpec.

### Risks

1. Lack of understanding of code without adequate documentation.
2. Difficulty in understanding, upgrading, and fixing code without documentation.

### Recommendation

1. Practice consistent use of NatSpec on all parts of the project codebase.
2. Include the need for NatSpec comments for code reviews to pass.

# Unnecessary or Uncomplemented receive function

Most functions that handle accounting for funds in smart contracts typically have a complementary function to provide the opposite operation.  In the case of `LendingLedger.sol`, there is the `receive` function, however there is no related accounting in that function, or a way to recover funds sent to that contract.

[https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L209](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L209)

```solidity
receive() external payable {} // No way to get funds out, no accounting for balance in contract
```

## Risk

Any CANTO deposited in the contract will be locked there because there is no method to transfer it back to the depositor. 

## Mitigation

Remove the receive function.

# Old version of OpenZeppelin Contracts used

Using old versions of 3rd-party libraries in the build process can introduce vulnerabilities to the protocol code. 

- Latest is 4.9.3 (as of July 28, 2023), version used in project is 4.9.2

### Risks

1. Older versions of OpenZeppelin contracts might not have fixes for known security vulnerabilities.
2. Older versions might contain features or functionality that have been deprecated in recent versions.
3. Newer versions often come with new features, optimizations, and improvements that are not available in older versions.

## Recommendation

Review the latest version of the OpenZeppelin contracts and upgrade

# Error Messages don’t match intent

Meaningful error messages give better handles to test against. While building a test suite, edge cases can become numerous, and providing descriptive error messages allows not just for the project developers to write better tests, it helps developers of 3rd-party integrations and forks (other protocols, token projects, etc) to understand the intentions and reasoning of the parent project.

In the veRWA codebase, there are places where the error cases are materially different from the guards they are included with. 

- From `VotingEscrow.sol`, line 573:

[https://github.com/code-423n4/2023-08-verwa/blob/9a2e7be003bc1a77b3b87db31f3d5a1bcb48ed32/src/VotingEscrow.sol#L573](https://github.com/code-423n4/2023-08-verwa/blob/9a2e7be003bc1a77b3b87db31f3d5a1bcb48ed32/src/VotingEscrow.sol#L573)

```solidity
require(_blockNumber <= block.number, "Only past block number"); //Should be “Only before block number”
```

## Risk

1. Project developers get the wrong understanding of why a case is erroneous.
2. Writing tests for specific error types becomes difficult because of a lack of unique errors provided by the executed code.
3. 3rd-party developers (forks, integrations) can have difficulties understanding developer intentions.

## Recommendation

Use more descriptive handler names and error messages and limit reusing error messages for categorically-similar-yet-different errors.

# Function Names don’t match intent

`totalSupply`, `totalSupplyAt`, `supplyAt` in `VotingEscrow.sol` refer to voting power, not locked supply.  This causes confusion to developers or auditors onboarding to the codebase.

# Dead Code

In many cases, dead code can create attack surfaces for malicious exploits. In the case for `VotingEscrow.sol`, there are instances of dead and unused code, though the risks are much lower.

### Unused assignment

- From `_checkpoint()` in `VotingEscrow.sol`, line 206:

[https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L206](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L206)

```solidity
lastCheckpoint = iterativeTime; // <= this is never used
```

### Unused Enums

Ex. VotingEscrow.sol, line 62 and 64

[https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L62](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L62)

[https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L64](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L64)

```solidity
enum LockAction {
		...
    INCREASE_TIME, // <= never used
    QUIT, // <= never used
		...
}
```

## Risk

While no attack vectors were identified from these instances of dead code, there is a definite increase in gas usage during execution and deployment.

## Mitigation

Remove the instances of dead code from the codebase.

# TODOs included in code/comments

Ex. `LendingLedger.sol`, line 48 and `GaugeController.sol` line 59

[https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L48](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L48)

[https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L59](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L59)

```solidity
governance = _governance; // TODO: Maybe change to Oracle
```

# Reference to non-existent documentation

The references to IVotingEscrow are misleading because there is no IVotingEscrow anywhere in the codebase.  This makes it difficult to trust comments in the codebase, let alone find the referenced documentation that may enlighten the developer or auditor.

Examples: 

- [https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L267](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L267)
- [https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L2](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L267)86
- [https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L267)325
- [https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L355](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L355)
- [https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L472](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L472)[https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L486](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L486)