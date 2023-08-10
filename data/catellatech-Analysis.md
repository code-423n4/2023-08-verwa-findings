# Analysis veRWA Contest 
![veRWA](https://code4rena.com/_next/image?url=https%3A%2F%2Fstorage.googleapis.com%2Fcdn-c4-uploads-v0%2Fuploads%2FVT6Se7uAcfK.0&w=96&q=75)

## Description of `veRWA project` 
veRWA is an incentivization mechanism designed for Real World Assets (RWA) on the Canto platform. It operates similarly to the veCRV system with its liquidity gauge. Users have the option to lock up CANTO tokens for a duration of five years in the `VotingEscrow` contract, in exchange for receiving veCANTO tokens. 

Subsequently, they can participate in voting activities within the ``GaugeController`` for various credit markets that have been whitelisted by the governance. Users who provide liquidity in these credit markets can claim `CANTO` tokens (provided by the CANTO governance) from the `LendingLedger` contract, based on their proportional contribution.

To illustrate, let's consider credit markets X, Y, and Z where Alice, Bob, and Charlie contribute liquidity. In the scenario of credit market X, assuming Alice contributes 60% of the liquidity, Bob contributes 30%, and Charlie contributes 10% at a specific time (epoch), and let's assume that credit market X receives 40% of all votes at that epoch. Consequently, the allocations would be:

- Alice: 40% * 60% = 24% of all allocated CANTO tokens for this epoch.
- Bob: 40% * 30% = 12% of all allocated CANTO tokens for this epoch.
- Charlie: 40% * 10% = 4% of all allocated CANTO tokens for this epoch.

This mechanism aims to incentivize participation and liquidity provision in credit markets backed by real world assets on the Canto platform.

## Summary
![veRWA](https://github.com/catellaTech/unknow/blob/main/Diagrama%20sin%20t%C3%ADtulo.drawio.png?raw=true)

## 1- Approach we followed when reviewing the code
To begin, we assessed the code's scope, which guided our approach to reviewing and analyzing the project.

### **Scope**

- **GaugeController.sol**:
    - The `GaugeController` contract is like a translated version of Curve's `GaugeController` written in Solidity. It simplifies the gauge types to just one type for veRWA. This leads to other changes in the code. The way gauges (lending markets) are approved is also different from the original design.

    The controller lets users vote on how much weight a gauge should have in distributing CANTO tokens during a specific period (epoch). It also allows checking the historical relative weights of all gauges.


- **VotingEscrow.sol**: 
    - The `VotingEscrow` system used here is based on an existing setup from FIAT DAO, which itself was built by modifying Curve's original design. In this version, a few changes were made, like allowing the use of native tokens instead of ERC20 tokens. Additionally, a fixed lock time of 5 years was added, which restarts every time an action is taken.

- **LendingLedger.sol**: 
    - The lending ledger keeps track of how much cryptocurrency users have contributed to a specific market over time. To do this, authorized markets need to notify the ledger whenever a user deposits or withdraws funds. The Canto governance manages the rewards by sending CANTO tokens to the contract, controlling the amount allocated for each epoch. 

With this understanding, we proceeded to scrutinize and audit the code through a series of structured steps:

![veRWA](https://github.com/catellaTech/unknow/blob/main/VERWA1.drawio.png?raw=true)

## 2- Analysis of the code base
The **`GaugeController` contract** is a fundamental component of the ``veRWA project`` is responsible for enabling users to vote on the distribution of CANTO tokens and managing information related to weights and changes in "gauges" (credit markets) within the `veRWA project`.

- Here's a breakdown of the key parts of this contract:

![`GaugeController`](https://github.com/catellaTech/unknow/blob/main/Gauge.drawio.png?raw=true)

The **`VotingEscrow` contract** is responsible for managing users locking and voting power. 

- I'll explain the key components and functionality of the contract in simpler terms:

![`VotingEscrow`](https://github.com/catellaTech/unknow/blob/main/`VotingEscrow`.drawio.png?raw=true)

The **`LendingLedger` contract** is responsible for tracking balances and rewards in lending markets. Users can synchronize their balances, claim rewards, and the governance can set rewards and control the whitelist of lending markets.

- Let's delve into its functionality and structure:

![`LendingLedger`](https://github.com/catellaTech/unknow/blob/main/lending.drawio.png?raw=true)

## 3- Test analysis
The audit scope of the contracts to be audited is 95% and it should be aimed to be 100%.

```
What is the overall line coverage percentage provided by your tests?: 95%
```

### How could they have done it better?
While the provided code seems to be functional, there are always areas for improvement to ensure better security, efficiency, and readability in both the contract and the protocol. 

- Here are some potential areas for improvement:

    1. **Code Comments and Documentation**:
        - The code lacks detailed comments explaining the purpose and functionality of each function. Adding comprehensive comments can help other developers understand the codebase more easily.

    2. **Access Control**:
        - While the contract includes a onlyGovernance modifier for certain functions, it's essential to ensure that access control mechanisms are robust and properly implemented throughout the protocol. Consider using a more standardized access control library for clarity and security.

    3. **Error Handling and Assertions**:
        - The code uses require statements for error checking, which is good. However, more informative error messages could be added to provide better context about what went wrong during transactions.

    4. **Code Organization**:
        - The code could be organized into separate files or contracts for improved modularity and readability. This can make it easier to understand the different components of the protocol.

    5. **Security Audits**:
        - A thorough security audit by professionals can help identify vulnerabilities and potential attack vectors that might not be obvious. Security should always be a top priority.

    6. **Event Logging**:
        - Adding well-defined event logs can help external services and users better understand the state changes and actions taken within the contract.

    7. **More Comprehensive Testing**:
        - Extensive testing, including unit tests, integration tests, and possibly even external audits, can help ensure the robustness and correctness of the protocol.


## 4- Architectural 
![Architectural](https://github.com/catellaTech/unknow/blob/main/arquitectura.drawio.png?raw=true)

- **Users**:
Users interact with the `veRWA project` through their actions, such as depositing tokens, voting on governance proposals, and participating in lending markets.

- **`VotingEscrow.sol`**:
    - This contract manages the voting and token locking system. Users can lock their tokens for a specified period and take part in community decision-making. The contract might be designed to encourage active participation and reward users for their voting actions.

- **`GaugeController.sol`**:
    - The `GaugeController` contract could manage the "gauges" that influence reward distribution. This contract likely defines how rewards are allocated among different markets or assets based on their relative weights.

- **`LendingLedger.sol`**:
    - This contract is the core of the lending and rewards functionality. Users can deposit tokens into lending markets, earn cTokens, and receive rewards in the form of `CANTO` tokens. The contract keeps track of user balances, total balances in each lending market, and other relevant metrics. It also allows users to claim rewards and enables governance to configure rewards.

This overview provides a general understanding of how the different components interact within the `veRWA project`.

## 5- Documents 
- It would be helpful if the Documentation explained how the ecosystem works from a basic contract level so that it is easier to digest for developers, users and auditors looking to integrate into the ``veRWA project``, it is recommended to add the architectural design to the documents as infographic

- I would also recommend adding quality Medium articles, it's a great way to provide an in-depth look at many of the topics in the project and is used by many blockchain projects.

##  6- Systemic & Centralization Risks
Here's an analysis of potential systemic and centralization risks in the provided contracts:

### Systemic Risks:

- **Epoch-Based System**: 
    - The protocol relies heavily on the concept of epochs (measured in weeks) for various functions like rewards, claims, and balances. If there's a flaw in how epochs are calculated or if the timing gets disrupted due to network issues, it could affect the accuracy of rewards and claims.

- **Reward Calculations**: 
    - The calculation of rewards involves several variables, including market weights, balances, and reward amounts. If any of these variables are inaccurately calculated, it could lead to incorrect distribution of rewards, potentially causing dissatisfaction among users.

- **Lending Market Whitelist**: 
    - The whitelist for lending markets is controlled by governance. If there's centralized control over whitelisting and governance decisions, it could result in concentration of power and decision-making, which goes against the principles of decentralization.

### Centralization Risks:

- **Governance Control**: 
    - The governance address is hard-coded in the contract. If the governance becomes inactive or malicious, it could lead to a centralized decision-making process or even potential abuse of power.

- **Market Whitelisting**: 
    - The ability for governance to whitelist or blacklist lending markets could lead to centralization if decisions are made without involving the broader community. Centralized control over market participation can hinder innovation and competition.

- **Reward Settings**: 
    - Governance's control over reward settings introduces centralization in determining how rewards are distributed. Mismanagement of rewards or a lack of transparency in setting rewards could lead to dissatisfaction among users.

- **Dependent Contracts**: 
    - The protocol relies on external contracts like `VotingEscrow` and `GaugeController`. If these contracts are compromised or misconfigured, it could impact the overall functionality of the `veRWA project`.

- **Rewards Allocation**: 
    - The distribution of rewards based on market weights controlled by `GaugeController` could be centralized if the controller's decisions are not well-distributed or transparently managed.

It's important to address these risks through careful auditing, community involvement, decentralization measures, and ongoing monitoring. The protocol should strive to minimize centralization, ensure transparency, and have contingency plans for potential disruptions or flaws in the system.

## 7- Security Approach of the Project
What the project should add in the understanding of Security?

- **Input Validation**: 
    - Ensuring that inputs to functions are properly validated and sanitized to prevent unexpected behavior or vulnerabilities like integer overflows/underflows, division by zero, etc.

- **Access Control**: 
    - Implementing proper access controls to restrict sensitive functions and data to authorized entities only. It seems that there is already an onlyGovernance modifier in the `LendingLedger.sol` contract for certain functions, which suggests some level of access control.

- **External Contract Interaction**: 
    - Ensuring secure interactions with external contracts to prevent unexpected behavior or vulnerabilities. This involves validating return values, using established interfaces, and handling failures gracefully.


- **Audits and Testing**: 
    - Conducting comprehensive security audits by reputable third-party auditors and performing extensive testing, including unit testing, integration testing, and scenario-based testing.

- **Code Simplicity and Clarity**: 
    - Keeping the codebase simple and easy to understand, which can reduce the risk of introducing vulnerabilities due to complex logic.

- **Known Vulnerability Mitigation-**: 
    - Staying updated with the latest developments in the blockchain and smart contract security space to mitigate known vulnerabilities.

- **Emergency Measures**: 
    - Implementing mechanisms for pausing or upgrading contracts in case of vulnerabilities or emergencies.

- **Continuous Monitoring and Response**: 
    - Implementing monitoring and response mechanisms to detect anomalies or attacks and respond to them in a timely manner.

## 8- Other Audit Reports and Automated Findings 

**Automated Findings:**
https://github.com/code-423n4/2023-08-verwa/blob/main/bot-report.md


## 9- New insights and learning from this audit 
The insights and learnings that could be gained from analyzing the provided smart contracts from the `veRWA project`. 

**Complexity and Design Patterns**: 
    - Analyzing these contracts might offer insights into how complex DeFi protocols are designed and implemented. Understanding the interplay of different contracts, data structures, and design patterns could deepen your understanding of decentralized applications.

**Governance and Voting**: 
    - By studying the `VotingEscrow` contract, you could learn about how decentralized governance and voting mechanisms are implemented. This could be valuable for understanding how community-driven decisions are made in various projects.

**Tokenization and Rewards**: 
- The use of ERC-20 tokens (like CANTO) for rewards and incentives demonstrates the tokenization of value within DeFi ecosystems. Learning about how tokens are distributed as rewards can provide insights into incentivizing users' participation.

## Conclusion 
In general, the `veRWA project` exhibits an interesting and well-developed architecture we believe the team has done a good job regarding the code, but the identified risks need to be addressed, and measures should be implemented to protect the protocol from potential malicious use cases. Additionally, it is recommended to improve the documentation and comments in the code to enhance understanding and collaboration among developers. It is also highly recommended that the team continues to invest in security measures such as mitigation reviews, audits, and bug bounty programs to maintain the security and reliability of the project. 

## Time Spent ⏱
A total of 2 days were spent to cover this audit, broken down into the following:
1. 1st Day: Trying to understand the protocol flows and implementation
2. 2nd Day: We focused on conducting the most comprehensive analysis possible, adding handcrafted diagrams based on the contracts and information provided by the protocol.













### Time spent:
24 hours