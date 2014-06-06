# Intersections

All relevant states are substates of StIntersection.

## StIntersectionApproach

The first substate is `StIntersectionApproach`. It queries the `IntersectionManager` to learn what intersection we are dealing with. Then it queries the `Topology` to get the distance to the intersection, the distance to the stop sign (if any) and traffic light.

The `IntersectionManager` also informs whether we have to stop at this intersection. The associated logic takes into account whether there is a stop sign, whether there is a crosswalk (checking for pedestrians), etc.

Based on all of that, there is plenty of logic (...) to find out whether we need to stop and where, turn on the blinkers, etc. It also takes into account a possible queue of vehicles.

Finally it calls the trajectory generator `ChsmPlanner::generateCurvePoints()` with as arguments: the distance to the stop point, the distance to the vehicle we are following, and the max speed allowed.

## Trajectory generation

The trajectory generator determines it's lookahead distance, which is the largest of the `center_line_min_lookahead_dist` parameter (20 meters), and the horizon (`checked_horizon` parameter, 5 seconds) times the current velocity.