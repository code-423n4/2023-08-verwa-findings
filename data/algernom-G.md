# Avoid addition assignment operator on loops with large ranges
### Summary
Use addition assignment operator (`+=`) uses more gas than regular assignment.

### Details
In multiple places in the codebase, the addition assignment operator is used to add some value/variable to another.

This is fine in most places but in code blocks where a range of values that a loop will execute over could be large, it would be better gas optimisation to use regular value addition and assignment (i.e. `x = x + y`) without sacrificing code readability.

### Code snippets
- https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/GaugeController.sol#L69
- https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/GaugeController.sol#L95
- https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/LendingLedger.sol#L174C17-L174C17

### Recommended mitigation
On functions that loop over large ranges, use regular addition with variable names (e.g. `x = x+y`) over addition assignment (`x += y`).

This is already implemented in some areas of the code e.g. 

```js
/// Excerpt from _checkpoint function in VotingEscrow.sol
...
for (uint256 i = 0; i < 255; i++) {
    // Hopefully it won't happen that this won't get used in 5 years!
    // If it does, users will be able to withdraw but vote weight will be broken
->  iterativeTime = iterativeTime + WEEK; // avoids addition assignment e.g iterativeTime += WEEK
    int128 dSlope = 0;
    if (iterativeTime > block.timestamp) {
         iterativeTime = block.timestamp;
    } else {
        dSlope = slopeChanges[iterativeTime];
    }
...
```
https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L188

and should be extended throughout the codebase.

# Use named return variable when function returns

### Summary
Use named return variable when function returns
### Details
When a function returns a value, you can set the type for the return and name the returned variable in the function declaration.

Using named return function variables saves gas on the variable that is returned during the functions execution.

The codebase sporadically does and doesn't make use of named return variables.
### Code snippets
- https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L431
- https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L451

### Recommended mitigation
Where a function returns value, if this value can be named and is calculated in the scope of the function, set the type of the return also it's name in the function declaration and not in the function code block. Example:

```js
- // function _findBlockEpoch(uint256 _block, uint256 _maxEpoch) internal view returns (uint256) {
+ function _findBlockEpoch(uint256 _block, uint256 _maxEpoch) internal view returns (uint256 min) {
    // Binary search
-   //  uint256 min = 0; // uint256 default is 0, no need to init
    uint256 max = _maxEpoch;
    // Will be always enough for 128-bit numbers
    for (uint256 i = 0; i < 128; i++) {
        if (min >= max) break;
        uint256 mid = (min + max + 1) / 2;
        if (pointHistory[mid].blk <= _block) {
            min = mid;
        } else {
            max = mid - 1;
	    }
    }
    return min;
}
```