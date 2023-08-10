## Context
Did not have enough time to carefully go over all the codebase. 

## Approach taken in evaluating the codebase
Read Curve and FiatDao `VotingEscrow` code and then compared the diffs to the veRWA code. 

## Architecture recommendations
The codebase is quite simple and no major architecture recomendations were found.

## Codebase quality analysis
The code quality is quite good, which is expected as a big part of it is forked. Unfortunately it is not possible to compile without `via-ir`, which makes it impossible to calculate coverage without manually editing the code to handle `stack too deep` errors.

## Centralization risks
The governance has some previleges in the `GaugeController` and `LendingLedger`. 

In the `GaugeController` they may remove, add gauges and change gauge weights. This means that the governance as explicit control over how much users can claim in the `LendingLedger` for any specific market.

In the `LendingLedger`, `setRewards()` and `whiteListLendingMarket()` are permissioned functions, which means that, again, the governance chooses the rewards they issue.

All in all, the biggest risk is users locking their assets and then not being able to withdraw for 5 years without claiming rewards.

## Mechanism Review

### `VotingEscrow` 
Fork from Curve and FiatDao, allows users to lock native for a fixed period of 5 years and vote based on it. A delegate mechanism was added that enables the voting power to be delegated to other locks. Most of the details of this design, besides the delegates, is better explained in the Curve [documentation](https://curve.readthedocs.io/dao-vecrv.html) itself. Another modification is that the `lockEnd` of locks is reset when the amount is increased, having to wait 5 years to withdraw the full amount of the lock.

### `LendingLedger`
Users may claim their rewards according to their voting power from `VotingEscrow`. `Market`s call `sync_ledger()` and update the balances of each user. Then, users may claim `CANTO` based on the following formula:
```solidity
cantoToSend += (marketWeight * userBalance * ri.amount) / (1e18 * marketBalance); 
```
Essentially, the rewards increase prorata with the `marketWeight` (defined by the `GaugeController`, the user balance (set from the market itself), the `ri.amount` (set by governance), divided by the `marketBalance` (set the market on `sync_ledger()` calls).

### `GaugeController`
Sets the weights of the gauges. Governance adds, removes and overrides gauge weights. Users vote on the weight of each gauge according to their voting power from `VotingEscrow`.

## Systemic risks
- Users may find ways to break the voting system.
- Governance may set users rewards to 0 at any time.
- Delegates may act maliciously (the lock owner may undelegate, but might be too late).


### Time spent:
10 hours