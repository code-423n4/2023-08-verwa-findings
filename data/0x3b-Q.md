### **[L-01]** People can lock their tokens for slightly less that 5 years
[createLock](https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L269)'s unlock_time is rounded down to the nearest week with this equation. 
```jsx
 uint256 unlock_time = _floorToWeek(block.timestamp + LOCKTIME); 
``` 
Which specifically rounds down the time to the start of the week. This means that a lock that starts on Monday morning will have the same unlock time as the one on Sunday afternoon (+6 days and 16 hours).

### **[R-01]** Lock time is wrong
[Lock time](https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L32) in uses 1825 days to represent 5 years, which does not account that 1 year is 365 days and 6 hours. 1826 will be close, while 1826 + 6H will be the exact days in 5 years.

