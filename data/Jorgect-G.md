# GAS OPTIMIZATION

## [G-01] LACK OF UNCHECKED WHEN THE VARIABLE IT´S IMPOSIBLE TO OVEFLOW

Use unchecked when some opertaion is already checked, the contract itself its ensuring that the variable never gonna change, can save gas(three instans):

```
file:/src/GaugeController.sol
 function _get_sum() internal returns (uint256) {
            ...
            if (pt.bias > d_bias) {
                pt.bias -= d_bias; // add unchecked the if statement is already validating that this is no gonna overflow or underflow
                uint256 d_slope = changes_sum[t];
                pt.slope -= d_slope;
            } 
            ...

    }
```
https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L73C11-L74C35

```
file:/src/GaugeController.sol
function _get_weight(address _gauge_addr) private returns (uint256) {
        ...
                if (pt.bias > d_bias) {
                    pt.bias -= d_bias;// add unchecked the if statement is already validating that this is no gonna overflow or underflow
                    uint256 d_slope = changes_weight[_gauge_addr][t];
                    pt.slope -= d_slope;
                } 
        ...
    }
```
https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L99C16-L100C39

```
file:/src/GaugeController.sol
 function vote_for_gauge_weights(address _gauge_addr, uint256 _user_weight) external {
       ...
        if (old_slope.end > next_time) old_dt = old_slope.end - next_time; // add unchecked the if statement is already validating that this is no gonna overflow or underflow
        ...
}
```
https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L226


## [G-02] PASSING THE ARGUMENT DIRECTLY INSTED OF CREATE A MEMORY VARIABLE

Some time is more worth it pass a state variable direclty if is just gonna be used one time insted of keep it in a memory variable.

```
file:/src/GaugeController.sol
 function _change_gauge_weight(address _gauge, uint256 _weight) internal {
        ...
        uint256 new_sum = old_sum + _weight - old_gauge_weight;
        points_sum[next_time].bias = new_sum;
        ...
    }
```
https://github.com/code-423n4/2023-08-verwa/blob/a693b4db05b9e202816346a6f9cada94f28a2698/src/GaugeController.sol#L196

this is using a mstore opcode innecesary.