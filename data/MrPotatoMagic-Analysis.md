# Analysis Report

## Comments for the judge to contextualize findings
My issues comprise 2 Highs, 1 Medium, 9 Non-Critical and 5 Low issues. Below listed are some important contextual points on both my high severity findings, 1 Medium and 1 low severity finding.

1. Root cause of both my highs are different - The impact of both the high risk findings is the same (i.e. locking of user's funds) but the root cause is different. The first issue arises due to the user's end time being shorter than the previous assigned delegatee's. The second issue arises due to the user's lock expiring under an existing delegation to another delegatee. Why both these issues look similar (though they're not) is due to the user not being able to transfer delegation back to themselves. Even if we solve the first issue, the second issue still persists. This I believe is proof enough for them to be considered as separate issues. 

2. My Medium issue challenges the ``LOCKTIME`` of 5 years, which is native to the project idea. Due to this, an issue arises where the user does not receive any voting power on deposits below 1825 CANTO. I've added more context on this issue in the following sections below. Additionally, this issue should be specified in the documentation for security researchers in future audits and made aware to the user by mentioning that a minimum deposit of 1825 CANTO is required to receive voting power.

3. One of my low severity issues - ``[L-05] Consider skipping the epoch for which rewards are already set`` - includes my discussions with the sponsor to provide more context on why I think my solution would be more useful for the governance/voters.

## Approach taken in evaluating the codebase
The scope included only 3 files, therefore it was easy to identify the base to child contract path. It as follows:
```
VotingEscrow.sol (parentA) <= GaugeController.sol (childA parentB) <= LendingLedger.sol (childA childB)
```

**Day 1** : Audited VotingEscrow.sol with 413 SLOC (heaviest contract)

- Targeted important functions like createLock(), increaseAmount(), delegate(), withdraw() and _checkPoint() to understand how user locks are created, amounts for existing locks can be increased, delegations are done, withdrawal process and history for each of these functionalities is recorded time-to-time. 
- Ensured all storage updates are correct.
- Followed along the happy case of creating a lock, increasing amount, delegating power and withdrawing tokens to spot issues. This is where I found 2 High issues related to delegation and withdrawals.
- Created written and coded POCs for the 2 issues spotted.
- Added inline bookmarks to QA issues spotted while reading the code.

**Day 2** : Audited GaugeController.sol and LendingLedger.sol with 336 SLOC combined (lighter contracts)

- Targeted important functions in the GaugeController.sol and LendingLedger.sol such as vote_for_gauge_weights() and claim(). Rest of the functions were mainly checkpoint and syncing ledger related where I ensured all storage updates are correct. 
- There was a lot of going back and forth between tests and the code to further ensure that storage updates were correct and aligned with the function behaviour.
- Noted down some architecture recommendations for the setRewards() function.
- Added inline bookmarks to QA issues spotted while reading the code.

**Day 3** : Creating reports and proving validity of findings through tests

- Researching the impact of certain low severity issues.
- Created QA report
- Created Analysis Report

During all 3 days, I had several discussions with the sponsor on the architecture of the codebase (which I have documented in one of my low severity findings), especially because it is an implementation of the FIATDAO VotingEscrow contract with certain functions removed and modifications made.

## Architecture recommendations
The sponsor has kept the codebase one step ahead in terms of security by implementing an [already audited FIATDAO codebase](https://code4rena.com/reports/2022-08-fiatdao#m-05-unsafe-casting-from-int128-can-cause-wrong-accounting-of-locked-amounts). But the modifications made introduce two high severity and 1 Medium severity security bugs. Here is how:

- First modification: The ``increaseUnlockTime()`` function has been removed in the VotingEscrow.sol contract while the [FIATDAO VE contract includes it](https://github.com/code-423n4/2022-08-fiatdao/blob/fece3bdb79ccacb501099c24b60312cd0b2e4bb2/contracts/VotingEscrow.sol#L493). After following the happy case and spotting the 2 high issues, I believe this increaseUnlockTime() function is important and should be added with **access control** to the codebase as it will allow the person with access to extend a user's unlock time to a certain extent, allowing them to withdraw their locked CANTO. 

- Second modification: The ``LOCKTIME`` for a user's lock has been fixed to 5 years, which is native to the project's idea I believe. But this introduces a higher entry barrier to those who want to create locks. To prevent the rounding issue (as mentioned in my issue #299), the user needs to deposit atleast 1825 CANTO to have valid voting power greater than 0. **(Note: The barrier might look low right now since we're in a bear market and 1825 CANTO is around 200 USD at the time of reading. But as we enter the bull market, the barrier becomes higher).** We could say that the barrier is proportional to the price of 1825 CANTO. Even if this LOCKTIME is native to the project idea, I would recommend the sponsor to reconsider it. 

Other than these modifications, there is another change I would recommend in the architecture of the setRewards() function:
1. ``[L-05] Consider skipping the epoch for which rewards are already set`` - This finding is included in my QA report but it is worth mentioning here as well. The governance sets the rewards for epochs in the range X to Y, but if there is an epoch [X+1] in this range for which rewards are already set, all the previous changes made revert. Although this reversion is the desired behaviour (i.e. what the sponsor expects - discussed with sponsor) and can be solved with two separate proposals to go around the epoch [X+1] (rewards for which are already set) in the manner [X,X] and [X+2,Y], I think it might be time consuming and a bit heavy work-wise on the governance's side **if there is more than 1 epoch for which rewards are already set**. That is why I believe using the continue keyword is much better for ease of operation. Additionally, an event can be emitted for an epoch for which rewards are already set in order to make the governance aware of them. (Note: This event emission is necessary as voters are expecting that the rewards will be set for the whole range - as mentioned by the sponsor)

## Codebase quality analysis
The codebase quality is between above average. Why not Medium or High you may ask? I say this due to 2 reasons:
1. The test coverage is above 90%, which puts the codebase one step ahead in terms of security. Due to this, I would assign it the High rank.
2. If one follows the happy case for the process of delegation, the issue of not being able to transfer delegation back to the user has not been tested. Such issues arising in the happy case itself should be thoroughly tested first as it is fundamental to the functioning of the protocol. Due of this, I would assign it the Low rank as the happy case or user flow should be tested first.
3. The sponsor has done an amazing job with storage updates in the [increaseAmount() function](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L288). This is because if you observe the previous findings in the [FIATDAO C4 contest](https://code4rena.com/reports/2022-08-fiatdao#m-03-inconsistent-logic-of-increase-unlock-time-to-the-expired-locks), one can observe how unlock times could be increased on expired locks. This is well mitigated by the sponsor by adding a require statement to check for lock expiry and updating the ``newLocked.amount`` and ``newLocked.delegated`` storage variables correctly. Due to this, I would assign it the High rank.

## Centralization risks
- There is some degree of centralisation introduced through the governance/voters. 
- The implementation of the governance mechanism is out of scope but it is important to ensure it is implemented correctly since there are several configuration functions like add_gauge(), remove_gauge(), whitelisting lender markets and setting epoch rewards, which are critical to the functioning of the codebase.

## Mechanism review

Gauge model used: Linear decay

Function/Equation for this decaying linear model: W(t) = m*t + b , where b is the bias and m is the slope, and t is the time.

Brief explanation of the model: A user can lock up tokens and receive voting power. User's voting power decays linearly over time (in our case over 5 years, i.e. is 0 after 5 years). The VotingEscrow.sol stores the linear function (which is parameterized by a slope and a bias) of this user and then you can always linearly interpolate the time if amount for lock is increased by increaseAmount() function. 

Here is a mindmap created to get an idea of the important contract and their function flow:
https://drive.google.com/file/d/1RQ3WPYnjOEBIg1FDJfJUZDjwhN1Xl1Ua/view?usp=sharing

### Time spent:
30 hours