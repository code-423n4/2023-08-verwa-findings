# Codebase quality analysis
- Tests are written for coverage only but lacks in integration test to test interactions between contracts. Multiple vulnerabilities are found in context of cross-contract interaction.
- Features that are different from source contract is not sufficiently documented e.g removal of vote delay of GaugeController.
- The project uses via-ir which is experimental feature. In the light of recent Vyper compiler vulnerability, it is highly advised that the team reconsider usage of via-ir.
- Variables naming are not self-descriptive and there are lots of confusing math equations. More inline-documentation would be appreciated. It helps both auditors and developers themselves to understand the code better.
- Some ported parts are not used. Recommend to remove them.

# Mechanism review
- Vote delegation is a newly added feature that doesn't exist in source contract. This addition is conflicting with decaying voting power. Developers should take great care to ensure that both functionality can work together.
- Despite high reward emission, locking token for 5 years without option for users to pick their own lock time preference discourage some users from participating.
- Per epoch reward distribution can be gamed if not handled properly.

### Time spent:
14 hours