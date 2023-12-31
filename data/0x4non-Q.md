# Lows

## L-01 Governance cant be change

The governance setted on [LendingLedger.sol#L48](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L48) and [GaugeController.sol#L59](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L59) can be changed or transfered.

A good idea might be just to simple use a [`Ownable2Step`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol) pattern, and replace `onlyGovernance` with `onlyOwner`, and clarify that the owner is the governance.

## L-02 Several loops could not execute due to gas limitation

The codebase relies on several loops that can iterate hundreds of times with costly gas
consumption. This design is error-prone and may cause the contract to be trapped because
it runs out of gas.
For example, in `GaugeController:_get_sum()` and `VotingEscrow:_checkpoint` functions both have
loops that can be iterated hundreds of times while changing state:
[GaugeController.sol#L69](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L69)
```solidity
    function _get_sum() internal returns (uint256) {
        uint256 t = time_sum;
        Point memory pt = points_sum[t];
        for (uint256 i; i < 500; ++i) {
```

[VotingEscrow.sol#L184-L185](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L184-L185)
```solidity
        uint256 iterativeTime = _floorToWeek(lastCheckpoint);
        for (uint256 i = 0; i < 255; i++) {
```

These loops have code that writes state variables, which is the operation that consumes the most gas.
Both loops are executed with every interaction of the contract. 
Recommendation update GaugeController logic and VotingEscrow to work with a large number of periods.

## L-03 No incentive to vote early in [`GaugeController`](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L211)
GaugeController voting offers no incentive to vote early, so late-voting users have a
benefit over early voters.
Sinces all the votes are public, users who vote earlier are penalized because their votes are
known by the other participants. An attacker can learn exactly how many tokens are
necessary to change the outcome of the voting just before it ends.
Consider:
- Using a decreasing weight to create an early voting advantage
- Using a blind vote Long term, properly document and test the voting process and closely follow the
community’s progress regarding on-chain voting.

# L-04 GaugeController allows for quick vote voting strategy
This means that an attacker can wait to the last moment to submit their votes manipulating the outcome. Consider add a voting weight decreasing over time.

# NonCritical

## N-01 Avoid setting extra variable
On [GaugeController.sol#L60-L61](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L60-L61) instead of:
```solidity
        uint256 last_epoch = (block.timestamp / WEEK) * WEEK;
        time_sum = last_epoch;
```

You can simple do:
```solidity
        // @dev time_sum is last epoch
        time_sum = (block.timestamp / WEEK) * WEEK;
```

## N-02 `claim` can be simplefied

The method `claim` on [LendingLedger.sol#L152-L182](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L152-L182) can be simplified:
```diff
diff --git a/src/LendingLedger.sol b/src/LendingLedger.sol
index 145a5b8..0f949ce 100644
--- a/src/LendingLedger.sol
+++ b/src/LendingLedger.sol
@@ -162,19 +162,22 @@ contract LendingLedger {
         uint256 currEpoch = (block.timestamp / WEEK) * WEEK;
         uint256 claimStart = Math.max(userLastClaimed, _claimFromTimestamp);
         uint256 claimEnd = Math.min(currEpoch - WEEK, _claimUpToTimestamp);
+        if (claimEnd < claimStart) {
+            // Nothing to
+            return;
+        }
+
         uint256 cantoToSend;
-        if (claimEnd >= claimStart) {
-            // This ensures that we only set userClaimedEpoch when a claim actually happened
-            for (uint256 i = claimStart; i <= claimEnd; i += WEEK) {
-                uint256 userBalance = lendingMarketBalances[_market][lender][i];
-                uint256 marketBalance = lendingMarketTotalBalance[_market][i];
-                RewardInformation memory ri = rewardInformation[i];
-                require(ri.set, "Reward not set yet"); // Can only claim for epochs where rewards are set, even if it is set to 0
-                uint256 marketWeight = gaugeController.gauge_relative_weight_write(_market, i); // Normalized to 1e18
-                cantoToSend += (marketWeight * userBalance * ri.amount) / (1e18 * marketBalance); // (marketWeight / 1e18) * (userBalance / marketBalance) * ri.amount;
-            }
-            userClaimedEpoch[_market][lender] = claimEnd + WEEK;
+        // This ensures that we only set userClaimedEpoch when a claim actually happened
+        for (uint256 i = claimStart; i <= claimEnd; i += WEEK) {
+            uint256 userBalance = lendingMarketBalances[_market][lender][i];
+            uint256 marketBalance = lendingMarketTotalBalance[_market][i];
+            RewardInformation memory ri = rewardInformation[i];
+            require(ri.set, "Reward not set yet"); // Can only claim for epochs where rewards are set, even if it is set to 0
+            uint256 marketWeight = gaugeController.gauge_relative_weight_write(_market, i); // Normalized to 1e18
+            cantoToSend += (marketWeight * userBalance * ri.amount) / (1e18 * marketBalance); // (marketWeight / 1e18) * (userBalance / marketBalance) * ri.amount;
         }
+        userClaimedEpoch[_market][lender] = claimEnd + WEEK;
         if (cantoToSend > 0) {
             (bool success, ) = msg.sender.call{value: cantoToSend}("");
             require(success, "Failed to send CANTO");
```

## NC-3 secs to EPOCH should be a library

There is a pattern that repeats all over:
`(timeInSecods / WEEK) * WEEK`
- [GaugeController.sol#L60](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L60)
- [GaugeController.sol#L153](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L153)
- [GaugeController.sol#L191](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L191)
- [GaugeController.sol#L224](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L224)
- [LendingLedger.sol#L60](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L60)
- [LendingLedger.sol#L84](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L84)
- [LendingLedger.sol#L134](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L134)
- [LendingLedger.sol#L162](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L162)

This pattern should be extracted to a simple library or use a function as you do on [VotingEscrow.sol#L424-L426](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L424-L426)

## NC-4 Abuse of `nonReentrant` modifier

The nexts functions follow the CEI pattern, this means that there is no need to use the `nonReentrant` modifier:
- `createLock` [VotingEscrow.sol#L268](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L268)
- `increaseAmount` [VotingEscrow.sol#L288](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L288)
- `withdraw` [VotingEscrow.sol#L326](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L326)
- `delegate` [VotingEscrow.sol#L356](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L356)
