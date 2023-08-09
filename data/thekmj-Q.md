## [L-01] Hardcoding lock duration to 5 years may discourage users from locking.

The original veCRV implementation allows users to lock CRV tokens in exchange for voting power. Each CRV token locked for the maximum duration will yield one unit of voting power, and said voting power decays with time. The same idea is applied here, but with CANTO.

However, while veCRV allows users to specify the lock duration, veRWA does not, and only allows unlocking after 5 years of inactivity. This removes a certain degree of flexibility in the economy game, and may discourage users from locking their tokens, due to the high risk nature of smart contracts and cryptocurrency market. 

## [L-02] Documentation do not properly reflect the changes from the original `VotingEscrow`.

There is no such file `IVotingEscrow` present in the repository. 

- https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L267
- https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L286
- https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L325
- https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L325
- https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L472
- https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L486
- https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L564
- https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L571

One should not be relying on FIAT DAO's original `IVotingEscrow` as the documented source of truth, as the contract veRWA has undergone changes. Several behaviors are important to document for the user to be aware of. For example, `increaseAmount` also resets their lock to 5 years, or `delegate` actually does not reset the lock (contrary to the statement at [line 18](https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L18)).

## [N-01] Redundant equivalence check for `msg.value`

It is enough to use `msg.value` itself as the lock value. There is no need to require it as a function parameter.

- https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L18
- https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L292

## [N-02] Voting token metadata should either be all hardcoded or all immutable 

https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L25-L28

In `VotingEscrow`, the voting token metadata are set to storage. 

However, it is known that they will be the native CANTO token, with known immutable metadata. 

We suggest setting all of them to a constant, or to immutables. This also has the virtue of gas-saving whenever the data are used in a transaction (e.g. decimals).

## [N-03] Redundant limit check for voting power

https://github.com/code-423n4/2023-08-verwa/blob/main/src/GaugeController.sol#L212

In the function `vote_for_gauge_weights()`, there is an assertion for user voting weight being in range $[0, 10000]$.

```solidity
require(_user_weight >= 0 && _user_weight <= 10_000, "Invalid user weight");
```

However, if the user attempts to input more than $10000$, then the call will revert anyway from the following assertion.

```solidity
require(power_used >= 0 && power_used <= 10_000, "Used too much power");
```

https://github.com/code-423n4/2023-08-verwa/blob/main/src/GaugeController.sol#L241C9-L241C81

## [N-04] Incorrect variable addresses on testing

In the test file `GaugeController.t.sol`, `user1` is often used as a gauge instead of `gauge1`. Using these two variables interchangeably may lead to confusions, especially in future integrations. Thus it is not recommended.

## [R-01] Recommend allowing users to vote for multiple gauges in one transaction

Currently, to edit a vote for a gauge, a user must send one transaction. This means if the user wants to divide their votes across gauges (e.g. if they have multiple positions in multiple pools), then they'd have to send multiple transactions.

We suggest exposing a function for users to update their vote for multiple pools. Validate that the user's total vote after the transaction is less than $10000$ is enough.