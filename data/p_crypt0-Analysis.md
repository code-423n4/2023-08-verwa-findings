# Approach
First I reviewed the content on the C4A webpage about the documentation for the project to gain an understanding of each of the three contracts in play.

After this, I investigated GaugeController.sol to explore changes in logic from the CURVE implementation to this one. I was satisfied with the logic verwa adapted and swiftly moved on to the LendingLedger.sol contract which handles internal accounting.

I perceived LendingLedger.sol to be the riskiest aspect of the codebase, since typically accounting errors and off by one errors can wreak havoc. Upon investigation, I had a couple of points that I needed clarifying by the sponsor. Please find my correspondence below:

## Beginning of Correspondence

p_crypt0 — 08/07/2023 11:13 PM
Hi, Can you tell me what the 500 signifies in GaugeController's for loops, please?
Line 69 is an example
Lambda — 08/07/2023 11:16 PM
Hi, this is the upper limit for the number of weeks to forward (ported from Curve's GaugeController). Basically a bounded version of while(true)
p_crypt0 — 08/07/2023 11:17 PM
I see, so up to 500 weeks?
p_crypt0 — 08/07/2023 11:17 PM
Thank you for the swift response by the way, appreciate it!
Lambda — 08/07/2023 11:21 PM
Yep exactly, up to 500 weeks. Sure no problem, happy to help
p_crypt0 — Today at 8:10 AM
Hi, the claim function in LendingLedger.sol retrieves the UserClaimedEpoch[_market][lender] prior to running _checkpoint_lender() call.

This could be problematic since the UserClaimedEpoch is only set when the user calls sync_ledger() as it then performs a checkpoint_lender() operation where the UserClaimedEpoch is set?

So, the claim() function only becomes active after a user has called sync_ledger()?
Lambda — Today at 8:39 AM
Exactly, but before the first sync_ledger call for a user / market, there is nothing to claim for this user / market combination. This is the reason we do it like that
p_crypt0 — Today at 12:14 PM
Okay, great. So the LendingMarket contract which will call the sync_ledger(), is going to be decided at a later date through governance voting?

The reason I ask, is because it seems the lendingmarket has ultimate control over user's direct positions through the _delta parameter. How can users be certain that the LendingMarket will not act nefariously and alter their positions arbitrarily through a manipulated call to sync_ledger()?
Say the lending_market passed a call: (lender, int256.max)

The contract would first check it was whitelisted for authorisation, then checkpoint_ledger to get the latest position data,  then perform a manipulation on lender balance through delta
Lambda — Today at 12:18 PM
Yes, lending contracts will be whitelisted smart contracts and it will be decided later by the governance which will be whitelisted. Before one will be whitelisted, it will be ensured that they adhere to the interface, i.e. only call sync_ledger when a user deposits / withdraws 
﻿
Lambda
Lambda#9382

## End of correspondence

# Learnings and comments for judges
One drawback from this contest was, because only a partial picture was presented to us, it was difficult to assess whether all aspects of the lending logic posed minimal security risk. For example, no base implementation of a LendingMarket was implemented in this project, which stores necessary information with regards to user deposits and withdraws. As such, code from the code-base itself appears prone to compromise when in actual fact the final product may not have these security flaws, depending on implementation. But then again, I guess that's the point of the product :)

### Time spent:
10 hours