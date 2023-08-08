The _get_sum() and _get_weight() functions in the GaugeController.sol contract contain loops that can run indefinitely if conditions inside don't change as expected. This can cause gas to be consumed indefinitely until the transaction runs out of gas, leading to transaction failures.

Impact:
Transactions that interact with these functions can fail, leading to wasted gas fees for users. If these functions are critical for other operations, it can halt the functionality of the entire platform, leading to potential fund lockups and loss of trust.

POC

 function _get_sum() internal view returns (uint256 sum) {
    while(someCondition) {
        // Some logic here
        if (anotherCondition) {
            break; // This might never be reached if 'anotherCondition' is never true
        }
    }
}
