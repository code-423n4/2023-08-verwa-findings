# [01] Lack of events is error prone

**Description**
Events are used by users and other systems to reliably detect protocol state for integration, market information and exploit detection purposes.

There are some critical operations that do not emit events:
- Vote for gauge weight: [GaugeController.vote_for_gauge_weights()](https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/GaugeController.sol#L211)
- Claim: [LendingLedger.claim()](https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/LendingLedger.sol#L179)
- Whitelist lending market: [LendingLedger.whiteListLendingMarket()](https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/LendingLedger.sol#L206)

**Recommendation**
Implement the listed events to improve contract transparency.

# [02] Lack of documentation is error prone

**Description**
While much of the code is based on well-established protocols, the various modifications and specializations would benefit from clear documentation about the broader context, types of users, interactions with and between contracts, arithmetic properties and access controls. The lack of documentation makes the protocol difficult to review.

**Recommendation**
Fully document the solution

# [03] No incentive to vote early in `GaugeController`

**Description**
*This issue was identified in the original Curve Finance audit, finding ID TOB-CURVE-DAO-017. It remains unresolved in the `mkt.market` implementation.*

`GaugeController` voting offers no incentive to vote early, so late-voting users have a benefit over early voters.

Given that votes are public, earlier voters reveal information to other voters. An unethical voter could wait until the last moment to vote, and calculate the minimal effective vote. Effectively, earlier voters have to use more power for the same effect as last-minute voters.

**Recommendation**
Consider using a decreasing vote weight to compensate early voters for revealing their intentions, or use a blind voting protocol.

# [04] Inconsistent use of `user` and `gauge` in tests

Scope: While the tests are out of scope for the audit, the test still effectively form part of the documentation for the project.

The tests setup 2 test `user` and `gauge` addresses each:
```solidity
/home/rjs/Desktop/code-423n4/2023-08-verwa/src/test/GaugeController.t.sol:27
27:         users = utils.createUsers(5);
28:         (gov, user1, user2, gauge1, gauge2) = (users[0], users[1], users[2], users[3], users[4]);
```

But the test code mixes the usage of addresses, which makes it difficult to understand the intention behind the test.

For example, in the below test, `user1`'s address is the gauge:
```solidity
src/test/GaugeController.t.sol:34
34:     function testAddGauge() public {
35:         assertTrue(!gc.isValidGauge(user1));
36:         vm.startPrank(gov);
37:         gc.add_gauge(user1);
38:         vm.stopPrank();
39:         assertTrue(gc.isValidGauge(user1));
40:     }
```

Whereas in this test, `user1` is the user:
```solidity
src/test/GaugeController.t.sol:159
159:     function testVoteGaugeWeight50Pcnt() public {
160:         // vote_for_gauge_weights valid vote 50%
161:         // Should vote for gauge and change weights accordingly
162:         vm.startPrank(gov);
163:         gc.add_gauge(gauge1);
164:         gc.add_gauge(gauge2);
165:         gc.change_gauge_weight(gauge1, 50);
166:         gc.change_gauge_weight(gauge2, 50);
167:         vm.stopPrank();
168: 
169:         vm.startPrank(user1);
170:         ve.createLock{value: 1 ether}(1 ether);
171:         gc.vote_for_gauge_weights(gauge1, 5000); // vote 50% for gauge1
172:         gc.vote_for_gauge_weights(gauge2, 5000); // vote 50% for gauge2
173: 
174:         assertEq((gc.get_total_weight() * 5000) / 10000, gc.get_gauge_weight(gauge1));
175:     }
```