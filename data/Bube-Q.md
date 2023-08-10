# Non-critical issues

## [NC-01] Missing events in sensitive function

## Summary
Events should be emitted when sensitive changes are made to the contracts, but some functions lack them.
The contract `GaugeController.sol` does not emit events for critical state change, when the gauge weight is changed, an event should be emitted. This will make it easier to track changes and debug issues.

## Reference code
https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L204-L206

## Recommendation
Emit events for critical state changes.

# Low issues

## [L-01] Use of tautology

## Summary
Thera are expressions in smart contract `GaugeController.sol` that are tautologies. There are 2 instances of this issue.

## Reference code
`File: src/GaugeController.sol`
`212: require(_user_weight >= 0 && _user_weight <= 10_000, "Invalid user weight");`
https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L212

`241: require(power_used >= 0 && power_used <= 10_000, "Used too much power");`
https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L241

`_user_weight` and `power_used` are uint256, so `_user_weight >= 0` and `power_used >= 0` will be always true.

## Recommendation
Fix the incorrect comparison by changing the value type or the comparison.

## [L-02] Unnecessary nonReentrant modifier

## Summary
If a method does not have an external call then it is impossible to reenter, so you can skip this modifier in such methods. There are 3 instances of this issue in smart contract `VotingEscrow.sol`.

## Reference code

`File: src/VotingEscrow.sol`

`268: function createLock(uint256 _value) external payable nonReentrant {`
https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L268

`288: function increaseAmount(uint256 _value) external payable nonReentrant {`
https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L288

`356: function delegate(address _addr) external nonReentrant {`
https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L356

## Recommendation

In smart contract `VotingEscrow.sol` in functions `createLock`, `increaseAmount` and `delegate` is used nonReentrant modifier. But these methods does not have an external call. The modifier would add unnecessary gas costs and code complexity without providing any additional security. So, it is unnecessary to use nonReentrant modifier.




