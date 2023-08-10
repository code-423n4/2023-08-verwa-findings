## [I-01] Not handled result 
t does not handle the return result from the functions `_get_sum` and `_get_weight`.

Instances: [142-143](https://github.com/code-423n4/2023-08-verwa/blob/main/src/GaugeController.sol#L142-L143)

## [I-02] Missed crucial event
The NewGaugeWeight event was not emitted at a crucial place.

Instance: [188](https://github.com/code-423n4/2023-08-verwa/blob/main/src/GaugeController.sol#L188)

## [I-03] Missing Check for _timestamp in is_valid_epoch Modifier
A check for _timestamp != 0 is missing in the is_valid_epoch modifier.

## [I-04] Redundant comment
Comments for documentation on the forked project of FiatDao are present in some places.

Instances: [267](https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L267), [286](https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L286),[325](https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L325),[355](https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L355),[472](https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L472),[486](https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L486),[564](https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L564) and [571](https://github.com/code-423n4/2023-08-verwa/blob/main/src/VotingEscrow.sol#L571)

## [I-05] _claimFromTimestamp should be less than _claimUpToTimestamp
Add additional check for claimFromTimestamp should be less than _claimUpToTimestamp 

Instance: [154](https://github.com/code-423n4/2023-08-verwa/blob/main/src/LendingLedger.sol#L154)