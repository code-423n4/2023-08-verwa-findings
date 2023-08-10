### veRWA Analysis Report by 0xDetermination

The math models used in this codebase are relatively simple, but the implementation is long, somewhat complicated, and poorly documented. Therefore, it is an area of particular concern. I was only able to find one math-related issue in the alloted contest time, and would have liked more time in order to thoroughly audit the math. The documentation from Curve regarding the math models is very bare-bones, and does not sufficiently cover the details of the implementation. If math models exactly matching the implementation were created, it would greatly increase ease of auditing, as one could simply go through the code and make sure it has no chance of deviating from the specifications.

Another area of particular concern is the interaction between the GaugeController and VotingEscrow contracts, although I did not specifically anticipate this before reading through the codebase. Both of these contracts store similar state values, those being user slope/bias, and global slope/bias; furthermore, GaugeController pulls all of its data from VotingEscrow. The interaction between these two contracts became a specific area of concern for me during the process of the audit, and I was able to find multiple issues stemming from lack of proper synchronization between the two contracts. Significant code design changes may be necessary in order to ensure that these two contracts interface in a more secure manner.

Conversely, I did not find any fundamental issues in the interactions between the LendingLedger and GaugeController contracts, although I spent the least amount of time on the LendingLedger contract and would have liked to spend more time around this area.

There isn't a lot of varied user input that goes into the protocol, so I didn't spend much time on fuzzing or testing inputs; instead, I chose to focus on finding errors in the contract logic. 

Finally, I found that classic issues like re-entrancy and frontrunning were less of a concern.

In conclusion, most of my audit time was spent looking for math and contract logic/interaction errors, and I was able to catch some issues in these areas. Comprehensive documentation covering the math implementations and cross-contract interactions would be very helpful.

### Time spent:
32 hours