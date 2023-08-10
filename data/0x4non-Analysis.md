## veRWA Smart Contract Audit Report

### Introduction
This report encapsulates a comprehensive analysis of the veRWA smart contracts. Our approach aimed to focus on changes made to the initial codebase, dependencies between the contracts, and standard vulnerabilities.

### Methodology

#### Time Spent:
Approximately 5 hours were dedicated to auditing the codebase.

#### Preliminary Actions:
1. **Test Code Review:** Before delving into the main codebase, a brief perusal of the test codes was carried out to gain an understanding of the test cases and the intended functionality of the smart contracts.

2. **Test Coverage:** An attempt was made to run a test coverage report. Unfortunately, it failed with a `stack too deep` error. This indicates that there may be too many local variables or complex computations within some of the contract functions. This is fixed using the `--via-ir` but somehow this doesnt work to run `fork coverage`

3. **Audit History:** Since the contract is a fork of previously audited code (Curve's and FIAT DAO's implementations), previously available audits for the base code were reviewed. This ensures that any known vulnerabilities in the original contracts were addressed in the veRWA version. I spend some time trying reading prev audits and reviewing bugs on **solodit**

4. **Differential Analysis:** Utilizing tools like Meld, differences between the original fork contracts and the veRWA contracts were identified. Special attention was paid to these changes to ascertain whether they might introduce new vulnerabilities or affect the contracts' behavior unpredictably.

### Key Findings:

#### Reviewed Contracts:
1. **src/GaugeController.sol**
2. **src/VotingEscrow.sol**
3. **src/LendingLedger.sol**

#### Observations:
1. **Dependencies and Libraries:** The @openzeppelin/* libraries were integrated into the GaugeController and VotingEscrow contracts. These are well-audited and trusted libraries in the Ethereum community, which adds a layer of reliability to the contracts. There are alternatives to this that are more gas efficient focused.

2. **Changes in VotingEscrow:** Notably, modifications were made to the FIAT DAO implementation in the VotingEscrow contract. This includes support for underlying native tokens instead of ERC20 and a fixed lock time of 5 years that resets with every action. Such changes need to be tested thoroughly, especially regarding how the lock times are managed and reset.

3. Abuse of `nonReentrant` some how i see this modifier in functions that dont perform a external callback or follow the **CEI** pattern. 

### Recommendations:
1. **Address the 'Stack Too Deep' Error:** The developers should investigate the `stack too deep` error encountered during the test coverage attempt. It could be symptomatic of functions with too many local variables or convoluted computations.

2. **Focus on Changed Sections:** Given the contract's lineage from Curve's and FIAT DAO's implementations, changes made in the veRWA version should be thoroughly tested to ensure they do not inadvertently introduce vulnerabilities.

3. **Ensure Adequate Governance Control:** It's stated that it's the responsibility of the governance to ensure that LendingLedger always contains enough CANTO. Measures should be in place to prevent possible governance failures or oversights.



### Time spent:
4 hours