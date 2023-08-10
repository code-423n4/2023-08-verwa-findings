## Gas-1 Unused Imports

Some files have imports that are not used

[import {VotingEscrow} from "./VotingEscrow.sol"; // Lending Ledge.sol line 4](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L4C31-L4C43)

**Impact:** --> The above leads to bloated code, code readability, code auditability, code maintanability issues as readers may think it implies missing code of functionality. Most importantly it adds to gas costs as it increases the size of the contract file as the unused import is extra bytecode for no reason which increases deployment costs for no reason 

**This doubles as a Non Critical(Informational Issue too)** 

**Recommendation:** --> It is recommended to remove all unused imports in above cases and any other relevant cases that may have been missed
