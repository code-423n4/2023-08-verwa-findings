# [G-01] No real need to use the `_value` parameter if the data will be fully dependent on the `msg.value` in the `VotingEscrow::createLock()`
- The [`VotingEscrow::createLock()` receives as a parameter the value that will be used for the lock](https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L268), but the value of this parameter must be equals to the `msg.value`, otherwise the tx will be reverted.
  - In these scenarios when a parameter must match the msg.value there is not really a point on sending the data as a parameter, the value can be read directly from the msg.value
### Current Implementation
```solidity
function createLock(uint256 _value) external payable nonReentrant {
    uint256 unlock_time = _floorToWeek(block.timestamp + LOCKTIME); // Locktime is rounded down to weeks
    LockedBalance memory locked_ = locked[msg.sender];
    // Validate inputs
    require(_value > 0, "Only non zero amount");

    //@audit => _value must be exactly equals to msg.value
    require(msg.value == _value, "Invalid value");

    require(locked_.amount == 0, "Lock exists");
    // Update lock and voting power (checkpoint)
    locked_.amount += int128(int256(_value));
    locked_.end = unlock_time;
    locked_.delegated += int128(int256(_value));
    locked_.delegatee = msg.sender;
    locked[msg.sender] = locked_;
    _checkpoint(msg.sender, LockedBalance(0, 0, 0, address(0)), locked_);

    emit Deposit(msg.sender, _value, unlock_time, LockAction.CREATE, block.timestamp);
}
```


### Suggested Refactored
```solidity
- function createLock(uint256 _value) external payable nonReentrant {
+ function createLock() external payable nonReentrant {
    uint256 unlock_time = _floorToWeek(block.timestamp + LOCKTIME); // Locktime is rounded down to weeks
    LockedBalance memory locked_ = locked[msg.sender];
    // Validate inputs
-   require(_value > 0, "Only non zero amount");
+   require(msg.value > 0, "Only non zero amount");
    
-   require(msg.value == _value, "Invalid value");
    require(locked_.amount == 0, "Lock exists");
    // Update lock and voting power (checkpoint)
-   locked_.amount += int128(int256(_value));
+   locked_.amount += int128(int256(msg.value));
    locked_.end = unlock_time;
-   locked_.delegated += int128(int256(_value));
+   locked_.delegated += int128(int256(msg.value));
    locked_.delegatee = msg.sender;
    locked[msg.sender] = locked_;
    _checkpoint(msg.sender, LockedBalance(0, 0, 0, address(0)), locked_);

-   emit Deposit(msg.sender, _value, unlock_time, LockAction.CREATE, block.timestamp);
+   emit Deposit(msg.sender, msg.value, unlock_time, LockAction.CREATE, block.timestamp);
}
```