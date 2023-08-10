## Unnecessary `nonReentrant` modifier in the `createLock` function of `VotingEscrow.sol` contract
```
    function createLock(uint256 _value) external payable nonReentrant { 
```
[VotingEscrow.sol#L268](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/VotingEscrow.sol#L268)
The modifier was necessary in the FIAT DAO codebase but in this setup, it should not be as it follows the Check-Effects-Interactions pattern. 

## Wrong comments in the `whiteListLendingMarket` function of `LendingLedger.sol` contract

`whiteListLendingMarket` function in the `LendingLedger.sol` contract allows both whitelisting a lending market and delisting it from the whitelist by submitting `true` or `false` in the `_isWhiteListed` parameter. 
```
    /// @notice Used by governance to whitelist a lending market
    /// @param _market Address of the market to whitelist
    /// @param _isWhiteListed Whether the market is whitelisted or not
```
[LendingLedger.sol#L201](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/LendingLedger.sol#L201)
Recommendation: Please edit the comments accordingly. For instance, replace the first line of comments with "/// @notice Used by governance to assign or remove whitelist status to a lending market"

## Missing checks for `address(0)` in `add_gauge` function of `GaugeController.sol` contract
```
    function add_gauge(address _gauge) external onlyGovernance { 
        require(!isValidGauge[_gauge], "Gauge already exists");
        isValidGauge[_gauge] = true;
        emit NewGauge(_gauge);
    }
```
[GaugeController.sol#L118](https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L118)
Recommendation: add a check for `address(0)` at the beginning of the `add_gauge` function. Example: `require(!address(0), "Zero address");` 
