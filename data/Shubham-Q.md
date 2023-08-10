## Low Risk Issue

| |Issue|Instances|
|-|:-|:-:|
| [L-01](#L-01) | Leap Year not accounted for in LOCKTIME | 1 |


### [L-01] Leap Year not accounted for in LOCKTIME

The LOCKTIME global variable is calculated taking 365 days a year for 5 years into consideration.
But Leap Year occurs every 4 years. So it has to occur atleast once in the duration of LOCKTIME.
Therefore the value of LOCKTIME should be minimum 1826 instead of 1825.

#### Recommeded Step

```solidity
File: VotingEscrow.sol

-        uint256 public constant LOCKTIME = 1825 days;
+        uint256 public constant LOCKTIME = 1826 days;
```