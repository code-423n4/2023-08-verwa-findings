
1.Not necessary to use `nonReentrant`
https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L268

2.Should ensure `_timestamp` is greater than zero
https://github.com/code-423n4/2023-08-verwa/tree/main/src/LendingLedger.sol#L36#L39

```solidity
    modifier is_valid_epoch(uint256 _timestamp) {
-       require(_timestamp % WEEK == 0 || _timestamp == type(uint256).max, "Invalid timestamp");
+       require( (_timestamp > 0 && _timestamp % WEEK == 0) || _timestamp == type(uint256).max, "Invalid timestamp");
        _;
    }
```
3.`msg.sender` receive token can be marked as `payable`
https://github.com/code-423n4/2023-08-verwa/tree/main/src/LendingLedger.sol#L179