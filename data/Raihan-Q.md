## [L-01] Contracts are not using their OZ upgradeable counterparts
Description
The non-upgradeable standard version of OpenZeppelin's library, such as `Ownable`, `Pausable`, `Address`, `Context`, `SafeERC20`, `ERC1967Upgrade` etc, are inherited / used by both the proxy and the implementation contracts.

As a result, when attempting to use the upgrades plugin mentioned, the following errors are raised:

```
Error: Contract `FiatTokenV1` is not upgrade safe

contracts/v1/FiatTokenV1.sol:58: Variable `totalSupply_` is assigned an initial value
  Move the assignment to the initializer
  https://zpl.in/upgrades/error-004

contracts/v1/Pausable.sol:49: Variable `paused` is assigned an initial value
  Move the assignment to the initializer
  https://zpl.in/upgrades/error-004

contracts/v1/Ownable.sol:28: Contract `Ownable` has a constructor
  Define an initializer instead
  https://zpl.in/upgrades/error-001

contracts/util/Address.sol:186: Use of delegatecall is not allowed
  https://zpl.in/upgrades/error-002
       
```
Having reviewed these errors, none had any adversarial impact:

`totalSupply_` and `paused` are explictly assigned the default values `0` and `false`
the implementation contracts utilises the internal `_transferOwnership()` in the initializer, thus transferring ownership to `newOwner` regardless of who the current owner is
`Address's` `delegatecall` is only used by the `ERC1967Upgrade` contract. Comparing both the `Address` and `ERC1967Upgrade` contracts against their upgradeable counterparts show similar behaviour (differences are some refactoring done to shift the delegatecall into the `ERC1967Upgrade` contract).
Nevertheless, it would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

### Recommended Mitigation Steps
Where applicable, use the contracts from `@openzeppelin/contracts-upgradeable` instead of `@openzeppelin/contracts`.

```solidity
4    import {ReentrancyGuard} from "@openzeppelin/contracts/security/ReentrancyGuard.sol";
```
https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L4


## [L-02] Low-level calls that are unnecessary for the system should be avoided
Low-level calls that are unnecessary for the system should be avoided whenever possible because low-level calls behave differently from a contract-type call. For example;

address.call(abi.encodeWithSelector("fancy(bytes32)", mybytes))`` does not verify that a target is actually a contract, while ContractInterface(address).fancy(mybytes) does.

Additionally, when calling out to functions declared view/pure, the solidity compiler would actually perform a staticcall providing additional security guarantees while a low-level call does not. Similarly, return values have to be decoded manually when performing low-level calls.

```
179   (bool success, ) = msg.sender.call{value: cantoToSend}("");
```
https://github.com/code-423n4/2023-08-verwa/tree/main/src/LendingLedger.sol#L179


```
346   (bool success, ) = msg.sender.call{value: amountToSend}("");
```
https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol#L346


## [L-03] Execution at deadlines should be allowed
The condition may be wrong in these cases, as when block.timestamp is equal to the compared > or < variable these blocks will not be executed.

### note: this issue not completly find by bots i find this but bots miss this
```solidity
70   if (t > block.timestamp) break;

82   if (t > block.timestamp) time_sum = t;

96   if (t > block.timestamp) break;

108   if (t > block.timestamp) time_weight[_gauge_addr] = t;

263  if (old_slope.end > block.timestamp) {
```
https://github.com/code-423n4/2023-08-verwa/tree/main/src/GaugeController.sol#L70

## [L-04] External calls in an un-bounded for-loop may result in a DOS
Consider limiting the number of iterations in for-loops that make external calls.

```
193   for (uint256 i = _fromEpoch; i <= _toEpoch; i += WEEK) {
```
https://github.com/code-423n4/2023-08-verwa/tree/main/src/LendingLedger.sol#L193