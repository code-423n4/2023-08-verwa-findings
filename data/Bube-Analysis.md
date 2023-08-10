The scope of the project consists of three smart contracts: `GaugeController.sol`, `VotingEscrow.sol` and `LendingLedger.sol`. 
The first one allows users to vote for gauge weights. The controller then enables to query the relative weights for all the gauges at any time in the past. 
The secont smart contract implements a fork of the FIAT DAO implementation, which is itself a fork/solidity port of Curve's original implementation with a few modifications. 
The third one keeps track of how much liquidity a user has provided in a market at any time in the past. To do so, the (white-listed) markets need to call `sync_ledger` on every deposit/withdrawal by a user. Canto governance calls `setRewards` and sends CANTO to the contract to control how much CANTO is allocated for one epoch. Users can then claim the CANTO according to their balance in the market and the weight of this market in the GaugeController.
I used Remix, VS Code and Slither during my audit. I found some non-critical, low and medium issues. This is one of my first contests, but i hope, that my findings are valuable.
I have learned a lot from the audit of the codebase. I learned about that how Vote-Escrowed CRV works and about Liquidity Gauges and Minting CRV. During the audit i read a lot about the reentracy vulnerability, the ReentracyGuard and nonReentrant modifier. Also, about the events and when is recommended to use them. In my opinion, there are missing events in sensitive functions.
It was pleasure for me to participate in this contest.


### Time spent:
20 hours