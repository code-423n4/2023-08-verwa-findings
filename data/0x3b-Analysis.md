### Approach
Originally I planned to do full audit on the code, spending approximately 24H. I was only able to review the code in the first day, and with it there was not much to be founds. The code is solid, as it was mostly forked from other projects.

### Findings
As with limited time I was not able to finds many issues. Spend half of it in the test trying to break different invariant to no avail. At the end 1 LOW and 1 refactoring were sent. 

### Code
The entire code-base was interesting, as it introduced me to a novel concept I hadn't encountered before: a linear voting graph with timed checkpoints. I found the code to be robust and well-structured, even the newly developed **LedndingLedger** performed effectively despite being built from scratch.

### Time spent:
6 hours