# VULN 1 

## [LOW] Empty receive()/payable fallback() function does not authenticate requests
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 209 at 2023-08-verwa/src/LendingLedger.sol:

    receive() external payable {}

------------------------------------------------------------------------ 

### Mitigation 

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. require(msg.sender == address(weth))).Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas. Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds. If the concern is having to spend a small amount of gas to check the sender against an immutable address, the code should at least have a function to rescue unused Ether.










# VULN 2 

## [LOW] Division before multiplication causing significant loss of precision
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 60 at 2023-08-verwa/src/GaugeController.sol:

        uint256 last_epoch = (block.timestamp / WEEK) * WEEK;


Found in line 153 at 2023-08-verwa/src/GaugeController.sol:

        uint256 t = (_time / WEEK) * WEEK;


Found in line 191 at 2023-08-verwa/src/GaugeController.sol:

        uint256 next_time = ((block.timestamp + WEEK) / WEEK) * WEEK;


Found in line 224 at 2023-08-verwa/src/GaugeController.sol:

        uint256 next_time = ((block.timestamp + WEEK) / WEEK) * WEEK;


Found in line 60 at 2023-08-verwa/src/LendingLedger.sol:

        uint256 currEpoch = (block.timestamp / WEEK) * WEEK;


Found in line 84 at 2023-08-verwa/src/LendingLedger.sol:

        uint256 currEpoch = (block.timestamp / WEEK) * WEEK;


Found in line 134 at 2023-08-verwa/src/LendingLedger.sol:

        uint256 currEpoch = (block.timestamp / WEEK) * WEEK;


Found in line 162 at 2023-08-verwa/src/LendingLedger.sol:

        uint256 currEpoch = (block.timestamp / WEEK) * WEEK;

------------------------------------------------------------------------ 

### Mitigation 

Multiply first before dividing to keep the precision.










# VULN 3 

## [LOW] Open TODOs
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 59 at 2023-08-verwa/src/GaugeController.sol:

        governance = _governance; // TODO: Maybe change to Oracle


Found in line 48 at 2023-08-verwa/src/LendingLedger.sol:

        governance = _governance; // TODO: Maybe change to Oracle

------------------------------------------------------------------------ 

### Mitigation 

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment.










# VULN 4 

## [LOW] Add parameter to event-emit
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 23 at 2023-08-verwa/src/VotingEscrow.sol:

    event Unlock();

------------------------------------------------------------------------ 

### Mitigation 

Some event-emit description doesn’t have parameter. Add parameter for front-end website or client app, they can has that something has happened on the blockchain.
