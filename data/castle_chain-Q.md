## adding a function to calculate the current epoch to increase the modularity of the code 
as shown here the current epoch calculated several times in the contracts 
https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L60
```
        uint256 currEpoch = (block.timestamp / WEEK) * WEEK;
```
https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L84

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L134

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L162

```
function currentEpoch() private returns (uint currEpoch) {
            currEpoch = (block.timestamp / WEEK) * WEEK;
}
```

## `type(uint256).max` can be stored as a constant 

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L37

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L133

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L139

```
uint256 constant MAX_UINT256 = type(uint256).max ; 
```

## should using modifier `nonreenterant` form openzeppelin library in function `claim` , to prevent reenterancy attacks 

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L152-L182

## should adding `moreThanZeroModifier` instead of check it every time to enhance the modularity 
https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L129

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L268-L284 

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L288

## should check if the _claimFromTimestamp is bigger than _claimToTimestamp  in the function `claim` 

https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L152-L156

## create a function `_floorToWeek()` in the `lendingLeadger` contract to enhance the modularity of the contract 

```
    function _floorToWeek(uint256 _t) internal pure returns (uint256) {
        return (_t / WEEK) * WEEK;
    }
```
https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L162



