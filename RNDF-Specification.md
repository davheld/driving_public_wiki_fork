 * This specification covers only elements of the RNDF spec that differ from the original [[DARPA RNDF version 1.0|http://www.darpa.mil/grandchallenge/docs/RNDF_MDF_Formats_031407.pdf]].
 * Current RNDF version: 1.1
 * The RNDF library is still able to read old V1.0 RNDF files.
 * The current version of the RNDF parser / writer only supports a single intersection for all traffic lights. 

***

# General

The RNDF is a tab-delimited ASCII file. Within the RNDF, strings have a maximum length of 128 characters, and do not contain any spaces, backslashes, or *. Integers are always positive and have a maximum value of 32,768. The RNDF has the following form:

'''RNDF_name''' ''filename (string)''[[BR]]
'''num_segments''' ''number_of_segments (integer>0)''[[BR]]
'''num_zones'''	''number_of_zones (integer≥ 0)''[[BR]]
'''num_intersections'''	''number_of_intersections (integer≥ 0)''[[BR]]
''<optional file header>''[[BR]]
''<segment 1>''[[BR]]
.[[BR]]
.[[BR]]
''<segment M>''[[BR]]
''<zone M+1>''[[BR]]
.[[BR]]
.[[BR]]
''<zone M+N>''[[BR]]
''<intersection M+N+1>''[[BR]]
''<intersection M+N+O>''[[BR]]
'''end_file'''[[BR]]

The ''<optional file header>'' may include the following:

'''format_version'''  ''format_version (string)''[[BR]]
'''creation_date'''  ''creation_date (string)''

These optional file header fields are provided for convenience, and the format is unspecified. Blank lines may be added to the file for formatting purposes. C-style comments are delimited by “/*” and “*/” and are always placed at the end of the line.


# Segments


Each ''<segment>'' has the following format:

'''segment'''  ''segment_id (integer>0)''[[BR]]
'''num_lanes'''  ''number_of_lanes (integer>0)''[[BR]]
'''num_crosswalks'''  ''number_of_crosswalks (integer≥0)''[[BR]]
''<optional segment header>''[[BR]]
''<lane 1>''[[BR]]
.[[BR]]
.[[BR]]
''<lane N>''[[BR]]
''<crosswalk 1>''[[BR]]
.[[BR]]
.[[BR]]
''<crosswalk M>''[[BR]]
'''end_segment'''

The ''<optional segment header>'' may contain the following elements:

'''segment_name''' ''segment_name (string)''[[BR]]
'''speed_limit''' ''speed_limit_in_mph (fixed)''

The segment_name attribute of the segment is used for the street name, such as “Campus_Drive”.


# Lanes


Each ''<lane>'' has the following format:

'''lane'''  ''lane_id (x.y; x,y∈ integer>0)''[[BR]]
'''num_waypoints'''  ''number_of_waypoints (integer>0)''[[BR]]
''<optional lane header>''[[BR]]
''<waypoint 1>''[[BR]]
.[[BR]]
.[[BR]]
''<waypoint P>''[[BR]]
'''end_lane'''

The ''lane_id'' is constructed from the number of the segment in which it is contained. For example, if segment 17 has two lanes, they would be named “17.1” and “17.2”. The general naming convention numbers adjacent lanes consecutively from west to east or north to south. If segment 17 in the example moves in an east-west direction, the north lane would be named “17.1” and the south lane “17.2.”

The ''<optional lane header>'' contains some or all of the following elements:

'''lane_width'''  ''lane_width (integer≥ 0)''[[BR]]
'''lane_type'''  ''lane_type (string ∈ {car_lane, bike_lane})''[[BR]]
'''left_boundary'''  ''left_boundary (string ∈ {double_yellow, solid_yellow, solid_white, broken_white})''[[BR]]
'''right_boundary'''  ''right_boundary (string ∈ {double_yellow, solid_yellow, solid_white, broken_white})''[[BR]]
'''checkpoint'''  ''waypoint_id (x.y.z; x,y,z ∈ integer>0) checkpoint_id (integer>0)''[[BR]]
'''stop'''  ''waypoint_id (x.y.z; x,y,z∈ integer>0)''[[BR]]
'''exit''' ''exit_waypoint (waypoint_id)''  ''entry_waypoint (waypoint_id)''[[BR]]
'''exit''' ''exit_waypoint (waypoint_id)''  ''entry_perimeterpoint (perimeterpoint_id)''[[BR]]
'''cross'''  ''crosswalk_waypoint (waypoint_id)  crosswalk_id (x.y; x,y ∈ integer>0)  link_type (string ∈ {stop, incoming})''[[BR]]
'''light'''  ''trafficlight_waypoint (waypoint_id)  trafficlight_id (x.y; x,y ∈ integer>0)
              

The ''lane_width'' provides the width of the lane in feet. [[BR]]
The keyword '''checkpoint''' indicates that the waypoint is a named checkpoint. [[BR]]
The keyword '''stop''' indicates a waypoint associated with a stop sign. [[BR]]
The keyword '''exit''' is followed by the name of the ''exit_waypoint'' and related ''entry_waypoint'' or ''entry_perimeterpoint'' associated with the lane. [[BR]]
The keyword '''cross''' indicates a waypoint associated with a crosswalk. It is followed by the name of the ''crosswalk_waypoint'', the name of the associated crosswalk and the type of the association. The type ''stop'' indicates that the vehicle has to stop ''exactly at'' this waypoint and wait for the crosswalk to become clear, while the type ''incoming'' indicates that the vehicle has to yield for pedestrians ''before'' arriving at this waypoint, e.g. at intersections.[[BR]]
The keyword '''light''' indicates a waypoint associated with a traffic light. It is followed by the name of the ''crosswalk_waypoint'' and the name of the associated traffic light. The vehicle has to stop at this waypoint until the associated traffic light is green. Note that one waypoint may be associated to more than one traffic light.

A '''lane''' may have multiple checkpoints, stop signs, entry waypoints, entry perimeterpoints, exit waypoints, crosswalks or traffic lights.

# Crosswalks

Each ''<crosswalk>'' has the following format:

'''crosswalk'''  ''crosswalk_id (x.y; x,y∈ integer>0)''[[BR]]
'''crosswalk_width'''  ''crosswalk_width_in_feet (integer≥ 0)''[[BR]]
'''crosswalk_p1'''  ''(x.y.z; x,y,z∈ integer>0)     latitude (fixed)    longitude (fixed)''[[BR]]
'''crosswalk_p2'''  ''(x.y.z; x,y,z∈ integer>0)     latitude (fixed)    longitude (fixed)''[[BR]]
'''end_crosswalk'''

The ''crosswalk_id'' is constructed from the number of the segment in which it is contained. For example, if segment 17 has two crosswalks, they would be named “17.1” and “17.2”. A crosswalk always consists of two coordinates, '''crosswalk_p1''' and '''crosswalk_p2'''. The ''latitude'' and ''longitude'' fields are fixed points with six decimal places and express the waypoint locations in decimal-degrees, using the ITRF00 reference frame and the GRS80 ellipsoid. Points in the northern and western hemispheres have positive latitude and negative longitude, respectively.

# Waypoints

A ''<waypoint>'' has the following format:

''waypoint_id''  ''(x.y.z; x,y,z∈ integer>0)     latitude (fixed)    longitude (fixed)''[[BR]]

The ''waypoint_id'' is constructed from the lane_id. The first waypoint of lane 17.1 is thus named “17.1.1”. The ''latitude'' and ''longitude'' fields are fixed points with six decimal places and express the waypoint locations in decimal-degrees, using the ITRF00 reference frame and the GRS80 ellipsoid. Points in the northern and western hemispheres have positive latitude and negative longitude, respectively.

# Intersections

An ''<intersection>'' has the following format:

'''intersection'''  ''intersection_id (integer>0)''[[BR]]
'''num_trafficlights''' ''number_of_trafficlights (integer>0)''[[BR]]
''<trafficlight 1>''[[BR]]
.[[BR]]
.[[BR]]
''<trafficlight N>''[[BR]]
'''end_intersection'''

The '''intersection''' is merely a container for a group of traffic lights, e.g. on one particular intersection.

# Traffic Lights

A ''<trafficlight>'' has the following format:

'''trafficlight'''  ''trafficlight_id (x.y; x,y∈ integer>0)''[[BR]]
''<optional_trafficlight_header>''[[BR]]
'''position'''  ''latitude (fixed)    longitude (fixed)    z (fixed)''[[BR]]
'''end_trafficlight'''

The ''trafficlight_id'' is constructed from the number of the intersection in which it is contained. For example, if intersection 18 has two intersections, they would be named “18.1” and “18.2”. A traffic light is defined by its 3D ''latitude'', ''longitude'' and ''height''. The orientation of a traffic light can be obtained from the coordinate of its associated waypoints.

The ''<optional_trafficlight_header>'' can contain the following element:

'''group_id'''  ''group_id (integer ∈ 0..255)''[[BR]]

The '''group_id''' is only used in the simulator and is a bitmask that indicates at which state the light will be green. The simulator switches between 8 ''simulator_states'' 0b00000001 -> 0b00000010 -> ... -> 0b10000000 at constant time intervals. A light is green in a given state if ''simulator_state & group_id != 0''. For example, for ''group_id'' = 5 (0b00000101), the light will be green in states 1 and 3.
