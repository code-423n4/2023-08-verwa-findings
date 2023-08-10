## [N-01] Use named parameters for mapping type declarations

Consider using named parameters in mappings (e.g. *mapping(address account => uint256 balance)*) to improve readability. This feature is present since Solidity 0.8.18.

There are 20 instances of this issue in 3 files:

    File: src/GaugeController.sol	

    26: mapping(address => bool) public isValidGauge;

    27: mapping(address => mapping(address => VotedSlope)) public vote_user_slopes;

    28: mapping(address => uint256) public vote_user_power;

    29: mapping(address => mapping(address => uint256)) public last_user_vote;

    31: mapping(address => mapping(uint256 => Point)) public points_weight;

    32: mapping(address => mapping(uint256 => uint256)) public changes_weight;

    33: mapping(address => uint256) time_weight;

    35: mapping(uint256 => Point) points_sum;

    36: mapping(uint256 => uint256) changes_sum;

https://github.com/code-423n4/2023-08-verwa/tree/main/src/GaugeController.sol

    File: src/VotingEscrow.sol	

    38: mapping(address => Point[1000000000]) public userPointHistory;

    39: mapping(address => uint256) public userPointEpoch;

    40: mapping(uint256 => int128) public slopeChanges;

    41: mapping(address => LockedBalance) public locked;

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol

    File: src/LendingLedger.sol	

    15: mapping(address => bool) public lendingMarketWhitelist;

    17: mapping(address => mapping(address => mapping(uint256 => uint256))) public lendingMarketBalances; // cNote balances of users within the lending markets, indexed by epoch

    19: mapping(address => mapping(address => uint256)) public lendingMarketBalancesEpoch; // Epoch when the last update happened

    21: mapping(address => mapping(uint256 => uint256)) public lendingMarketTotalBalance; // Total balance locked within the market, i.e. sum of lendingMarketBalances for all

    23: mapping(address => uint256) public lendingMarketTotalBalanceEpoch; // Epoch when the last update happened

    26: mapping(address => mapping(address => uint256)) public userClaimedEpoch; // Until which epoch a user has claimed for a particular market (exclusive this value)

    32: mapping(uint256 => RewardInformation) public rewardInformation;

https://github.com/code-423n4/2023-08-verwa/tree/main/src/LendingLedger.sol

## [N-02] Lack of address(0) checks in the constructor

Zero-address check should be used in the constructors, to avoid the risk of setting smth as address(0) at deploying time.

There are 2 instances of this issue in 2 files:

    File: src/GaugeController.sol	

    /// @audit _votingEscrow, _governance
    57: constructor(address _votingEscrow, address _governance) {

https://github.com/code-423n4/2023-08-verwa/tree/main/src/GaugeController.sol

    File: src/LendingLedger.sol	

    /// @audit _gaugeController, _governance
    46: constructor(address _gaugeController, address _governance) {

https://github.com/code-423n4/2023-08-verwa/tree/main/src/LendingLedger.sol

## [N-03] According to the syntax rules, use *=> mapping (* instead of *=> mapping(* using spaces as keyword

There are 8 instances of this issue in 2 files:

    File: src/GaugeController.sol	

    27: mapping(address => mapping(address => VotedSlope)) public vote_user_slopes;

    29: mapping(address => mapping(address => uint256)) public last_user_vote;

    31: mapping(address => mapping(uint256 => Point)) public points_weight;

    32: mapping(address => mapping(uint256 => uint256)) public changes_weight;

https://github.com/code-423n4/2023-08-verwa/tree/main/src/GaugeController.sol

    File: src/LendingLedger.sol	

    17: mapping(address => mapping(address => mapping(uint256 => uint256))) public lendingMarketBalances; // cNote balances of users within the lending markets, indexed by epoch

    19: mapping(address => mapping(address => uint256)) public lendingMarketBalancesEpoch; // Epoch when the last update happened

    21: mapping(address => mapping(uint256 => uint256)) public lendingMarketTotalBalance; // Total balance locked within the market, i.e. sum of lendingMarketBalances for all

    26: mapping(address => mapping(address => uint256)) public userClaimedEpoch; // Until which epoch a user has claimed for a particular market (exclusive this value)

https://github.com/code-423n4/2023-08-verwa/tree/main/src/LendingLedger.sol

## [N-04] require()/revert() statements should have descriptive reason strings

There is 1 instance of this issue in 1 file:

    File: src/LendingLedger.sol	

    42: require(msg.sender == governance);

https://github.com/code-423n4/2023-08-verwa/tree/main/src/LendingLedger.sol

## [N-05] Event is missing *indexed* fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it’s not necessarily best to index the maximum allowed per event (threefields). Each *event* should use three *indexed* fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

There are 2 instances of this issue in 1 file:

    File: src/VotingEscrow.sol	

    21: event Deposit(address indexed provider, uint256 value, uint256 locktime, LockAction indexed action, uint256 ts);

    22: event Withdraw(address indexed provider, uint256 value, LockAction indexed action, uint256 ts);

https://github.com/code-423n4/2023-08-verwa/tree/main/src/VotingEscrow.sol

## [N-06] *0 address* check for *asset*

Check of the address to protect the code from 0x0 address problem just in case. This is best practice or instead of suggesting that they verify address != 0x0, you could add some good NatSpec comments explaining what is valid and what is invalid and what are the implications of accidentally using an invalid address.

There are 5 instances of this issue in 1 file:

    File: src/GaugeController.sol	

    91: function _get_weight(address _gauge_addr) private returns (uint256) {

    118: function add_gauge(address _gauge) external onlyGovernance {

    127: function remove_gauge(address _gauge) external onlyGovernance {

    141: function checkpoint_gauge(address _gauge) external {

    152: function _gauge_relative_weight(address _gauge, uint256 _time) private view returns (uint256) {

https://github.com/code-423n4/2023-08-verwa/tree/main/src/GaugeController.sol

