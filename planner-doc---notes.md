# Code structure

The main class for the planner is `AWRoadPlanner` in `stdr_planners/aw_road_planner.h`. It complies with the `AbstractPlanner` interface, and is mostly a ROS wrapper around the `ChsmPlanner` class from package `aw_chsm_planning`.

Btw, `aw` stands for Annie Way, which was the name of a team in one of the Darpa challenges. The planner was formerly called `paw2` which stands for Planner Annie Way. `Chsm` stands for Concurrent Hierarchical (Finite) State Machine. At the heart of the planner is a state machine, which is implemented using the boost statechart framework. The other main component of the planner is the trajectory generator, that derives from Moritz Werling's work.

`AWRoadPlanner`'s main loop does one iteration of the state machine, which generates a trajectory, then publishes that trajectory. The planner's internal representation of the trajectory is stored in variable `trajectory_points_`.

# Intersection states

All relevant states are substates of StIntersection.

## StIntersectionApproach

The first substate is `StIntersectionApproach`. It queries the `IntersectionManager` to learn what intersection we are dealing with. Then it queries the `Topology` to get the distance to the intersection, the distance to the stop sign (if any) and traffic light.

The `IntersectionManager` also informs whether we have to stop at this intersection. The associated logic takes into account whether there is a stop sign, whether there is a crosswalk (checking for pedestrians), etc.

Based on all of that, there is plenty of logic (...) to find out whether we need to stop and where, turn on the blinkers, etc. It also takes into account a possible queue of vehicles.

Finally it calls the trajectory generator `ChsmPlanner::generateCurvePoints()` with as arguments: the distance to the stop point, the distance to the vehicle we are following, and the max speed allowed.

## Trajectory generation

The trajectory generator determines it's lookahead distance, which is the largest of the `center_line_min_lookahead_dist` parameter (20 meters), and the horizon (`checked_horizon` parameter, 5 seconds) times the current velocity.