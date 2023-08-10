The following _value argument in VotingEscrow.sol L268,L288 can be removed since they are payable,
```
   function createLock(uint256 _value) external payable nonReentrant
   function increaseAmount(uint256 _value) external payable nonReentrant 
```

The functions can use msg.value directly and wont need to use the following modifier
```
require(msg.value == _value, "Invalid value");
```