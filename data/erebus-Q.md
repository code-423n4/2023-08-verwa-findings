# Lows
## [L-01] Lack of methods to recover dust
There is a `receive() payable` function which implies funds can be sent directly to the [LendingLedger.sol](https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L209) (AKA dust) and the only way to recover funds is throuh the [claim](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L152) function, which does not take into account this behavior. That means, the dust in the contract is locked. Consider adding a method to recover dust sent to the contract, like

```
function recoverDust(address target, uint256 amount) public onlyGovernance {
    require(target, "recoverDust -> target == 0");
    (bool success, ) = target.call{value: amount}("");
    require(success, "recoverDust -> Failed call");
}
```

# Non criticals
## [NC-01] Redundant code

CHECK => [LendingLedger.sol#L63](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L63)

CHECK => [LendingLedger.sol#L86](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L86)

Both lines are calculating `updateUntilEpoch` before the `if-else` clause which is just used on the `else` one. Consider moving that calculation to the `else` clause to be consistent with the expected "workflow" (that is, don't do unnecessary things) 

## [NC-02] Use `weeks` instead of `days` when defining "full-weeks" constants
In [VotingEscrow](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L31) it defines the constant `WEEK` as `7 days`, instead of the builtin solidity definition. Rely on them for basic dates instead of hardcoding them as days (readiness). For example, this does not apply to `LOCKTIME`

```
uint256 public constant WEEK = 7 days; <================ shall be 1 weeks which is more explicit
uint256 public constant LOCKTIME = 1825 days; <======== fine
```

The same in [GaugeController.sol#L16](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L16)

## [NC-03] Misplaced comment
Retrieving the last user points from the Voting Scrow in the [GaugeController.sol#L215-L220](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L215-L220) is done by ommiting the `bias` and `ts` returned values. For readiness, they are written as comments, but the `/*int128 bias*/` one is placed within `slope_` instead of before the comma. Consider putting the `/*int128 bias*/` before the comma:

```
       (
            /*int128 bias*/,
            int128 slope_, 
            /*uint256 ts*/

        ) = ve.getLastUserPoint(msg.sender);
```

instead of the current implementation

```
        (
            ,
            /*int128 bias*/
            int128 slope_, /*uint256 ts*/

        ) = ve.getLastUserPoint(msg.sender);
```