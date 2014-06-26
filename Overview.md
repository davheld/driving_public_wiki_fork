The overall system can be broken down in 5 main modules: localization, perception, navigation, control and drive-by-wire. Those modules are themselves made of several parts that run as separate processes. To orchestrate those processes and how they communicate we use [[ROS|http://www.ros.org]].

## Sensors

The main sensors on the car are:
 * **Applanix** which combines the IMU, GPS and encoder, to provide the best estimate of the car's position. As the IMU is of very good quality, we get a very precise displacement information, that we use to compute the **smooth** coordinates (more on that later). As it is connected to the **Trimble** GPS receiver with Omnistar capability, the error on the global position is usually smaller than a meter.
 * **Velodyne**, a 64 beams 360 degrees laser scanner.
 * **Ladybug**, a 360 degrees field of view camera.

There are more sensors, but we are currently not using them.

## Localization

The localization module uses the information from the Applanix to estimate the location and pose of the vehicle.

### Global coordinates

The applanix sensor gives a position estimate in global coordinates (latitude, longitude, altitude, etc.). This estimates is computed from the GPS position combined with IMU readings. It's quite accurate and the error is bounded by the GPS error. However, in case the GPS is not available for a while, the position could jump significantly when the GPS becomes available again.

### Smooth coordinates

When the applanix module is first started, it takes the initial location as origin. From there, is computes the smooth coordinates by integrating the measured velocity given by the applanix sensor. The key properties of the smooth frame are:

* it's drifting with time, and the error is unbounded. It means that after some time, the position of the origin of the smooth frame, plus the current position in the smooth frame, is no longer equal to the global coordinates.
* there is no sudden jump in pose from one measurement to the next, which makes it suitable for frame to frame analysis (point matching, etc.). 

### UTM coordinates and offset

The `localize` module provides the offset between the UTM coordinates of the car and the smooth coordinates, such that UTM = Offset + Smooth. `fake_localizer` computes this offset by simply substracting the smooth coordinates from the UTM coordinates obtained from the latitude and longitude returned by the applanix. 

## TF frames

`tf` is the ROS package that maintains the transforms between all the different coordinates frames on a robot. See http://wiki.ros.org/tf

On Junior we have the following frames:

 * `base_link` is the vehicle frame (base_link is the standard ROS name for that frame). It's origin is located on the roof top of the car at the back (somewhat above the IMU). It's x axis is pointing forward, y to the left and z up.
 * below `base_link` are the sensors frames: `velodyne`, `ladybug`, etc. As the sensors don't move, the transform is a static one.
 * the `smooth` frame's origin is where the applanix module was started. It's aligned with the UTM grid (x to the east, y towards north). The transform between `base_link` and `smooth` is the smooth coordinates of the car.
 * the `utm` frame origin's is at the origin of the UTM tile 10S
 * the `local_utm` frame is initialized where the localizer is started. The transform between the `utm` frame and the `local_utm` frame is fixed and is typically a large number because we are far from the origin of the `utm` frame. The transform between the `local_utm` and `smooth` frame is adjusted by the localizer (it's initially 0) to correct for odometric drift.
