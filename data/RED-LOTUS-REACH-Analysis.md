## Table Of Contents

1. [Architectural Review & Feedback](#ArchitecturalReview&Feedback)

# Architectural Review & Feedback

## GaugeController.sol

As part of the RED-LOTUS team’s audit approach, we identify previous audit findings from original implementation of the protocol code, in the case Curve’s GaugeController.vy. The following High finding within the 2020 Trail of Bits audit of Curve DAO ([https://solodit.xyz/issues/several-loops-are-not-executable-due-to-gas-limitation-difficulty-high-trailofbits-curve-dao-pdf](https://solodit.xyz/issues/several-loops-are-not-executable-due-to-gas-limitation-difficulty-high-trailofbits-curve-dao-pdf)) was examined to see if it was applicable to the veRWA codebase. 

While GaugeController is based on Curve Finance’s protocol, it can be observed that the main modifications from the Curve implementation are that gauge types have been removed, different whitelisting of gauge addresses is implemented due to the removed types and specific gauges have been removed also. Furthermore, the Governance role / address plays a more crucial and centralized role in administration of the protocol. 

### Curve concepts for Canto veRWA

To understand Canto’s veRWA implementation of GaugeController, we first need to understand the concepts of gauges, weights, bias, check-ins and slopes pioneered originally within Curve and then forked, modified and implemented in veRWA. 

#### Gauges

Within Canto’s veRWA and as aforementioned, pioneered by Curve, a gauge is a mechanism used to incentivize liquidity providers and govern the distribution of rewards within the protocol. Let's break down the concept of gauges:

1. Liquidity Provision: Canto is an example of a decentralized exchanges that specialize in stablecoin trading. Users can provide liquidity by depositing their stablecoins into liquidity pools. These pools enable efficient trading with low slippage.
2. Gauge Weight: Each liquidity pool in veRWA has an associated gauge weight. The gauge weight determines the amount of rewards that liquidity providers receive for their participation in the pool. Higher gauge weights indicate higher rewards.
3. Voting Power: Holding veCANTO tokens grants voting power within the veRWA governance system. 
4. Gauge Voting: Canto allows the community to vote on changes to the gauge weights. Liquidity providers can participate in these votes to influence the allocation of rewards and incentivize specific pools, handled by `vote_for_gauge_weights()`

```solidity
function vote_for_gauge_weights(address _gauge_addr, uint256 _user_weight) external 
```

Overall, gauges in veRWA play a crucial role in incentivizing liquidity provision and governing the distribution of rewards. They allow liquidity providers to earn CANTO tokens based on their participation in the protocol and provide a mechanism for the community to influence the allocation of rewards through voting.

The addition and removal of gauges within the protocol are handled by:

```solidity
function add_gauge(address _gauge) external onlyGovernance {

function remove_gauge(address _gauge) external onlyGovernance {
```

#### Weights

Weights refer to the relative importance or allocation of different assets within a liquidity pool. These weights determine the pricing and trading dynamics of the pool. 

In more detail:

1. Asset Weights: Each asset within a veRWA liquidity pool is assigned a weight, which represents its proportionate share or allocation within the pool. The weights are determined by the protocol developers and can vary for different pools.
2. Pricing Formula: veRWA uses a specific pricing formula, known as the "StableSwap" algorithm, to determine the exchange rates between assets in the pool. The formula takes into account the relative weights of the assets and the total value of each asset in the pool.
3. Impact on Pricing: The weights assigned to the assets directly influence the pricing curve of the pool. Assets with higher weights have a greater impact on the pool's pricing, while assets with lower weights have a lesser impact. This ensures that the pool maintains stability and low slippage for trades involving the most significant assets.
4. Trading Dynamics: The weights also affect the trading dynamics within the veRWA pools. They determine the slippage, or price impact, of trades. Assets with higher weights will experience lower slippage, making them more liquid and desirable for trading.
5. Balancing Trade-offs: The choice of weights involves a trade-off between providing liquidity and stability for different assets. The weights are set based on considerations such as market conditions, asset liquidity, and user demand. Adjusting the weights can help optimize the trading experience and ensure efficient swaps within the pool.
6. Governance and Updates: In some cases, the weights assigned to assets within a veRWA pool can be updated through governance processes. This allows the protocol to adapt to changing market conditions or address any imbalances that may arise.

By assigning weights to assets within liquidity pools, veRWA aims to provide efficient and low-slippage trading for stablecoins and similar assets. The specific weight allocations used in different pools are determined by the protocol developers and are subject to ongoing evaluation and adjustment.

The core functions that manage gauge and protocol weights are

```solidity
function _gauge_relative_weight(address _gauge, uint256 _time) private view returns (uint256) {

function gauge_relative_weight(address _gauge, uint256 _time) external view returns (uint256) {

function gauge_relative_weight_write(address _gauge, uint256 _time) external returns (uint256) {

function _change_gauge_weight(address _gauge, uint256 _weight) internal {

function change_gauge_weight(address _gauge, uint256 _weight) public onlyGovernance {

function get_gauge_weight(address _gauge) external view returns (uint256) {

function get_total_weight() external view returns (uint256) {
```

#### Bias

In the context of the veRWA codebase, the concept of "bias" refers to a parameter that influences the pricing and trading dynamics within the veRWA related pools. 

In more detail:

1. Bias Parameter: The bias parameter is a configurable value within the Curve protocol that influences the pricing and trading dynamics. It is set by the protocol developers and can vary for different pools.

2. Impact on Pricing: The bias parameter affects the pricing curve of the stablecoin pool. It determines how the prices of stablecoins change as the trading volume and liquidity in the pool fluctuate. A higher bias value results in a more aggressive price curve, while a lower bias value leads to a flatter price curve.

3. Trading Dynamics: The bias parameter also impacts the trading dynamics within the veRWA pools. It affects the slippage, or price impact, of trades. A higher bias value can result in lower slippage for smaller trades but higher slippage for larger trades. Conversely, a lower bias value can lead to higher slippage for smaller trades but lower slippage for larger trades.

4. Balancing Trade-offs: The choice of bias value involves a trade-off between providing low-slippage trades for smaller transactions and maintaining stability and efficiency for larger transactions. The bias parameter is set based on considerations such as market conditions, liquidity requirements, and user experience.

By adjusting the bias parameter, veRWA aims to optimize the trading experience and provide efficient stablecoin swaps within its pools. The specific bias values used in different pools are determined by the protocol developers and are subject to ongoing evaluation and adjustment.

#### Check-ins and historical data

In the context of veRWA, a "check-in" refers to a specific action or interaction that liquidity providers are required to perform within a designated timeframe. Check-ins are an essential part of the protocol's mechanism to ensure active participation and management of liquidity positions. Let's explore the concept of check-ins in more detail:

1. Check-In Mechanism: veRWA implements a check-in mechanism to incentivize liquidity providers to actively manage their positions. This mechanism encourages regular interaction with the protocol to ensure the stability and efficiency of the liquidity pools.

2. Timeframe: The check-in mechanism operates within a specific timeframe, typically on a weekly basis. Liquidity providers are expected to perform certain actions or updates within this timeframe to maintain their participation in the pool.

3. Actions and Updates: The specific actions or updates required for a successful check-in may vary depending on the protocol and pool. They can include activities such as adding or removing liquidity, adjusting weights, or performing other necessary operations.

4. Importance of Check-Ins: Check-ins serve several purposes within the Curve protocol:

a. Pool Management: Regular check-ins help liquidity providers actively manage their positions and respond to changes in market conditions. This ensures that the liquidity pools remain efficient and responsive.

b. Governance Participation: Check-ins often play a role in governance processes within the protocol. Liquidity providers may be required to check-in and vote on proposals related to parameter updates, such as adjusting the bias parameter or making changes to the protocol's functionality.

c. Reward Distribution: Check-ins may also be necessary to ensure liquidity providers receive their rewards accurately and in a timely manner. By checking in, providers confirm their continued participation and eligibility for rewards.

If a liquidity provider fails to perform the required actions or updates within the designated timeframe, it is considered a "missed check-in." This may result in penalties or a reduction in rewards, depending on the specific rules and mechanisms implemented by the protocol. 

The following functions in GaugeController can be observed to fill historic gauge weights week-over-week for missed checkins, return the sum of weights for the future week and return both the sum of weights and gauge weight respectively.

The functions that handle historic weights and sum-of-weights are:

```solidity
function _get_sum() internal returns (uint256) {

function _get_weight(address _gauge_addr) private returns (uint256) {   
```

Overall, the check-in mechanism in veRWA encourages liquidity providers to actively manage their positions, participate in governance processes, and contribute to the stability and efficiency of the liquidity pools.

#### Slopes

Slopers refer to a parameter that influences the rate of change in the pricing curve of a liquidity pool. The slope determines how the prices of assets within the pool adjust in response to changes in supply and demand. It plays a crucial role in determining the trading dynamics and slippage within the veRWA pools.`vote_for_gauge_weights()` concerns itself with generating slopes for gauge calculations.

### Concerns about Governance

As mentioned previously, one of the main changes to the GaugeController was an increased role of Governance in the administration of the protocol. This can be seen in the code snippet of the modifier below.

```solidity
modifier onlyGovernance() {
    require(msg.sender == governance);
    _;
}
```

Specifically in the context of the previous High audit finding in the original Curve protocol codebase ([https://solodit.xyz/issues/several-loops-are-not-executable-due-to-gas-limitation-difficulty-high-trailofbits-curve-dao-pdf](https://solodit.xyz/issues/several-loops-are-not-executable-due-to-gas-limitation-difficulty-high-trailofbits-curve-dao-pdf)) in which a DoS vector was possible due to the permisonless nature of adding gauges in the original Curve protocol. 

Due to the onlyGovernance modifier being added to the `add_gauge` function, this DoS vector is no longer viable in the veRWA codebase. Nonethless, it does again add a risk of centralization within the protocol and priveleged persons (whale token holders i.e project team and founders) potentially being able to abuse their position of trust.

### A note on the recent Curve exploit

GaugeController is a Solidity port of Curve’s Vyper GaugeController. Due to recent events in which Curve pools with contracts utilizing the Vyper programming language were exploited, alarm could be raised over veRWA’s use of this code. However, the exploit was due to a vulnerability within the Vyper compiler itself and not the code. As such, the exploit would not be viable within the ported Solidity code.

### Conclusion

This comprehensive breakdown of the GaugeController protocol, explanation of key concepts such as Gauges, Weights, Bias, Slopes and Check-In’s and modifications from the original Curve codebase should give an external observer a robust understanding of the architecture and operation of the contract.

## LendingLedger.sol

The Lending Ledger is responsible for allowing users to interact with Canto's veRWA system in order to provide liquidity for different lending markets which are whitelisted by the governance system. In addition to this responsibility, the users who provide liquidity are rewarded in the form of `CANTO`, respective to their share. As a result, the lending ledger has a duty of ensuring how much liquidity a specific user has supplied for any given market, during any specified timeframe. Therefore, it is quite important to analyse the possibilities of any attack vector, whether this is from the result of a governance proposal or from a malicious exploit.

### Core Behaviour

When a user interacts with the `LendingLedger`, their transaction is ultimately processed via `sync_ledger()`:

```solidity
function sync_ledger(address _lender, int256 _delta) external {
```

The `sync_ledger` checks to ensure that there are no underflows, and that the user is interacting with a valid lending market which has been whitelisted: 

```solidity
  address lendingMarket = msg.sender;
  require(lendingMarketWhitelist[lendingMarket], "Market not whitelisted");

  ...
  uint256 currEpoch = (block.timestamp / WEEK) * WEEK;
  int256 updatedLenderBalance = int256(lendingMarketBalances[lendingMarket][_lender][currEpoch]) + _delta;
  require(updatedLenderBalance >= 0, "Lender balance underflow"); // Sanity check performed here, but the market should ensure that this never happens

  ...
  int256 updatedMarketBalance = int256(lendingMarketTotalBalance[lendingMarket][currEpoch]) + _delta;
  require(updatedMarketBalance >= 0, "Market balance underflow"); // Sanity check performed here, but the market should ensure that this never happens
```

The use of the `_checkpoint_lender` function is rather important, because a user’s first deposit is actually stored and updated via this function, and without it being included within `sync_ledger`, the user would never have the ability to successfully execute the `claim` function days, weeks or years after providing their liquidity to any given market:

```solidity
_checkpoint_lender(lendingMarket, _lender, type(uint256).max);
```

This was an initial problem upon first discovery, because it was not clear whether the user has an updated value for `userLastClaimed` upon first deposit however they do, purely because `sync_ledger` calls `_checkpoint_lender`, and within the checkpoint lender function we have the following lines of code which makes the accounting aspect of this successful:

```solidity
if (lastUserUpdateEpoch == 0) {
  // Store epoch of first deposit
  userClaimedEpoch[_market][_lender] = currEpoch;
  lendingMarketBalancesEpoch[_market][_lender] = currEpoch;
  ...
```

### Feedback

#### A robust protocol and comprehensive test suite

With this being said, the tests which exist for epoch vulnerabilities are very well made, and had a well-thought-out process. The Red Lotus team was unable to find any vulnerabilities pertaining to the reusage of the same epoch, or attempting to claim rewards for future epochs which do not exist at that point in time. 

Additionally, from an accounting perspective, it is remarkable that Canto implemented a fail-safe methodology to “fill in gaps in the market total balances history (if any exist)” because if any problems were to arise, Canto has the ability to successfully maintain accurate accounting. This is something that not every protocol considers, and therefore we deem it as a good addition to the protocol:

```solidity
function _checkpoint_market(address _market, uint256 _forwardTimestampLimit) private {
    ...
    if (lastMarketUpdateEpoch == 0) {
        lendingMarketTotalBalanceEpoch[_market] = currEpoch;
    } else if (lastMarketUpdateEpoch < currEpoch) {
        // Fill in potential gaps in the market total balances history
        uint256 lastMarketBalance = lendingMarketTotalBalance[_market][lastMarketUpdateEpoch];
        for (uint256 i = lastMarketUpdateEpoch; i <= updateUntilEpoch; i += WEEK) {
            lendingMarketTotalBalance[_market][i] = lastMarketBalance;
        }
        ...
    }
}
```

#### Usability Critiques

Regarding the actual mechanism of the protocol, we would argue that it is important to account for user mistakes such as the ones which relate to “Users can forfeit the rewards for some epochs by skipping them when calling `claim`”. This problem can be eliminated and as a result, would improve user friendliness for the usage of the protocol overall and would provide a safer environment for the users. 

#### Liquidity and Governance concerns

Something that we would like to bring to light is the actual mechanism of the protocol, where the governance essentially has to assure that `LendingLedger`  at all times contains enough `CANTO`. It can be argued that it is quite fragile in this sense because if the `Governance`  system were to fail, this would mean that `LendingLedger`  would also fail because the `Governance`  system itself assures that the `LendingLedger`  contains enough `CANTO`, and this allows for the possibility that users may not have the ability to withdraw their `CANTO` rewards due to an inaccurate balance on Canto’s side - something which is out of the user’s control and is in the hands of Canto.

#### A note on confusion and Canto team’s response

It’s important to note that attempts to clarify with the developers which token was actually accepted by `LendingLedger`  revealed the assumption that only `cNOTE` is accepted as a valid form of currency within the `veRWA`  protocol, it is apparent from the audit that `CANTO` is the expected token of deposit. 

It is not clear that `cNOTE` is the only accepted form of currency when it comes to providing liquidity for the markets, and without a crystal clear notice to the users, it may cause the users to lose out on their potential liquidity assignment to the specified markets, also allowing them to not retrieve the funds back which is a tragedy. Whilst this may not be a big problem for Canto, it is a big problem for the architectural design and how it links to user-friendliness.

## VotingEscrow.sol

Curve Voting Escrow (`ve`) has become the common way for DeFi projects to get, lose, and use voting power in their smart contract functions. The motivation for `ve` is to provide the ability to vote for which pools will get the share of limited rewards emitted by the protocol.  To combat the gaming of votes by powerful voters, decay in the voting power is included.  

Decay in voting power is mediated by three major factors:

1. The amount of tokens locked
2. How long they are locked for
3. How long it’s been since first locking

`ve` makes later lockers competitive with early lockers. While it introduces the problem of continuous risk/reward judgements by investors due to the game theory involved, it keeps the protocol’s markets available to a larger pool of investors wanting to compete for the fees and rewards by the protocol.

Since its inception, `ve` has remained a popular DeFi mechanic.  Forks and ports have been made and remade.  Originally, the first version of the contract was written in Vyper. Projects like Frax, Reflexer, and Workhard Finance later provided a version in Solidity.  The version used in veRWA is a fork of the FIAT DAO implementation, which is itself a fork of Curve's original implementation.

### **Overview**

Users can lock up CANTO (for five years) in the `VotingEscrow` contract to get veCANTO.

When you lock up your CANTO, any further actions such as increasing your locked amount (`**increaseAmount**`) will reset your lock-time to 5 years. This is primarily a strategic choice in terms of business or design, and its done to prevent mercenary capital from locking a large number of tokens for a brief period to gain significant voting power for a limited number of periods.  These short-term investors can provide negative value to the protocol because of the constant flux in locked capital, making it less attractive for long-term investors looking for safe and consistent gains.

For the rest of this analysis, we’ll delve into the mechanics of the smart contract that implements veRWA’s `ve` functionality.

### **Core `ve` Functionality**

1. Locking/Depositing
2. Withdrawing
3. Increasing locked deposits
4. Delegating

#### **Caveats and Linear Decay**

You can't transfer the veCANTO token. The only method to get veCANTO is by locking up CANTO. As the time left until you unlock your CANTO decreases, your veCANTO balance goes down in a straight line. For instance, if you lock up 4000 CANTO for one year, you'll have the same veCANTO as if you locked up 2000 CANTO for two years, or 1000 CANTO for four years.

#### **Locking/Depositing**

Locks can be created with **`createLock` .**

**`createLock`** updates your locked balance with the chosen amount of CANTO and sets the unlock time. Your delegated tokens also increase, and you become the delegatee.

#### **Withdrawing**

The **`withdraw`** function allows you to retrieve your locked tokens after your lock-time has passed. 

Actions:

- Calculates the amount of tokens to send back based on the locked balance.
- Updates your locked balance to show that you've withdrawn the tokens.
- Clears the lock details, including the amount, end time, delegation, and delegatee.
- Triggers a checkpoint to record the change in your voting power.
- Sends the tokens back to your address.

#### **Increasing Locked Deposits**

The **`increaseAmount`** function allows you to add more tokens to an existing locked balance, enhancing your voting power. 

If you're updating your own locked balance (not delegating), your locked balance is updated:

- The existing **`amount`** and **`end`** values are copied to a new **`LockedBalance`**.
- The new **`amount`** is increased by the chosen **`_value`**.
- The **`end`** time is updated to the rounded down week after the current time.\

If you're updating a delegated lock:

- Your lock is updated first, and your voting power is checkpointed.
- Then, the delegatee's lock is updated:
    - If the delegatee's lock hasn't expired, their delegated amount increases by the chosen **`_value`**.
    - The delegatee's lock and voting power are checkpointed.

#### **Delegating**

Users can let someone else control when their lock ends and how much they have for voting. Both the person giving control (delegator) and the person receiving it (delegatee) should have active locks when this happens. Also, the delegatee's lock should last longer than the delegator's lock.

Actions:

- If you're delegating to yourself:
    - Your lock becomes the new delegatee's lock.
- If you're undelegating:
    - The old delegatee's lock becomes your new lock.
- If you're re-delegating to someone else:
    - The old delegatee's lock becomes the new delegatee's lock.
    - Your lock remains the same if not involved in delegation.

### Time spent:
90 hours