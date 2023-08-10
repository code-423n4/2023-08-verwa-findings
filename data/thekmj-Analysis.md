## Summary of the project

The project implements a veToken voting-escrow model, similar to veCRV. Users are able to lock their CANTO tokens in exchange for voting power. Users can then vote to channel incentives to different pools, e.g. for pools they have positions in.

The project is a fork of various protocols, with changes.
- `VotingEscrow`: Fork of FIAT DAO's implementation.
- `GaugeController`: Solidity implementation of Curve's Vyper counterpart.
- `LendingLedger`: For distributing rewards. Is not a fork.

For the forked contracts, the major changes are as follow:
- `VotingEscrow` now uses native token, instead of ERC20 token.
- In FIAT DAO's Voting Escrow, a user is able to specify their lock duration (not exceeding MAX and ending at valid timestamps). In veRWA, the lock duration is fixed at 5 years (maximum possible).
- `GaugeController`: Governance is able to remove gauges.
    - Additionally, governance does not have to specify gauge weight upon addition.

## Approach taken in evaluating the codebase

For the forked contracts, we focus on the changes made, and its implications on existing variables of the contract. Thus we focus mostly on the functionalities and algorithmic aspects. We do not focus on the mathematical specifics, as the math workload are mostly forked, and has already been battle-tested. 

For `LendingLedger` however, we do focus on the specifics. However, it is still more algorithmic, rather than mathematical, in nature.

We identified and have submitted the following issues and recommendations within the codebase:

| No. | Title | Context |
|---|:---:|---|
| [H-01] | If governance removes a gauge, user's voting power for that gauge will be lost |  |
| [M-01] | There is no functionality to extend one's own lock duration |  |
| [L-01] | Hardcoding lock duration to 5 years may discourage users from locking | 1 |
| [L-02] | Documentation do not properly reflect the changes from the original VotingEscrow | 8 |
| [L-03] | Events should be emitted for important events | 2 |
| [N-01] | Redundant equivalence check for msg.value | 2 |
| [N-02] | Voting token metadata should either be all hardcoded or all immutable | 1 |
| [N-03] | Redundant limit check for voting power | 1 |
| [N-04] | Incorrect variable addresses on testing |  |
| [R-01] | Recommend allowing users to vote for multiple gauges in one transaction | |

## Centralization risks

There are two contracts holding funds:
- `VotingEscrow`: Holds locked CANTO
- `LendingLedger`: Holds rewards.

In both contracts, we identified no functions for the admin to withdraw funds to themselves. 

However, governance is able to override gauge weights using the function `GaugeController.change_gauge_weight()`. This is technically a possible path to manipulate rewards distribution. 

Governance also has the responsibility to correctly distribute rewards for every epoch (every week), as well as ensuring enough CANTO is locked in the reward Ledger.

## Systemic risks

External outgoing calls happen only when sending CANTO to the users, either through `withdraw()` or `claim()`. Both functions either have re-entry guards in place, or follows the CEI pattern. 

However, external incoming call also happens in `sync_ledger()`. While only whitelisted markets can call this function, it can still affect rewards distribution. It is then the team's responsibility to diligently verify the market was correctly implemented before whitelisting.

## Architecture recommendations

`LendingLedger` currently only supports rewarding in CANTO/native token. We suggest integrating ERC20 tokens into the mix, as this will enable external partnered protocols to provide incentives, which may boost the growth of the CANTO ecosystem.



### Time spent:
16 hours