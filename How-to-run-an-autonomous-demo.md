**TO BE ADJUSTED WITH THE LATEST PROCEDURE**

* Power up. Note that it's best to power up the trimble and applanix outside and keep the car perfectly still while they initialize.
* launch junior2.launch, etc.
* switch all the control switches (steering, throttle, etc.) to the autonomous position
* on oldpro or ezekiel
  * rosrun passat can
  * rosrun passat passat_fsm
  * rosrun estop fake_estop -i
  * run the actuator controller, the trajectory controller and the planner
* driver: init the DBW for autonomous driving
  * if starting from a stop position:
    * turn on the parking brake and shift to neutral
    * release the red emergency button
    * wait until the beeping stops
  * while driving:
    * simply release the red emergency button
    * if you press the brake while it's beeping, it resets the initialization procedure
* use the fake e-stop window to run / pause / resume (keys 'p' and 'r') the driving