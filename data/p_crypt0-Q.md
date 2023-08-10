# [QA report]

## [Non-Critical] GaugeController.sol ToDos not implemented

Line 59 recommends changing `_governance` address to oracle, change not implemented yet.

Developers should consider either implementing change or removing the ToDo for QA purposes.

## [Non-Critical] Use of Magic Numbers not recommended in GaugeController.sol

The use of magic numbers is bad practice across software development, since it makes code difficult to read and the intentions behind it impossible to understand at first glance.

Thus, I would recommend the removal of magic numbers from this contract.

Examples include:

- Line 69 (`uint256 i; i < 500; ++i`).
	- Consider making 500 a constant variable denoting the upper limit of weeks to forward, as `uint16 MAX_FORWARDING_WEEKS = 500` 