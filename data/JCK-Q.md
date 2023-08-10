
# LOW RISK

| Number | Issue | Instances |
|--------|-------|-----------|
|[LOW-01]| Upgradeable contract uses non-upgradeable version of the OpenZeppelin libraries/contracts | 3 |
|[LOW-02]| Potential division by zero should have zero checks in place | 1 |
|[LOW-03]| FLOATING PRAGMAS | 3 |
|[LOW-04]| Variable being initialized with the default value | 7 |


## [LOW‑01] Upgradeable contract uses non-upgradeable version of the OpenZeppelin libraries/contracts

OpenZeppelin has an Upgradeable variants of each of its libraries and contracts, and upgradeable contracts should use those variants.


```solidity
file:  src/GaugeController.sol

5     import {Math} from "@openzeppelin/contracts/utils/math/Math.sol";
```
https://github.com/code-423n4/2023-08-verwa/tree/main/src/GaugeController.sol#L5


```solidity
file:   src/LendingLedger.sol

6      import {Math} from "@openzeppelin/contracts/utils/math/Math.sol";
```
https://github.com/code-423n4/2023-08-verwa/tree/main/src/LendingLedger.sol#L6

```solidity
file:  src/VotingEscrow.sol

4    import {ReentrancyGuard} from "@openzeppelin/contracts/security/ReentrancyGuard.sol";

```
https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L4


## [LOW-02] Potential division by zero should have zero checks in place

Implement a zero address check for found instances  

```solidity
file:   src/LendingLedger.sol

174    cantoToSend += (marketWeight * userBalance * ri.amount) / (1e18 * marketBalance);

```

https://github.com/code-423n4/2023-08-verwa/tree/main/src/LendingLedger.sol#L174


## [LOW-03] FLOATING PRAGMAS

It is a best practice to lock pragmas instead of using floating pragmas to ensure that contracts are tested and deployed with the intended compiler version. Accidentally deploying contracts with different compiler versions can lead to unexpected risks and undiscovered bugs. Please consider locking pragmas for the following files.

```solidity
file:  src/VotingEscrow.sol

2     pragma solidity ^0.8.16;

```
https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L2


```solidity 
file:  src/LendingLedger.sol

2     pragma solidity ^0.8.16;

```
https://github.com/code-423n4/2023-08-verwa/tree/main/src/LendingLedger.sol#L6#L2


```solidity
file:  src/GaugeController.sol

2     pragma solidity ^0.8.16;

```
https://github.com/code-423n4/2023-08-verwa/tree/main/src/GaugeController.sol#L2

## [LOW-04] Variable being initialized with the default value

Unsigned integers will already be initalized with zero on their declaration, e.g. there’s no need to manually assign zero.



```solidity
file:  src/GaugeController.sol

227     uint256 old_dt = 0;
```
https://github.com/code-423n4/2023-08-verwa/tree/main/src/GaugeController.sol#L227

```solidity
file:   src/VotingEscrow.sol


176      uint256 blockSlope = 0;

433      uint256 min = 0;

452      uint256 min = 0;

505      uint256 dBlock = 0;

506      uint256 dTime = 0;

585      uint256 dTime = 0;

```


https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L176