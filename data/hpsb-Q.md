# Low Risk Findings
#

## Title L-1
ERROR-PRONE TYPECASTING

## Instance 
###### GaugeController.sol#L222-L222
```uint256 slope = uint256(uint128(slope_));```

## Impact
The variable/value `slope_` is found to be typecasted more than once which is not standard, making the contract’s logic more complex, and error-prone.

## Type Of Report
Manual Report

## Remediation
It is recommended to go through the code logic to make sure the typecasting is working as expected and the resultant values are whole.
#
## Title L-2
LONG NUMBER LITERALS

## Instance 
###### VotingEscrow.sol#L37-L37
```
Point[1000000000000000000] public pointHistory;
```
###### VotingEscrow.sol#L38-L38
```
mapping(address => Point[1000000000]) public userPointHistory;
```

## Impact
Solidity supports multiple rational and integer literals, including decimal fractions and scientific notations. The use of very large numbers with too many digits was detected in the code that could have been optimized using a different notation also supported by Solidity.


## Type Of Report
Manual Report

## Remediation
Scientific notation in the form of 2e10 is also supported, where the mantissa can be fractional but the exponent has to be an integer. The literal MeE is equivalent to M * 10**E. Examples include 2e10, 2e10, 2e-10, 2.5e1, as suggested in official solidity documentation https://docs.soliditylang.org/en/latest/types.html#rational-and-integer-literals
#
## Title L-3
USE OF FLOATING PRAGMA

## Instance 
###### LendingLedger.sol#L2-L2
```pragma solidity ^0.8.16;```
###### GaugeController.sol#L2-L2
```pragma solidity ^0.8.16;```
###### VotingEscrow.sol#L2-L2
```pragma solidity ^0.8.16;```

## Impact
Solidity source files indicate the versions of the compiler they can be compiled with using a pragma directive at the top of the solidity file. This can either be a floating pragma or a specific compiler version.
The contract was found to be using a floating pragma which is not considered safe as it can be compiled with all the versions described.
The following affected files were found to be using floating pragma:
`/src/LendingLedger.sol - ^0.8.16`
`/src/GaugeController.sol - ^0.8.16`
`/src/VotingEscrow.sol - ^0.8.16`

## Type Of Report
Manual Report

## Remediation
It is recommended to use a fixed pragma version, as future compiler versions may handle certain language constructions in a way the developer did not foresee.
Using a floating pragma may introduce several vulnerabilities if compiled with an older version.
The developers should always use the exact Solidity compiler version when designing their contracts as it may break the changes in the future.
Instead of `^0.8.16` use `pragma solidity 0.8.18`, which is a stable and recommended version right now.
#

## Title L-4
OUTDATED COMPILER VERSION

## Instance 
###### LendingLedger.sol#L2-L2
```pragma solidity ^0.8.16;```
###### GaugeController.sol#L2-L2
```pragma solidity ^0.8.16;```
###### VotingEscrow.sol#L2-L2
```pragma solidity ^0.8.16;```

## Impact
Using an outdated compiler version can be problematic especially if there are publicly disclosed bugs and issues that affect the current compiler version.

The following outdated versions were detected:
`/src/LendingLedger.sol - ^0.8.16`
`/src/GaugeController.sol - ^0.8.16`
`/src/VotingEscrow.sol - ^0.8.16`

## Type Of Report
Manual Report

## Remediation
It is recommended to use a recent version of the Solidity compiler that should not be the most recent version, and it should not be an outdated version as well. Using very old versions of Solidity prevents the benefits of bug fixes and newer security checks. Consider using the solidity version `0.8.18`, which patches most solidity vulnerabilities.
#

# Informational/Non-Critical Findings
#

## Title I1
UNUSED RECEIVE FALLBACK

## Instance 
###### LendingLedger.sol#L209-L209
```receive() external payable {}```

## Impact
The contract was found to be defining an empty receive function.
It is not recommended to leave them empty unless there’s a specific use case such as to receive Ether via an empty `receive()` function.

## Type Of Report
Slither Report

## Remediation
It is recommended to go through the code to make sure these functions are properly implemented and are not missing any validations in the definition.
