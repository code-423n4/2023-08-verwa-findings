# Analysis  - veRWA
### Summary
| List |Head |Details|
|:--|:----------------|:------|
|a) |The approach I followed when reviewing the code | Stages in my code review and analysis |
|b) |Test analysis | Test scope of the project and quality of tests |
|c) |Documents  | What is the scope and quality of documentation for Users and Administrators? |
|d) |Other Audit Reports and Automated Findings | What are the previous Audit reports and their analysis |
|e) |Gas Optimization | Gas usage approach of the project and alternative solutions to it |


## a) The approach I followed when reviewing the code

First, by examining the scope of the code, I determined my code review and analysis strategy.
https://github.com/code-423n4/2023-08-verwa#scope
Accordingly, I analyzed and audited the subject in the following steps;

| Number |Stage |Details|Information|
|:--|:----------------|:------|:------|
|1|Compile and Run Test|[Installation](https://github.com/code-423n4/2023-08-verwa#tests)|Test and installation structure is simple, cleanly designed|
|2|Architecture Review| [veRWAl](https://github.com/code-423n4/2023-08-verwa#overview) |Provides a basic architectural teaching for General Architecture|
|3|Graphical Analysis  |Graphical Analysis with [Solidity-metrics](https://github.com/ConsenSys/solidity-metrics)|A visual view has been made to dominate the general structure of the codes of the project.|
|4|Slither Analysis  | [Slither](https://github.com/crytic/slither)| |
|5|Test Suits|[Tests](https://github.com/code-423n4/2023-08-verwa#tests)|In this section, the scope and content of the tests of the project are analyzed.|
|6|Manuel Code Review|[Scope](https://github.com/code-423n4/2023-08-verwa#scope)||
|7|Infographic|[Figma](https://www.figma.com/)|I made Visual drawings to understand the hard-to-understand mechanisms|
|8|Special focus on Areas of  Concern|[Areas of Concern](https://github.com/code-423n4/2023-08-verwa#additional-context)||




### GaugeController.sol

The contract essentially manages voting for gauge weights in a dynamic system where historical weights are tracked and used for distributing rewards. Users can vote for weight changes, and the governance address has control over adding and removing gauges, as well as changing their weights. 


## b) Test analysis

The audit scope of the contracts to be audited is 95% and it should be aimed to be 100%.

```
- What is the overall line coverage percentage provided by your tests?: 95%
```

### What could they have done better?;
There are many unit tests in the project, integration tests in which the interaction of contracts with each other are modeled should be increased.

### Test suites do not test for attack vectors, especially re-entrancy

Test teams are testing many functions and variables, but recently, due to the vulnerability in the Vyper Compiler, the hacking of the projects using certain Vyper compiler and losing 50 million $ has revealed the security weakness here.
https://cointelegraph.com/news/curve-finance-pools-exploited-over-24-reentrancy-vulnerability

Attack vectors, especially re-entrancy, seem untested and trusted

```solidity

src/VotingEscrow.sol:
  267      // See IVotingEscrow for documentation
  268:     function createLock(uint256 _value) external payable nonReentrant {
  269          uint256 unlock_time = _floorToWeek(block.timestamp + LOCKTIME); // Locktime is rounded down to weeks

  287      // @dev A lock is active until both lock.amount==0 and lock.end<=block.timestamp
  288:     function increaseAmount(uint256 _value) external payable nonReentrant {
  289          LockedBalance memory locked_ = locked[msg.sender];

  325      // See IVotingEscrow for documentation
  326:     function withdraw() external nonReentrant {
  327          LockedBalance memory locked_ = locked[msg.sender];

  355      // See IVotingEscrow for documentation
  356:     function delegate(address _addr) external nonReentrant {
  357          LockedBalance memory locked_ = locked[msg.sender];

```





## c) Documents 
- Documentation should be increased further, it is recommended to add the architectural design to the documents as infographic

- I would also recommend adding quality Medium articles, it's a great way to provide an in-depth look at many of the topics in the project and is used by many blockchain projects.






## d) Other Audit Reports and Automated Findings 

**Automated Findings:**
https://github.com/code-423n4/2023-08-verwa/blob/main/bot-report.md


Especially  Medium detections in the Automated Finding Report should be taken into account




## e) Gas Optimization

The project is generally efficient in terms of gas optimizations, many generally accepted gas optimizations have been implemented, gas optimizations with minor effects are already mentioned in automatic finding, but gas optimizations will not be a priority considering code readability and code base size

Highest gas optimization: It is Storage Packed, high gas gain will be achieved in case of packaging in the following structs

https://github.com/code-423n4/2023-08-verwa/blob/9a2e7be003bc1a77b3b87db31f3d5a1bcb48ed32/src/VotingEscrow.sol#L50-L55

```solidity
File: src/VotingEscrow.sol

// @audit Can save 1 storage slot (from 4 to 3) 
// @audit Consider using the following order:
/*
  *	uint256 end (32)
  *	int128 amount (16)
  *	int128 delegated (16)
  *	address delegatee (20)
*/
50: 		    struct LockedBalance {
51: 		        int128 amount;
52: 		        uint256 end;
53: 		        int128 delegated;
54: 		        address delegatee;
55: 		    }
```







### Time spent:
9 hours