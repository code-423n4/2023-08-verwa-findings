**src/LendingLedger.sol**
- L4 - VotingEscrow import of contract that is not used are made, apart from generating an extra amount of gas in the deploy, it also generates little readability in the code when viewing the code in the blockchain explorer.

- L36/41/55/83/106/117/129/188/204 - The names of the functions respond to different writing styles, some functions are camelCase such as: onlyGovernance and setRewards. But others are underscore case: is_valid_epoch or sync_ledger.
Only one way should be respected, to maintain a standard.

- L47/48 - No validation is performed in the constructor and the variables (gaugeController and governance) are immutable, therefore they should be validated before setting the variable to != 0x.

- L174 - A division is made by 1e18 * marketBalance and if marketBalance == 0 it would generate an exception, therefore a previous validation should be carried out to handle it, if it happens.


**src/GaugeController.sol**
- L24/26/27/28/29/31/32/35/36/50/66/91/118/127/141/152 - The name of the functions and variables in storage respond to different writing styles, some are camelCase like: votingEscrow or isValidGauge. But others are underscore case: vote_user_slopes or last_user_vote.
Only one way should be respected, to maintain a standard.

- L69 - The number 500 is used, but the reason why is not explained, this should be explained to increase the understanding of the code.

- L211 - The vote_for_gauge_weights() function has 67 lines and within it many logics are performed, therefore it would be beneficial to raise the level of understanding, create auxiliary functions.

- L58/59 - No validation is performed in the constructor and the variables (votingEscrow and governance) are immutable, therefore they should be validated before setting the variable to != 0x.


**src/VotingEscrow.sol**
- L62/64 - Two values ​​(INCREASE_TIME and QUIT) are created inside the LockAction enum that are never used, this can cause confusion, therefore the ideal would be to eliminate it.
