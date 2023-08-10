# [01] Summary

## 1.1. Introduction

veRWA is a voting escrow implementation for CANTO Real World Assets. The implementation under audit covers the core `GaugeController`, `VotingEscrow` and `LendingLedger` contracts. The first two contracts are heavily based on Curve Finance's original Vyper implementation, while the `LendingLedger` is a 
an original piece of code.
## 1.2. Approach

This was a 3 day contest:

- Day 1 (4 hours)
	- Read `readme.md` and drafted an audit strategy
	- Reviewed RWA & Curve documentation around Liquidity Gauges and Voting Escrow
	- Collected and reviewed audit reports from the projects in the code lineage (Curve Finance, mkt.market, mStable, FIAT DAO)
	- Reviewed tests (24)
- Day 2 (12 hours)
	- Line-by-line review of `GaugeController.sol`, `VotingEscrow.sol` and `LendingLedger.sol`
	- Compared `GaugeController.sol` to Curve's Vyper implementation, line-by-line
	- Compared `VotingEscrow.sol` to FIAT DAO implementation using file diff
	- Verified the implementation against the `Additional Context` section of the `readme.md` file
	- Drafted reports
- Day 3 (6 hours)
	- Finalised and submitted reports

## 1.3. Documentation

The `readme.md` did not link to a project documentation page. I tried to track down `veCANTO` and `veRWA` but was unable to turn up further information.  `RWA` appears to be a generic term, and `CANTO` documentation doesn't appear to reference the project either . The `mkt.market` site didn't appear to have project information.

The code documentation did have very well-documented lineage in 2 out of 3 cases, and I was able to reference sufficient relevant documentation to audit those contracts.

## 1.4. Codebase

Codebase had good test coverage, and was relatively well laid out. 

## 1.5. Mechanism Review

The mechanism here is based heavily on the Curve Finance protocol:
- **RWA Implementation**: veRWA is a non-standard ERC20 token, represented by the VotingEscrow contract on the Ethereum mainnet.
- **Obtaining veRWA**: veRWA cannot be transferred and is obtained by locking CANTO. The maximum lock time is five years.
- **Balance Decay**: A user's veRWA balance decays linearly as the remaining time until the CANTO unlock decreases. The decay is proportional to the lock time.
- **User Voting Power**: Voting power decreases linearly from the moment of lock, and changes are recorded in user_point_history.
- **Total Voting Power**: Changes in total voting power are recorded in point_history, and future slope changes are scheduled in slope_changes.
- **Implementation Efficiency**: The system avoids periodic check-ins and limits the end of user locks to whole weeks, making reads from the blockchain more efficient.
- **Expiration Times**: All possible expiration times are rounded to whole weeks to optimize the number of reads from the blockchain.
- **Liquidity Gauges**: Directs inflation to users providing liquidity, measured via individual liquidity gauges. Users deposit LP tokens into the gauge, receiving a share of minted RWA/CANTO.
- **Gauge Weight Voting**: Users can allocate their veRWA towards gauges, affecting the distribution of newly minted RWA/CANTO tokens. Weight votes are applied at the start of the next epoch week.
- **The Gauge Controller**: Manages the list of gauges and their types and weights, handling the linear character of voting power. Records points, scheduled changes, and per-user slopes.

# [02] Architecture Recommendations

## 2.1. Consider adding an incentive to vote early in `GaugeController`

As discussed in my QA report, voting early reveals information to other voters. This effectively reduces the power of a user's vote. To compensate for this, consider implementing:

- A decreasing vote weight, thus creating an advantage for early voters
- A blind vote

## 2.2. Consider adding ability to remove whitelisted lending markets in `LendingLedger`

While the `readme` states that governance operations are always assumed to be correct, human error should always be considered in protocol design. 

# [03] Risks

## 3.1. Founder Disbursements
The recent CRV hack exposed a problem where one of the founders had used $280 million CRV as loan collateral for a $100 Real World Asset (a house), and the devaluing of the CRV used as collateral put the loan at risk of liquidation.

This would have dumped millions of CRV onto the market, and caused the price of CRV to collapse further. This would have caused liquidity providers to withdraw from Curve.

Fortunately this has not yet happened, but demonstrates the problems of large disbursements of liquidity tokens to founders in these styles of protocols.

# [04] References

- [Curve DAO Security Assessment](https://files.safe.de.fi/safe/files/audit/pdf/CurveDAO.pdf)
- [Curve Finance DAO Voting Security Audit Report](https://github.com/mixbytes/audits_public/blob/master/Curve%20Finance/DAO%20Voting/Curve%20Finance%20DAO%20Voting%20Security%20Audit%20Report.pdf) 

### Time spent:
22 hours