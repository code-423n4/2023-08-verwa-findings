|     |
| --- |
| \[L‑01\] Empty receive()/payable fallback() function does not authenticate requests |
| \[L-02\] Insufficient coverage |
| \[N-01\] Camel case function name |
| \[N-02\] Function writing that does not comply with the Solidity Style Guide |
| \[N-03\] NatSpec comments should be increased in contracts |
| \[N-04\] Use SMTChecker |
| \[N-05\] Large multiples of ten should use scientific notation |
| \[G-06\] Using private rather than public for constants |
| \[G-07\] Contract declarations should have NatSpec @title annotations |
| \[G-08\]  Consider using delete rather than assigning zero/false to clear values |

## \[L‑01\] Empty receive()/payable fallback() function does not authenticate requests

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. require(msg.sender == address(weth))). Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds. If the concern is having to spend a small amount of gas to check the sender against an immutable address, the code should at least have a function to rescue unused Ether.

```
receive() external payable {}
```

https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/LendingLedger.sol#L209

## \[L-02\] Insufficient coverage

Description
The test coverage rate of the project is ~95%. Testing all functions is best practice in terms of security criteria.

Due to its capacity, test coverage is expected to be 100%.

## \[N-01\] Camel case function name

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

```
function checkpoint() external {
```

https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L262

## \[N-02\] Function writing that does not comply with the Solidity Style Guide

Context
All Contracts

Description
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private
within a grouping, place the view and pure functions last

## \[N-03\] NatSpec comments should be increased in contracts

## \[N-04\] Use SMTChecker

The highest tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification.

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19

## \[N-05\] Large multiples of ten should use scientific notation

```
uint256 public constant MULTIPLIER = 10**18;
```

https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/GaugeController.sol#L17

```
uint256 public constant MULTIPLIER = 10**18;
```

https://github.com/code-423n4/2023-08-verwa/blob/498a3004d577c8c5d0c71bff99ea3a7907b5ec23/src/VotingEscrow.sol#L33C49-L33C49

## \[G-06\] Using private rather than public for constants

> all Contest

## \[G-07\] Contract declarations should have NatSpec @title annotations

## \[G-08\] Consider using delete rather than assigning zero/false to clear values