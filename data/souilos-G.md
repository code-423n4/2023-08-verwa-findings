# VULN 1 

## [GAS] Use != 0 instead of > 0 for unsigned integer comparison
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 93 at 2023-08-verwa/src/GaugeController.sol:

        if (t > 0) {


Found in line 155 at 2023-08-verwa/src/GaugeController.sol:

        if (total_weight > 0) {


Found in line 129 at 2023-08-verwa/src/VotingEscrow.sol:

            if (_oldLocked.end > block.timestamp && _oldLocked.delegated > 0) {


Found in line 133 at 2023-08-verwa/src/VotingEscrow.sol:

            if (_newLocked.end > block.timestamp && _newLocked.delegated > 0) {


Found in line 167 at 2023-08-verwa/src/VotingEscrow.sol:

        if (epoch > 0) {


Found in line 199 at 2023-08-verwa/src/VotingEscrow.sol:

            if (lastPoint.bias < 0) {


Found in line 203 at 2023-08-verwa/src/VotingEscrow.sol:

            if (lastPoint.slope < 0) {


Found in line 228 at 2023-08-verwa/src/VotingEscrow.sol:

            if (lastPoint.slope < 0) {


Found in line 231 at 2023-08-verwa/src/VotingEscrow.sol:

            if (lastPoint.bias < 0) {


Found in line 272 at 2023-08-verwa/src/VotingEscrow.sol:

        require(_value > 0, "Only non zero amount");


Found in line 291 at 2023-08-verwa/src/VotingEscrow.sol:

        require(_value > 0, "Only non zero amount");


Found in line 293 at 2023-08-verwa/src/VotingEscrow.sol:

        require(locked_.amount > 0, "No lock");


Found in line 314 at 2023-08-verwa/src/VotingEscrow.sol:

            require(locked_.amount > 0, "Delegatee has no lock");


Found in line 329 at 2023-08-verwa/src/VotingEscrow.sol:

        require(locked_.amount > 0, "No lock");


Found in line 359 at 2023-08-verwa/src/VotingEscrow.sol:

        require(locked_.amount > 0, "No lock");


Found in line 382 at 2023-08-verwa/src/VotingEscrow.sol:

        require(toLocked.amount > 0, "Delegatee has no lock");


Found in line 405 at 2023-08-verwa/src/VotingEscrow.sol:

        if (newLocked.amount > 0) {


Found in line 480 at 2023-08-verwa/src/VotingEscrow.sol:

        if (lastPoint.bias < 0) {


Found in line 558 at 2023-08-verwa/src/VotingEscrow.sol:

        if (lastPoint.bias < 0) {


Found in line 110 at 2023-08-verwa/src/LendingLedger.sol:

        require(lendingMarketTotalBalanceEpoch[_market] > 0, "No deposits for this market");


Found in line 122 at 2023-08-verwa/src/LendingLedger.sol:

        require(lendingMarketBalancesEpoch[_market][_lender] > 0, "No deposits for this lender in this market");


Found in line 159 at 2023-08-verwa/src/LendingLedger.sol:

        require(userLastClaimed > 0, "No deposits for this user");


Found in line 178 at 2023-08-verwa/src/LendingLedger.sol:

        if (cantoToSend > 0) {

------------------------------------------------------------------------ 

### Mitigation 

Use != 0 instead of > 0 for unsigned integer comparison.










# VULN 2 

## [GAS] Use shift Right/Left instead of division/multiplication if possible
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 231 at 2023-08-verwa/src/GaugeController.sol:

            slope: (slope * _user_weight) / 10_000,


Found in line 438 at 2023-08-verwa/src/VotingEscrow.sol:

            uint256 mid = (min + max + 1) / 2;


Found in line 458 at 2023-08-verwa/src/VotingEscrow.sol:

            uint256 mid = (min + max + 1) / 2;

------------------------------------------------------------------------ 

### Mitigation 

Use shift Right/Left instead of division/multiplication if possible.










# VULN 3 

## [GAS] ++i/i++ should be unchecked{++i}/unchecked{i++} and ++i costs less gas than i++
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 185 at 2023-08-verwa/src/VotingEscrow.sol:

        for (uint256 i = 0; i < 255; i++) {


Found in line 436 at 2023-08-verwa/src/VotingEscrow.sol:

        for (uint256 i = 0; i < 128; i++) {


Found in line 454 at 2023-08-verwa/src/VotingEscrow.sol:

        for (uint256 i = 0; i < 128; i++) {


Found in line 538 at 2023-08-verwa/src/VotingEscrow.sol:

        for (uint256 i = 0; i < 255; i++) {

------------------------------------------------------------------------ 

### Mitigation 

++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for and while-loops. Moreover ++i costs less gas than i++, especially when its used in for-loops (--i/i-- too).










# VULN 4 

## [GAS] Don’t initialize variables with default value
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 227 at 2023-08-verwa/src/GaugeController.sol:

        uint256 old_dt = 0;


Found in line 176 at 2023-08-verwa/src/VotingEscrow.sol:

        uint256 blockSlope = 0; // dblock/dt


Found in line 185 at 2023-08-verwa/src/VotingEscrow.sol:

        for (uint256 i = 0; i < 255; i++) {


Found in line 433 at 2023-08-verwa/src/VotingEscrow.sol:

        uint256 min = 0;


Found in line 436 at 2023-08-verwa/src/VotingEscrow.sol:

        for (uint256 i = 0; i < 128; i++) {


Found in line 452 at 2023-08-verwa/src/VotingEscrow.sol:

        uint256 min = 0;


Found in line 454 at 2023-08-verwa/src/VotingEscrow.sol:

        for (uint256 i = 0; i < 128; i++) {


Found in line 505 at 2023-08-verwa/src/VotingEscrow.sol:

        uint256 dBlock = 0;


Found in line 506 at 2023-08-verwa/src/VotingEscrow.sol:

        uint256 dTime = 0;


Found in line 538 at 2023-08-verwa/src/VotingEscrow.sol:

        for (uint256 i = 0; i < 255; i++) {


Found in line 585 at 2023-08-verwa/src/VotingEscrow.sol:

        uint256 dTime = 0;

------------------------------------------------------------------------ 

### Mitigation 

In such cases, initializing the variables with default values would be unnecessary and can be considered a waste of gas. Additionally, initializing variables with default values can sometimes lead to unnecessary storage operations, which can increase gas costs. For example, if you have a large array of variables, initializing them all with default values can result in a lot of unnecessary storage writes, which can increase the gas costs of your contract.










# VULN 5 

## [GAS] Use Custom Errors
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 119 at 2023-08-verwa/src/GaugeController.sol:

        require(!isValidGauge[_gauge], "Gauge already exists");


Found in line 128 at 2023-08-verwa/src/GaugeController.sol:

        require(isValidGauge[_gauge], "Invalid gauge address");


Found in line 212 at 2023-08-verwa/src/GaugeController.sol:

        require(_user_weight >= 0 && _user_weight <= 10_000, "Invalid user weight");


Found in line 213 at 2023-08-verwa/src/GaugeController.sol:

        require(isValidGauge[_gauge_addr], "Invalid gauge address");


Found in line 221 at 2023-08-verwa/src/GaugeController.sol:

        require(slope_ >= 0, "Invalid slope");


Found in line 225 at 2023-08-verwa/src/GaugeController.sol:

        require(lock_end > next_time, "Lock expires too soon");


Found in line 241 at 2023-08-verwa/src/GaugeController.sol:

        require(power_used >= 0 && power_used <= 10_000, "Used too much power");


Found in line 272 at 2023-08-verwa/src/VotingEscrow.sol:

        require(_value > 0, "Only non zero amount");


Found in line 273 at 2023-08-verwa/src/VotingEscrow.sol:

        require(msg.value == _value, "Invalid value");


Found in line 274 at 2023-08-verwa/src/VotingEscrow.sol:

        require(locked_.amount == 0, "Lock exists");


Found in line 291 at 2023-08-verwa/src/VotingEscrow.sol:

        require(_value > 0, "Only non zero amount");


Found in line 292 at 2023-08-verwa/src/VotingEscrow.sol:

        require(msg.value == _value, "Invalid value");


Found in line 293 at 2023-08-verwa/src/VotingEscrow.sol:

        require(locked_.amount > 0, "No lock");


Found in line 294 at 2023-08-verwa/src/VotingEscrow.sol:

        require(locked_.end > block.timestamp, "Lock expired");


Found in line 314 at 2023-08-verwa/src/VotingEscrow.sol:

            require(locked_.amount > 0, "Delegatee has no lock");


Found in line 315 at 2023-08-verwa/src/VotingEscrow.sol:

            require(locked_.end > block.timestamp, "Delegatee lock expired");


Found in line 329 at 2023-08-verwa/src/VotingEscrow.sol:

        require(locked_.amount > 0, "No lock");


Found in line 330 at 2023-08-verwa/src/VotingEscrow.sol:

        require(locked_.end <= block.timestamp, "Lock not expired");


Found in line 331 at 2023-08-verwa/src/VotingEscrow.sol:

        require(locked_.delegatee == msg.sender, "Lock delegated");


Found in line 347 at 2023-08-verwa/src/VotingEscrow.sol:

        require(success, "Failed to send CANTO");


Found in line 359 at 2023-08-verwa/src/VotingEscrow.sol:

        require(locked_.amount > 0, "No lock");


Found in line 360 at 2023-08-verwa/src/VotingEscrow.sol:

        require(locked_.delegatee != _addr, "Already delegated");


Found in line 382 at 2023-08-verwa/src/VotingEscrow.sol:

        require(toLocked.amount > 0, "Delegatee has no lock");


Found in line 383 at 2023-08-verwa/src/VotingEscrow.sol:

        require(toLocked.end > block.timestamp, "Delegatee lock expired");


Found in line 384 at 2023-08-verwa/src/VotingEscrow.sol:

        require(toLocked.end >= fromLocked.end, "Only delegate to longer lock");


Found in line 488 at 2023-08-verwa/src/VotingEscrow.sol:

        require(_blockNumber <= block.number, "Only past block number");


Found in line 573 at 2023-08-verwa/src/VotingEscrow.sol:

        require(_blockNumber <= block.number, "Only past block number");


Found in line 37 at 2023-08-verwa/src/LendingLedger.sol:

        require(_timestamp % WEEK == 0 || _timestamp == type(uint256).max, "Invalid timestamp");


Found in line 110 at 2023-08-verwa/src/LendingLedger.sol:

        require(lendingMarketTotalBalanceEpoch[_market] > 0, "No deposits for this market");


Found in line 122 at 2023-08-verwa/src/LendingLedger.sol:

        require(lendingMarketBalancesEpoch[_market][_lender] > 0, "No deposits for this lender in this market");


Found in line 131 at 2023-08-verwa/src/LendingLedger.sol:

        require(lendingMarketWhitelist[lendingMarket], "Market not whitelisted");


Found in line 136 at 2023-08-verwa/src/LendingLedger.sol:

        require(updatedLenderBalance >= 0, "Lender balance underflow"); // Sanity check performed here, but the market should ensure that this never happens


Found in line 141 at 2023-08-verwa/src/LendingLedger.sol:

        require(updatedMarketBalance >= 0, "Market balance underflow"); // Sanity check performed here, but the market should ensure that this never happens


Found in line 159 at 2023-08-verwa/src/LendingLedger.sol:

        require(userLastClaimed > 0, "No deposits for this user");


Found in line 172 at 2023-08-verwa/src/LendingLedger.sol:

                require(ri.set, "Reward not set yet"); // Can only claim for epochs where rewards are set, even if it is set to 0


Found in line 180 at 2023-08-verwa/src/LendingLedger.sol:

            require(success, "Failed to send CANTO");


Found in line 195 at 2023-08-verwa/src/LendingLedger.sol:

            require(!ri.set, "Rewards already set");


Found in line 205 at 2023-08-verwa/src/LendingLedger.sol:

        require(lendingMarketWhitelist[_market] != _isWhiteListed, "No change");

------------------------------------------------------------------------ 

### Mitigation 

Instead of using error strings, to reduce deployment and runtime cost, you should use Custom Errors. This would save both deployment and runtime cost.










# VULN 6 

## [GAS] Use calldata instead of memory for function arguments that do not get mutated
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 412 at 2023-08-verwa/src/VotingEscrow.sol:

    function _copyLock(LockedBalance memory _locked) internal pure returns (LockedBalance memory) {


Found in line 533 at 2023-08-verwa/src/VotingEscrow.sol:

    function _supplyAt(Point memory _point, uint256 _t) internal view returns (uint256) {

------------------------------------------------------------------------ 

### Mitigation 

Mark data types as calldata instead of memory where possible. This makes it so that the data is not automatically loaded into memory. If the data passed into the function does not need to be changed (like updating values in an array), it can be passed in as calldata. The one exception to this is if the argument must later be passed into another function that takes an argument that specifies memory storage.










# VULN 7 

## [GAS] <x> += <y> Costs More Gas Than <x> = <x> + <y> For State Variables
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 71 at 2023-08-verwa/src/GaugeController.sol:

            t += WEEK;


Found in line 97 at 2023-08-verwa/src/GaugeController.sol:

                t += WEEK;


Found in line 260 at 2023-08-verwa/src/GaugeController.sol:

            points_weight[_gauge_addr][next_time].slope += new_slope.slope;


Found in line 261 at 2023-08-verwa/src/GaugeController.sol:

            points_sum[next_time].slope += new_slope.slope;


Found in line 269 at 2023-08-verwa/src/GaugeController.sol:

        changes_weight[_gauge_addr][new_slope.end] += new_slope.slope;


Found in line 270 at 2023-08-verwa/src/GaugeController.sol:

        changes_sum[new_slope.end] += new_slope.slope;


Found in line 276 at 2023-08-verwa/src/VotingEscrow.sol:

        locked_.amount += int128(int256(_value));


Found in line 278 at 2023-08-verwa/src/VotingEscrow.sol:

        locked_.delegated += int128(int256(_value));


Found in line 300 at 2023-08-verwa/src/VotingEscrow.sol:

        newLocked.amount += int128(int256(_value));


Found in line 305 at 2023-08-verwa/src/VotingEscrow.sol:

            newLocked.delegated += int128(int256(_value));


Found in line 317 at 2023-08-verwa/src/VotingEscrow.sol:

            newLocked.delegated += int128(int256(_value));


Found in line 398 at 2023-08-verwa/src/VotingEscrow.sol:

            newLocked.delegated += value;


Found in line 71 at 2023-08-verwa/src/LendingLedger.sol:

            for (uint256 i = lastUserUpdateEpoch; i <= updateUntilEpoch; i += WEEK) {


Found in line 92 at 2023-08-verwa/src/LendingLedger.sol:

            for (uint256 i = lastMarketUpdateEpoch; i <= updateUntilEpoch; i += WEEK) {


Found in line 168 at 2023-08-verwa/src/LendingLedger.sol:

            for (uint256 i = claimStart; i <= claimEnd; i += WEEK) {


Found in line 174 at 2023-08-verwa/src/LendingLedger.sol:

                cantoToSend += (marketWeight * userBalance * ri.amount) / (1e18 * marketBalance); // (marketWeight / 1e18) * (userBalance / marketBalance) * ri.amount;


Found in line 193 at 2023-08-verwa/src/LendingLedger.sol:

        for (uint256 i = _fromEpoch; i <= _toEpoch; i += WEEK) {

------------------------------------------------------------------------ 

### Mitigation 

When you use the += operator on a state variable, the EVM has to perform three operations: load the current value of the state variable, add the new value to it, and then store the result back in the state variable. On the other hand, when you use the = operator and then add the values separately, the EVM only needs to perform two operations: load the current value of the state variable and add the new value to it. Better use <x> = <x> + <y> For State Variables.










# VULN 8 

## [GAS] Multiple Address Mappings Can Be Combined Into A Single Mapping Of An Address To A Struct, Where Appropriate
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 26 and 27 at 2023-08-verwa/src/GaugeController.sol:

    mapping(address => bool) public isValidGauge;

    mapping(address => mapping(address => VotedSlope)) public vote_user_slopes;


Found in line 28 and 29 at 2023-08-verwa/src/GaugeController.sol:

    mapping(address => uint256) public vote_user_power;

    mapping(address => mapping(address => uint256)) public last_user_vote;


Found in line 31 and 32 at 2023-08-verwa/src/GaugeController.sol:

    mapping(address => mapping(uint256 => Point)) public points_weight;

    mapping(address => mapping(uint256 => uint256)) public changes_weight;


Found in line 35 and 36 at 2023-08-verwa/src/GaugeController.sol:

    mapping(uint256 => Point) points_sum;

    mapping(uint256 => uint256) changes_sum;


Found in line 38 and 39 at 2023-08-verwa/src/VotingEscrow.sol:

    mapping(address => Point[1000000000]) public userPointHistory;

    mapping(address => uint256) public userPointEpoch;


Found in line 40 and 41 at 2023-08-verwa/src/VotingEscrow.sol:

    mapping(uint256 => int128) public slopeChanges;

    mapping(address => LockedBalance) public locked;


Found in line 16 and 17 at 2023-08-verwa/src/LendingLedger.sol:

    /// @dev Lending Market => Lender => Epoch => Balance

    mapping(address => mapping(address => mapping(uint256 => uint256))) public lendingMarketBalances; // cNote balances of users within the lending markets, indexed by epoch


Found in line 20 and 21 at 2023-08-verwa/src/LendingLedger.sol:

    /// @dev Lending Market => Epoch => Balance

    mapping(address => mapping(uint256 => uint256)) public lendingMarketTotalBalance; // Total balance locked within the market, i.e. sum of lendingMarketBalances for all

------------------------------------------------------------------------ 

### Mitigation 

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.










# VULN 9 

## [GAS] Do not calculate constants
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 17 at 2023-08-verwa/src/GaugeController.sol:

    uint256 public constant MULTIPLIER = 10**18;


Found in line 33 at 2023-08-verwa/src/VotingEscrow.sol:

    uint256 public constant MULTIPLIER = 10**18;

------------------------------------------------------------------------ 

### Mitigation 

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.










# VULN 10 

## [GAS] With assembly, .call (bool success) transfer can be done gas-optimized
------------------------------------------------------------------------ 

### Proof of concept 

Found in 2023-08-verwa/src/VotingEscrow.sol (function withdraw() external nonReentrant {):

        // oldLocked can have either expired <= timestamp or zero end

        // currentLock has only 0 end

        // Both can have >= 0 amount

        _checkpoint(msg.sender, locked_, newLocked);

        // Send back deposited tokens

        (bool success, ) = msg.sender.call{value: amountToSend}("");

        require(success, "Failed to send CANTO");

        emit Withdraw(msg.sender, amountToSend, LockAction.WITHDRAW, block.timestamp);

    }



    /// ~~~~~~~~~~~~~~~~~~~~~~~~~~ ///



Found in 2023-08-verwa/src/LendingLedger.sol (function claim():

                cantoToSend += (marketWeight * userBalance * ri.amount) / (1e18 * marketBalance); // (marketWeight / 1e18) * (userBalance / marketBalance) * ri.amount;

            }

            userClaimedEpoch[_market][lender] = claimEnd + WEEK;

        }

        if (cantoToSend > 0) {

            (bool success, ) = msg.sender.call{value: cantoToSend}("");

            require(success, "Failed to send CANTO");

        }

    }



    /// @notice Used by governance to set the overall CANTO rewards per epoch


------------------------------------------------------------------------ 

### Mitigation 

return data (bool success,) has to be stored due to EVM architecture, but in a usage like below, ‘out’ and ‘outsize’ values are given (0,0), this storage disappears and gas optimization is provided.










# VULN 11 

## [GAS] Empty blocks should be removed or emit something
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 209 at 2023-08-verwa/src/LendingLedger.sol:

    receive() external payable {}

------------------------------------------------------------------------ 

### Mitigation 

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}}).










# VULN 12 

## [GAS] Use double require instead of using &&
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 212 at 2023-08-verwa/src/GaugeController.sol:

        require(_user_weight >= 0 && _user_weight <= 10_000, "Invalid user weight");



Found in line 241 at 2023-08-verwa/src/GaugeController.sol:

        require(power_used >= 0 && power_used <= 10_000, "Used too much power");


------------------------------------------------------------------------ 

### Mitigation 

Using double require instead of operator && can save more gas. When having a require statement with 2 or more expressions needed, place the expression that cost less gas first.










# VULN 13 

## [GAS] bytes constants are more eficient than string constans
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 26 at 2023-08-verwa/src/VotingEscrow.sol:

    string public name;


Found in line 27 at 2023-08-verwa/src/VotingEscrow.sol:

    string public symbol;

------------------------------------------------------------------------ 

### Mitigation 

If the data can fit in 32 bytes, the bytes32 data type can be used instead of bytes or strings, as it is less robust in terms of robustness.










# VULN 14 

## [GAS] require() or revert() statements that check input arguments should be at the top of the function (Also restructured some if’s)
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 51 at 2023-08-verwa/src/GaugeController.sol:

        require(msg.sender == governance);


Found in line 221 at 2023-08-verwa/src/GaugeController.sol:

        require(slope_ >= 0, "Invalid slope");


Found in line 225 at 2023-08-verwa/src/GaugeController.sol:

        require(lock_end > next_time, "Lock expires too soon");


Found in line 241 at 2023-08-verwa/src/GaugeController.sol:

        require(power_used >= 0 && power_used <= 10_000, "Used too much power");


Found in line 274 at 2023-08-verwa/src/VotingEscrow.sol:

        require(locked_.amount == 0, "Lock exists");


Found in line 294 at 2023-08-verwa/src/VotingEscrow.sol:

        require(locked_.end > block.timestamp, "Lock expired");


Found in line 314 at 2023-08-verwa/src/VotingEscrow.sol:

            require(locked_.amount > 0, "Delegatee has no lock");


Found in line 315 at 2023-08-verwa/src/VotingEscrow.sol:

            require(locked_.end > block.timestamp, "Delegatee lock expired");


Found in line 347 at 2023-08-verwa/src/VotingEscrow.sol:

        require(success, "Failed to send CANTO");


Found in line 382 at 2023-08-verwa/src/VotingEscrow.sol:

        require(toLocked.amount > 0, "Delegatee has no lock");


Found in line 383 at 2023-08-verwa/src/VotingEscrow.sol:

        require(toLocked.end > block.timestamp, "Delegatee lock expired");


Found in line 384 at 2023-08-verwa/src/VotingEscrow.sol:

        require(toLocked.end >= fromLocked.end, "Only delegate to longer lock");


Found in line 37 at 2023-08-verwa/src/LendingLedger.sol:

        require(_timestamp % WEEK == 0 || _timestamp == type(uint256).max, "Invalid timestamp");


Found in line 42 at 2023-08-verwa/src/LendingLedger.sol:

        require(msg.sender == governance);


Found in line 136 at 2023-08-verwa/src/LendingLedger.sol:

        require(updatedLenderBalance >= 0, "Lender balance underflow"); // Sanity check performed here, but the market should ensure that this never happens


Found in line 141 at 2023-08-verwa/src/LendingLedger.sol:

        require(updatedMarketBalance >= 0, "Market balance underflow"); // Sanity check performed here, but the market should ensure that this never happens


Found in line 159 at 2023-08-verwa/src/LendingLedger.sol:

        require(userLastClaimed > 0, "No deposits for this user");


Found in line 172 at 2023-08-verwa/src/LendingLedger.sol:

                require(ri.set, "Reward not set yet"); // Can only claim for epochs where rewards are set, even if it is set to 0


Found in line 180 at 2023-08-verwa/src/LendingLedger.sol:

            require(success, "Failed to send CANTO");


Found in line 195 at 2023-08-verwa/src/LendingLedger.sol:

            require(!ri.set, "Rewards already set");

------------------------------------------------------------------------ 

### Mitigation 

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting alot of gas in a function that may ultimately revert in the unhappy case.
