### NC1 - Unused files/folder interface defined but not used 

[/interfaces/Turnstile.sol](https://github.com/code-423n4/2023-08-verwa/blob/main/interface/Turnstile.sol) 
Above interface file in interfaces is defined but never used in any of the contracts that are in scope. Audit scope does only excludes test files from audit scope.

**Impact**-> it affects the code interpretability, maintainability, auditability or it may signify missing logic 

**Recommendation** -> It is recommended to remove the interface folder if not needed or ensure all logic has been captured for the contracts in case it is needed. 

### NC2 - Inconsistent use of underscore _ preceding function arguments 

[function _delegate(address addr,LockedBalance memory _locked, int128 value, LockAction action) internal // VotingEscrow.sol line 390 ](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L390)
All other function arguments in VotingEscrow.sol use _ but for function above arguments address addr, int128 value, LockAction action are not like LockedBalance memory _locked which takes underscore, this is inconsistent within the function and across the file and other contracts. Contracts LendingLedger.so and GaugeController.sol also use preceding _ underscore for all function arguments.  

**Impact**-> it affects the code interpretability, maintainability, readability and consistency 

**Recommendation** -> It is recommended to apply consistent usage of underscore _ to all function arguments 
 
### NC3 - Consider using SafeTransferLib.safeTransferETH() or Address.sendValue() for clearer semantic meaning

[See similar Code4rena finding in other project for parts where .call was used to send value - > NC-70](https://gist.github.com/thebrittfactor/691192755086c4aa311b69a9de0690f3)

The instances that can be improved in the current code are as follows 

[(bool success, ) = msg.sender.call{value: amountToSend}(""); //VotingEscrow.sol line 346](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L346)
[(bool success, ) = msg.sender.call{value: cantoToSend}(""); // LendingLedger.sol line 179 ](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L179)

**Impacts** - affects readability and semantic understanding. Above also does not explicitly describe operation

**Recommendation** -> to improve readability, semantic understanding and explicitness that better captures operation description( due to naming convention) use OpenZeppelin's SafeTransferLib.safeTransferETH() or Address.sendValue().  Usage over lower-level calls enhances the maintainability of code by clearly indicating the purpose of the function.

### NC-4 Incorrect of inappropriate use of comments 

[ // State - GaugeController.sol line 23- line 53 ](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L23)
```
modifier onlyGovernance() {
        require(msg.sender == governance);
        _;
    }
```
Under the comment for state we get state variables or state related items like Structs. However we also have a modifier without comment its a modifiers so makes it appear as if its under state items 

[ // Send back deposited tokens - VotingEscrow.sol line 345](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L345)
```
(bool success, ) = msg.sender.call{value: amountToSend}("");
```
Above makes it appear as if a token is being sent when its "native token" or coin as this is CANTO being send, readers may think or expect some other token 


