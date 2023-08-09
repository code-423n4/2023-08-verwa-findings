Title: High cyclomatic complexity

Code: https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L115-L259

Severity: Informational

Details: the _checkpoint function contains multiple conditional statements, loops, and calculations, contributing to its high cyclomatic complexity.

Impact: it makes the code harder to analyze, debug, and maintain.

Recommendation: Reduce cyclomatic complexity by splitting the function into several smaller subroutines.

Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#cyclomatic-complexity