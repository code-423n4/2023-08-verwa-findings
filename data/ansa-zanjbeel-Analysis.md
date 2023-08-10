📌 What did I learn?

`Overview`

The contracts implement a voting-escrow incentivization structure for Canto RWA (Real World Assets) similar to veCRV with its liquidity gauge.

- `CANTO:` native token of the system
- `VeCANTO:` locked CANTO tokens with voting power
- `Epochs:` fixed time interval used for repetitive calculations

`parts have been divided down below along with how they interact:`

- `VotingEscrow:` The Voting Escrow allows users to lock up their CANTO tokens for a fixed period (five years). In exchange, they receive new tokens called veCANTO. This locking of tokens serves as a commitment and provides users the ability to participate in voting.
- `GaugeController:` The controller allows users to vote for gauge weights, such as how much of an epoch's CANTO is allocated to each gauge. The controller then enables it possible to query all of the gauges' relative weights at any time in the past.
- `LendingLedger:` The lending ledger allows users to claim the CANTO in accordance with their market balance and the market's weight in the gauge controller.


`instance` 
there might be lending markets X, Y, and Z where Alice, Bob, and Charlie provide liquidity.
In lending market X, 
`Alice provides 60% of the liquidity`
`Bob provides 30% of the liquidity`
`Charlie provides 10% of the liquidity`
At this epoch, lending market X receives 40% of all votes.`Therefore, the allocations are:`
`Alice: 40% * 60% = 24% of all CANTO that is allocated for this epoch`
`Bob: 40% * 30% = 12% of all CANTO that is allocated for this epoch`
`Charlie: 40% * 10% = 4% of all CANTO that is allocated for this epoch`

📌 Approach taken in evaluating the codebase
 `I first reviewed the scoping details, then view the contracts that are in scope, After that, I studied the documentation to create a report on the codebase's analysis.`

📌 Comment for the judge to contextualize findings?
`I'm certain that reading this context will make it easier to understand.`



### Time spent:
6 hours