GaugeWeightChanged
 For heightened transparency and visibility regarding gauge weight modifications, there should be an event emit for Gauge changed weight.
https://github.com/code-423n4/2023-08-verwa/blob/9a2e7be003bc1a77b3b87db31f3d5a1bcb48ed32/src/GaugeController.sol#L204-L206

Omitted event emit
An unlocked() event emit was never implemented anywhere in the contract.
https://github.com/code-423n4/2023-08-verwa/blob/9a2e7be003bc1a77b3b87db31f3d5a1bcb48ed32/src/VotingEscrow.sol#L23

Delegate event emit
An event emit should be created for every delegate made.
https://github.com/code-423n4/2023-08-verwa/blob/9a2e7be003bc1a77b3b87db31f3d5a1bcb48ed32/src/VotingEscrow.sol#L356