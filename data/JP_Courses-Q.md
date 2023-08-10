0. Lack of input validation, no address(0) checks:

https://github.com/code-423n4/2023-08-verwa/blob/6686cf4fef5ec15582ccd9a62232e8c1253e8fc0/src/GaugeController.sol#L58-L59
https://github.com/code-423n4/2023-08-verwa/blob/6686cf4fef5ec15582ccd9a62232e8c1253e8fc0/src/GaugeController.sol#L118-L122

Risk:
- voting funds/tokens can be lost.
- onlyGovernance modifier will be bricked
- able to add a new gauge with address(0)

Recommendation:

    constructor(address _votingEscrow, address _governance) {
    		require(_votingEscrow != address(0));
    		require(_governance != address(0));
        votingEscrow = VotingEscrow(_votingEscrow);     
        governance = _governance; // TODO: Maybe change to Oracle   
        uint256 last_epoch = (block.timestamp / WEEK) * WEEK;  
        time_sum = last_epoch;
    }

    function add_gauge(address _gauge) external onlyGovernance {
    		require(_gauge != address(0));
        require(!isValidGauge[_gauge], "Gauge already exists");
        isValidGauge[_gauge] = true;
        emit NewGauge(_gauge);
    }


1. Lack of input validation, no zero value or address(0) checks:

https://github.com/code-423n4/2023-08-verwa/blob/6686cf4fef5ec15582ccd9a62232e8c1253e8fc0/src/GaugeController.sol#L188-L199
https://github.com/code-423n4/2023-08-verwa/blob/6686cf4fef5ec15582ccd9a62232e8c1253e8fc0/src/GaugeController.sol#L204-L206

Risks:
- if _gauge == address(0) then this state variable/mapping's value will be incorrectly updated: points_sum[next_time].bias 
- and new_sum will be assigned an incorrect value.
- if _weight == 0 then gauge weight can be changed to zero; points_sum[next_time].bias will be incorrectly updated

Recommendation:

		function _change_gauge_weight(address _gauge, uint256 _weight) internal {
				require(_gauge != address(0));
				require(_weight != 0);

OR

    function change_gauge_weight(address _gauge, uint256 _weight) public onlyGovernance {
    		require(_gauge != address(0));
				require(_weight != 0);
        _change_gauge_weight(_gauge, _weight);
    }


2. No explicit user access control implemented for function vote_for_gauge_weights():

https://github.com/code-423n4/2023-08-verwa/blob/6686cf4fef5ec15582ccd9a62232e8c1253e8fc0/src/GaugeController.sol#L211


3. The usage of timestamps and the calculation of time intervals might introduce vulnerabilities due to miners' potential manipulation of timestamps. Be cautious and consider using block numbers or other mechanisms for timing.


4. The change_gauge_weight() function should use the `external` visibility modifier instead of `public`, as it directly calls the internal version, and doesn't seem to be called internally itself.

https://github.com/code-423n4/2023-08-verwa/blob/6686cf4fef5ec15582ccd9a62232e8c1253e8fc0/src/GaugeController.sol#L201-L206

Even though gas optimization is not in scope for this contest, using the `external` modifier instead is recommended as best practice.


5. Uninitialized public state variables generally not recommended, as it could expose contract to potential vulnerabilities/attack vectors, i.e. increase potential attack surface.

https://github.com/code-423n4/2023-08-verwa/blob/6686cf4fef5ec15582ccd9a62232e8c1253e8fc0/src/VotingEscrow.sol#L26-L27
https://github.com/code-423n4/2023-08-verwa/blob/6686cf4fef5ec15582ccd9a62232e8c1253e8fc0/src/VotingEscrow.sol#L36


6. Storage optimization possible here. Even though it helps to optimize gas usage, which is out of scope, it's still better to optimize storage setup.

https://github.com/code-423n4/2023-08-verwa/blob/6686cf4fef5ec15582ccd9a62232e8c1253e8fc0/src/VotingEscrow.sol#L50-L55

Recommendation:

Instead of doing:

    struct LockedBalance {
        int128 amount;
        uint256 end;
        int128 delegated;
        address delegatee;
    }
    
Do this:

    struct LockedBalance {
    		uint256 end;
        int128 amount;
        int128 delegated;
        address delegatee;
    }
    

7. Any reason why these two `if` statements weren't combined into one `if` statement block? I could not see any legit reason other than the intention to keep them separate for readability/"modularity" ?

https://github.com/code-423n4/2023-08-verwa/blob/6686cf4fef5ec15582ccd9a62232e8c1253e8fc0/src/VotingEscrow.sol#L223-L258

        if (_addr != address(0)) {
            // If last point was in this block, the slope change has been applied already
            // But in such case we have 0 slope(s)
            lastPoint.slope = lastPoint.slope + userNewPoint.slope - userOldPoint.slope;
            lastPoint.bias = lastPoint.bias + userNewPoint.bias - userOldPoint.bias;
            if (lastPoint.slope < 0) {
                lastPoint.slope = 0;
            }
            if (lastPoint.bias < 0) {
                lastPoint.bias = 0;
            }
        }

        // Record the changed point into history
        pointHistory[epoch] = lastPoint;

        if (_addr != address(0)) {
            // Schedule the slope changes (slope is going down)
            // We subtract new_user_slope from [new_locked.end]
            // and add old_user_slope to [old_locked.end]
            if (_oldLocked.end > block.timestamp) {
                // oldSlopeDelta was <something> - userOldPoint.slope, so we cancel that
                oldSlopeDelta = oldSlopeDelta + userOldPoint.slope;
                if (_newLocked.end == _oldLocked.end) {
                    oldSlopeDelta = oldSlopeDelta - userNewPoint.slope; // It was a new deposit, not extension
                }
                slopeChanges[_oldLocked.end] = oldSlopeDelta;
            }
            if (_newLocked.end > block.timestamp) {
                if (_newLocked.end > _oldLocked.end) {
                    newSlopeDelta = newSlopeDelta - userNewPoint.slope; // old slope disappeared at this point
                    slopeChanges[_newLocked.end] = newSlopeDelta;
                }
                // else: we recorded it already in oldSlopeDelta
            }
        }
