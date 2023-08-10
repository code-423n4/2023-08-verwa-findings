I was planning to work on my audit contests Monday, Tuesday, Wednesday but due to a personal/family emergency I was only able to audit a few hours.
Hopefully the new contests from 10 August onwards will be better for me, as my focus is now back to 100%.

Ok, this codebase is so full of calculations which I dont understand yet because I had to rush, speedrun through it. But so far it seems like a solid codebase, I could not find any medium/high risk bugs, although maybe invariant fuzz testing would uncover something, but I still need to learn exactly how to do invariant testing. As mentioned before, I will focus on this too now, both echidna's and foundry's invariant fuzzers.
I think a good candidate for fuzz testing would be vote_for_gauge_weights() function in GaugeController.sol.

Not the type of protocol or codebase that excites me very much when I start hunting for bugs, probably because it's already solid, and mathematical operations literally everywhere.

Hopefully I found at least one legit QA finding.

I still have some LoC to go through, and the time is already almost up.

Thanks

### Time spent:
6 hours