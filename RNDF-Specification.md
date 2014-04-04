* This specification covers only elements of the RNDF spec that differ from the original [[DARPA RNDF version 1.0|http://www.darpa.mil/grandchallenge/docs/RNDF_MDF_Formats_031407.pdf]].
* Current RNDF version: 1.1
* The RNDF library is still able to read old V1.0 RNDF files.
* The current version of the RNDF parser / writer only supports a single intersection for all traffic lights.


# General

The RNDF is a tab-delimited ASCII file. Within the RNDF, strings have a maximum length of 128 characters, and do not contain any spaces, backslashes, or *. Integers are always positive and have a maximum value of 32,768. The RNDF has the following form:

> **RNDF_name** _filename (string)_  
> **num_segments** _number_of_segments (integer\>0)_  
> **num_zones** _number_of_zones (integer≥ 0)_  
> **num_intersections** _number_of_intersections (integer≥ 0)_  
> _\<optional file header\>_  
> _\<segment 1\>_  
> .  
> .  
> _\<segment M\>_  
> _\<zone M+1\>_  
> .  
> .  
> _\<zone M+N\>_  
> _\<intersection M+N+1\>_  
> _\<intersection M+N+O\>_  
> **end_file**

The _\<optional file header\>_ may include the following:

> **format_version**  _format_version (string)_  
> **creation_date**  _creation_date (string)_

These optional file header fields are provided for convenience, and the format is unspecified. Blank lines may be added to the file for formatting purposes. C-style comments are delimited by “/*” and “*/” and are always placed at the end of the line.


# Segments


Each _\<segment\>_ has the following format:

> **segment**  _segment_id (integer\>0)_  
> **num_lanes**  _number_of_lanes (integer\>0)_  
> **num_crosswalks**  _number_of_crosswalks (integer≥0)_  
> _\<optional segment header\>_  
> _\<lane 1\>_  
> .  
> .  
> _\<lane N\>_  
> _\<crosswalk 1\>_  
> .  
> .  
> _\<crosswalk M\>_  
> **end_segment**

The _\<optional segment header\>_ may contain the following elements:

> **segment_name** _segment_name (string)_  
> **speed_limit** _speed_limit_in_mph (fixed)_

The segment_name attribute of the segment is used for the street name, such as “Campus_Drive”.


# Lanes


Each _\<lane\>_ has the following format:

> **lane**  _lane_id (x.y; x,y∈ integer\>0)_  
> **num_waypoints**  _number_of_waypoints (integer\>0)_  
> _\<optional lane header\>_  
> _\<waypoint 1\>_  
> .  
> .  
> _\<waypoint P\>_  
> **end_lane**

The _lane_id_ is constructed from the number of the segment in which it is contained. For example, if segment 17 has two lanes, they would be named “17.1” and “17.2”. The general naming convention numbers adjacent lanes consecutively from west to east or north to south. If segment 17 in the example moves in an east-west direction, the north lane would be named “17.1” and the south lane “17.2.”

The _\<optional lane header\>_ contains some or all of the following elements:

> **lane_width**  _lane_width (integer≥ 0)_  
> **lane_type**  _lane_type (string ∈ {car_lane, bike_lane})_  
> **left_boundary**  _left_boundary (string ∈ {double_yellow, solid_yellow, solid_white, broken_white})_  
> **right_boundary**  _right_boundary (string ∈ {double_yellow, solid_yellow, solid_white, broken_white})_  
> **checkpoint**  _waypoint_id (x.y.z; x,y,z ∈ integer\>0) checkpoint_id (integer\>0)_  
> **stop**  _waypoint_id (x.y.z; x,y,z∈ integer\>0)_  
> **exit** _exit_waypoint (waypoint_id)_  _entry_waypoint (waypoint_id)_  
> **exit** _exit_waypoint (waypoint_id)_  _entry_perimeterpoint (perimeterpoint_id)_  
> **cross**  _crosswalk_waypoint (waypoint_id)  crosswalk_id (x.y; x,y ∈ integer\>0)  link_type (string ∈ {stop, incoming})_  
> **light**  _trafficlight_waypoint (waypoint_id)  trafficlight_id (x.y; x,y ∈ integer\>0)


* The _lane_width_ provides the width of the lane in feet.
* The keyword **checkpoint** indicates that the waypoint is a named checkpoint.
* The keyword **stop** indicates a waypoint associated with a stop sign.
* The keyword **exit** is followed by the name of the _exit_waypoint_ and related _entry_waypoint_ or _entry_perimeterpoint_ associated with the lane.
* The keyword **cross** indicates a waypoint associated with a crosswalk. It is followed by the name of the _crosswalk_waypoint_, the name of the associated crosswalk and the type of the association. The type _stop_ indicates that the vehicle has to stop _exactly at_ this waypoint and wait for the crosswalk to become clear, while the type _incoming_ indicates that the vehicle has to yield for pedestrians _before_ arriving at this waypoint, e.g. at intersections.
* The keyword **light** indicates a waypoint associated with a traffic light. It is followed by the name of the _crosswalk_waypoint_ and the name of the associated traffic light. The vehicle has to stop at this waypoint until the associated traffic light is green. Note that one waypoint may be associated to more than one traffic light.

A **lane** may have multiple checkpoints, stop signs, entry waypoints, entry perimeterpoints, exit waypoints, crosswalks or traffic lights.

# Crosswalks

Each _\<crosswalk\>_ has the following format:

> **crosswalk**  _crosswalk_id (x.y; x,y∈ integer\>0)_  
> **crosswalk_width**  _crosswalk_width_in_feet (integer≥ 0)_  
> **crosswalk_p1**  _(x.y.z; x,y,z∈ integer\>0)     latitude (fixed)    longitude (fixed)_  
> **crosswalk_p2**  _(x.y.z; x,y,z∈ integer\>0)     latitude (fixed)    longitude (fixed)_  
> **end_crosswalk**

The _crosswalk_id_ is constructed from the number of the segment in which it is contained. For example, if segment 17 has two crosswalks, they would be named “17.1” and “17.2”. A crosswalk always consists of two coordinates, **crosswalk_p1** and **crosswalk_p2**. The _latitude_ and _longitude_ fields are fixed points with six decimal places and express the waypoint locations in decimal-degrees, using the ITRF00 reference frame and the GRS80 ellipsoid. Points in the northern and western hemispheres have positive latitude and negative longitude, respectively.


# Waypoints

A _\<waypoint\>_ has the following format:

> _waypoint_id_  _(x.y.z; x,y,z∈ integer\>0)     latitude (fixed)    longitude (fixed)_

The _waypoint_id_ is constructed from the lane_id. The first waypoint of lane 17.1 is thus named “17.1.1”. The _latitude_ and _longitude_ fields are fixed points with six decimal places and express the waypoint locations in decimal-degrees, using the ITRF00 reference frame and the GRS80 ellipsoid. Points in the northern and western hemispheres have positive latitude and negative longitude, respectively.

# Intersections

An _\<intersection\>_ has the following format:

> **intersection**  _intersection_id (integer\>0)_  
> **num_trafficlights** _number_of_trafficlights (integer\>0)_  
> _\<trafficlight 1\>_  
> .  
> .  
> _\<trafficlight N\>_  
> **end_intersection**

The **intersection** is merely a container for a group of traffic lights, e.g. on one particular intersection.


# Traffic Lights

A _\<trafficlight\>_ has the following format:

> **trafficlight**  _trafficlight_id (x.y; x,y∈ integer\>0)_  
> _\<optional_trafficlight_header\>_  
> **position**  _latitude (fixed)    longitude (fixed)    z (fixed)_  
> **end_trafficlight**

The _trafficlight_id_ is constructed from the number of the intersection in which it is contained. For example, if intersection 18 has two intersections, they would be named “18.1” and “18.2”. A traffic light is defined by its 3D _latitude_, _longitude_ and _height_. The orientation of a traffic light can be obtained from the coordinate of its associated waypoints.

The _\<optional_trafficlight_header\>_ can contain the following element:

> **group_id**  _group_id (integer ∈ 0..255)_

The **group_id** is only used in the simulator and is a bitmask that indicates at which state the light will be green. The simulator switches between 8 _simulator_states_ 0b00000001 -\> 0b00000010 -\> ... -\> 0b10000000 at constant time intervals. A light is green in a given state if _simulator_state & group_id != 0_. For example, for _group_id_ = 5 (0b00000101), the light will be green in states 1 and 3.
