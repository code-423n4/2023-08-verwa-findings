veRWA scope includes three contracts: `GaugeController`, `VotingEscrow`, and `LendingLedger`.
All of them are high-quality contracts, and I found no High/Medium severity risks, only a couple of Informational/GAS related. 

At the first glance, the codebase logic seems a bit complex, however, it becomes much easier after spending more time analysing it. Also, the constant activity of developers(sponsors) on the Code4rena Discord channel has made the auditing process faster and easier.

`GaugeController` and `VotingEscrow` are forks of Curve Finance (MIT) codebases. So, I have mostly spent time comparing the modifications from the original code and if those pose any risks. 

`GaugeController` was easier to compare than the `VotingEscrow`, as the latter originated from the Vyper language contract. I am no specialist in that language but I would not claim it was hard, the logic was kept the same with slight modifications. No High/Medium severity risks were found in the two abovementioned contracts.

The only recommendations are:
- provide checks for `address(0)` in `add_gauge` function of `GaugeController.sol`
- remove the `nonReentrant` modifier in `createLock` of `VotingEscrow.sol`. 

LendingLedger contract, on the other hand, is a brand new contract written by the veRWA team, this required more time to go through. So far, no risks in this contract where found. The only potential issue here was with the comments on whitelisting function not mentioning that it can not only whitelist but also delist lending markets by passing the `false` as the `_isWhiteListed` parameter.

Overall, I enjoyed auditing the veRWA codebase. The documentation was clear and the project developers were happy to assist and answer questions.

### Time spent:
7 hours