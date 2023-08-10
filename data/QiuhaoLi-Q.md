## [low] LOCKTIME should be 1826 days considering the leap year
Since there is a leap year every four years, the five years lock time should be 1826 days. In some cases, this can lead to different _floorToWeek() calculated locking time.

## [non-critical] should log vote event in vote_for_gauge_weights()
src/GaugeController.sol is ported from Curve's [`GaugeController.vy`](https://github.com/curvefi/curve-dao-contracts/blob/master/contracts/GaugeController.vy), which log the vote event in the vote_for_gauge_weights() but we don't don't do it.