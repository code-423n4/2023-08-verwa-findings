In the contract VotingEscrow.sol line 273 there is a check:
require(msg.value == _value, "Invalid value");

in the function:
function createLock(uint256 _value) external payable nonReentrant{...}

This check is not necessary, you can use directly msg.value.