# [A-01] Any comments for the judge to contextualize your findings

I found some low issues

- [L-01] Possible wrong calculation in GaugeController::`_change_gauge_weight` Function
  This finding highlights a potential vulnerability in the `_change_gauge_weight` function where an incorrect calculation might occur if the new gauge weight provided as input `_weight` is greater than the old gauge weight retrieved from `_get_weight(_gauge)`. This finding points out the risk of unexpected and potentially incorrect results in subsequent calculations due to the wrong calculation of the new_sum variable.

- [L-02] Potential Timestamp Manipulation in GaugeController::checkpoint_gauge
  This discovery points out a potential security concern in the checkpoint_gauge function where an external attacker could manipulate the timestamp of the blockchain to potentially manipulate the results of the `_get_weight` and `_get_sum` functions. The concern raised here emphasizes the risk of inaccurate data being used for gauge weights and sum calculations.

- [L-03] Possible Lock Duration Exploit in GaugeController::`vote_for_gauge_weights` Function
  This finding identifies a potential vulnerability in the `vote_for_gauge_weights` function, where an attacker could exploit a lock duration that is shorter than the time it takes for the `_user_weight-scaled` weight change to occur. This could potentially lead to the attacker claiming more influence than their actual locked voting power, causing improper governance decisions. The concern here centers around a possible lock duration exploit.

- [L-04] Inaccurate Initialization of time_sum in GaugeController Constructor
  This finding highlights a potential inaccuracy in the initialization of the time_sum variable in the constructor of the GaugeController contract. The concern is that the rounding of the current timestamp to the nearest week might lead to potential discrepancies in historical gauge weight calculations, particularly when the contract is deployed near the start or end of a week.

- [L-05] Potential Inaccurate Slope Calculation VotingEscrow::`_checkpoint` function
  This finding underscores the risk of potential inaccuracies in slope calculations, biases, and historical data synchronization within the VotingEscrow::`_checkpoint` function. The concern here is that inaccurate calculations could lead to discrepancies in voting power, compromised historical data, and unexpected behaviors in the contract's functioning.

- [L-06] Unchecked Loop in LendingLedger::`_checkpoint_lender` Function
  This discovery highlights a vulnerability in the LendingLedger::`_checkpoint_lender` function that could potentially lead to a denial-of-service attack. The concern raised here is that an unchecked loop could be exploited by an attacker depositing a large value for `_forwardTimestampLimit`, leading to excessive gas consumption and potential disruption of the contract's functionality.

# [A-02] Approach taken in evaluating the codebase

Accordingly, I analyzed and audited the subject in the following steps;

1. **Core Protocol Contracts Overview**:

   - GaugeController.sol:
     Purpose: This contract allows users to vote for lending markets (gauges).
     Functionality: Users can vote on the allocation of rewards to different lending markets. The controller calculates the relative weights for all gauges at any past time.
     Context: It's based on Curve's GaugeController with modifications, primarily supporting a single gauge type (veRWA). Whitelisting of gauges is performed differently from the original implementation.

   - VotingEscrow.sol:
     Purpose: Users can lock up CANTO tokens for five years and receive veCANTO in return, which is used to measure voting power.
     Functionality: Users lock up CANTO tokens for a fixed period (5 years), and they receive veCANTO tokens in return. This escrowed balance contributes to their voting power.
     Context: Derived from FIAT DAO's implementation with modifications, including support for native tokens and a fixed lock time that resets with each action.

   - LendingLedger.sol:
     Purpose: Tracks liquidity provided by users in different lending markets, calculates shares for each user/market, and enables users to claim CANTO rewards.
     Functionality: Records users' liquidity contributions in different markets. When users provide liquidity or withdraw, the markets call sync_ledger. The contract also handles the allocation of CANTO rewards for each epoch based on market weights.
     Context: Serves as a ledger for tracking user participation and rewards in lending markets.

   - Epochs:
     The concept of epochs is used to discretize time into one-week intervals.
     Calculations and reward distributions are performed per epoch.
     Users can claim rewards only after an epoch has ended.

   The system seems to involve incentivizing users to provide liquidity in various lending markets through the escrowing of CANTO tokens. Users receive veCANTO tokens, which represent their voting power. The GaugeController enables users to vote on reward allocations to different markets, and the LendingLedger tracks liquidity participation and handles reward distributions.

2. **Graphical Analysis**: Solidity-metrics is a popular tool used to analyze and visualize Solidity code. It provides various metrics and visualizations to gain insights into the codebase's complexity and maintainability.
   [solidity-metrics](https://github.com/ConsenSys/solidity-metrics)

3. **Utilizing Static Analysis Tools**: During the static analysis phase, I have discovered specific vulnerabilities in the smart contract. However, I want to bring to your attention that these vulnerabilities are already known issues and are associated with `BotRace`.

4. **Test Coverage Evaluation**: I currently encounter challenges with the foundry and, regrettably, am not proficient in resolving these issues. As a result, I am unable to run the tests effectively.

5. **Manuel Code Review**

# [A-03] Anything that you learned from evaluating the codebase

- Epoch-Based Logic: The use of epochs for tracking time-based events and rewards distribution is a common pattern in DeFi systems. It allows for efficient calculations and updates.

- Governance and Whitelisting: Governance roles are used to manage critical contract parameters and whitelisting of lending markets, emphasizing control and flexibility.

- Reward Calculation: The contracts demonstrate a systematic approach to reward calculation based on user balances, market weights, and predefined reward amounts.

- Claiming Mechanism: The contracts implement a controlled mechanism for users to claim rewards, enforcing specific conditions for eligibility.


### Time spent:
10 hours