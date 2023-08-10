## [L-01] Potential DoS in the `VotingEscrow#increaseAmount` from expired delegatee lock

### Description

In the `increaseAmount` function, if a user's lock is delegated to a delegatee whose lock has expired, the function will revert. This prevents the user from increasing their lock amount until they change their delegatee.

### Recommendation

Provide a mechanism to allow users to increase their lock amount even if their delegatee's lock has expired. This can be done by automatically undelegating the user's lock back to the user.

## [L-02] Incorrect unlock time in the Deposit event

### Description

In the `increaseAmount` function, the `Deposit` event is emitted with the incorrect `unlockTime` for the user. The event uses the old unlock time instead of the new one.

### Recommendation

Ensure that the `Deposit` event emits the correct unlock time.

```diff
- uint256 unlockTime = locked_.end;
LockAction action = LockAction.INCREASE_AMOUNT;
LockedBalance memory newLocked = _copyLock(locked_);
newLocked.amount += int128(int256(_value));
newLocked.end = _floorToWeek(block.timestamp + LOCKTIME);
+ uint256 unlockTime = newLocked.end;
```

## [L-03] Missing check for valid gauge address in the `GaugeController#change_gauge_weight`

### Description

In the `change_gauge_weight` function, there's no check to ensure that the provided gauge address is valid before changing its weight.

### Recommendation

Add a `require` statement to check if the provided `_gauge` address is a valid gauge. This will prevent potential misuse or errors.

```diff
function change_gauge_weight(address _gauge, uint256 _weight) public onlyGovernance {
+     require(isValidGauge[_gauge], "Invalid gauge address");
```

## [N-01] Inconsistent naming conventions

### Description

Throughout the contracts, there are variable and function names that are more reminiscent of Vyper naming conventions (e.g., `unlock_time`, `uepoch`) rather than the typical camelCase naming convention in Solidity.

### Recommendation

To maintain consistency and readability, rename these variables and functions to follow the camelCase convention.

For example:

```diff
- uint256 unlock_time = ...
+ uint256 unlockTime = ...
```

```diff
- function vote_for_gauge_weights(...)
+ function voteForGaugeWeights(...)
```

## [N-02] Within the `LockAction` enum, the `INCREASE_TIME` and `QUIT` are not used

### Description

In the `VotingEscrow` contract, the `LockAction` enum has two values `INCREASE_TIME` and `QUIT` which are not being used in the contract. These are just remnants of an earlier version of the contract.

### Recommendation

Remove unused enum values.

```diff
enum LockAction {
    CREATE,
    INCREASE_AMOUNT,
    INCREASE_AMOUNT_AND_DELEGATION,
-   INCREASE_TIME,
    WITHDRAW,
-   QUIT,
    DELEGATE,
    UNDELEGATE
}
```

## [N-03] Missing `IVotingEscrow` interface with documentation about its contents

### Description

The contract has comments indicating a reference to the `IVotingEscrow` interface, but this interface is not provided in the given code. This makes it challenging to understand the expected behavior and interface of the `VotingEscrow` contract.

### Recommendation

Ensure that the `IVotingEscrow` interface is included when sharing or deploying the contract. This aids in understanding the expected methods and their signatures.

## [N-04] Incorrect NatSpec comment use

### Description

Throughout the `VotingEscrow` contract, single-line comments using `//` are used for function documentation instead of the appropriate NatSpec format using `///` or `/** */`.

Using the correct format is important for generating the contract's interface specification and documentation, ensuring clarity and consistency.

### Recommendation

Replace single-line comments `//` with the appropriate NatSpec format `///` for single-line comments or `/** */` for multi-line comments before function definitions.

For instance:

```diff
- // @dev Floors a timestamp to the nearest weekly increment
+ /// @dev Floors a timestamp to the nearest weekly increment
```

Similarly, make similar adjustments throughout the contract to conform to the NatSpec format.

## [N-05] Redundant check for non-negative user weight in `GaugeController#vote_for_gauge_weights`

### Description

In the `vote_for_gauge_weights` function, a check is present to ensure the `_user_weight` is non-negative. However, `_user_weight` is of type `uint256`, which is always non-negative.

```solidity
require(_user_weight >= 0 && _user_weight <= 10_000, "Invalid user weight");
```

### Recommendation

The check `_user_weight >= 0` is redundant and can be safely removed.

```diff
- require(_user_weight >= 0 && _user_weight <= 10_000, "Invalid user weight");
+ require(_user_weight <= 10_000, "Invalid user weight");
```