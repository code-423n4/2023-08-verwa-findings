## Summary

The number 255 is not an acurate representation of how many weeks are in 1825 days / 5 years period.

## Vulnerability Details

Within the _checkpoint (a) and _supplyAt (b) functions in VotingEscrow.sol there is a for loop that:

(a) Go over weeks to fill history and calculate what the current point is. [_checkpoint](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L185)
(b) Iterates through all weeks between _point & _t to account for slope changes. [_supplyAt](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L538C44-L538C44)

The number 255 has been chosen in order to represent all the possible weeks. But given that the LOCKTIME is 1825 days (5 years) the better number to use here is 260 (1825/7 floored). In extreme edge cases not using 260 can generate unexpected outcomes like missing out some weeks data.

## Recommendations
Use 260 to match the correct amount of weeks in a 5 years period.