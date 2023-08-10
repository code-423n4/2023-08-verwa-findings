## Approach taken in evaluating the codebase

Read through the whole code, annotates what is weird or could be broken. Find way to exploit broken behaviours / assumptions.

Check difference with source of inspiration for the codebase at https://github.com/curvefi/curve-dao-contracts/blob/master/contracts/VotingEscrow.vy and https://github.com/code-423n4/2022-08-fiatdao/blob/main/contracts. Find out reason for differences and if they break something (they most certainly did).

Add and update tests to figure out if my understanding of the code was correct / my exploits correct.

## Architecture recommendations

Instead of adapting complicated code, take the code as is and build on top of it via inheritance / composition to adapt the existing code to your use case. Every change made to the code seem to break something.

## Codebase quality analysis

The code is tough to read due to the advanced logic of keeping the history of curve for votes. That being said, I don't know if there is a better way to write the logic. There are very few comments to help understand the logic.

Some variables are poorly named, e.g. a slope being the opposite of a slope (downward instead of upward slope), `ts` for timestamp, state variables of `GaugeController` called `points_weight`, `changes_weight`, or `time_weight` while they refer to a specific curve rather than a specific weight, some short names like `d_slope` or `d_bias` are meaningless.

The re-entrancy locks from OZ are correctly used.

Overall position of functions and variables in the code makes sense.

## Centralization risks

Governance can overwrite the weight for any gauge at any time via `GaugeController.change_gauge_weight()`. Governance can whitelist / blacklist a market at any point in time in `LendingLedger`. Depending on what `governance` is (a single user / a multisig / a DAO) the protocol is more or less decentralized.

## Mechanism review

Overall the idea of vesting users' canto for voting power is interesting. I wish there was a way for users with a lock to increase their lock duration (with a maximum limit of 5 years) when it gets shorter to increase their voting power back again.

## Systemic risks

With such a short time to review the code and such a complicated code, I would not be confident that it is bug free after the audit. Some risks rely on lending markets correctly calling `LendingLedger.sync_ledger()` which might bring complexity to lending markets and eventually bugs.

### Time spent:
25 hours