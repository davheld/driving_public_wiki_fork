We have several TB of data recorded on Junior over the year. Here is some instruction on how to get the data logs, how to record new data, and how to play it back.

## Getting the data logs

All the data is located on the AFS server. The AFS file system is accessible via various means, the most common of which is to SSH through `robo` first. Once you are connected to `robo`, the data is located in `/afs/cs/group/driving/data/logs/`. So copying data to and from your local hard drive will involve `scp` or even better `rsync`.

## Recording data

As we are using ROS, rosbags is used to store the data. However, we prefer to not record and store all the data. Instead we prefer to keep the raw data from the sensors, and the data that cannot be recreated easily. For instance, we do not want to keep the _projected_ velodyne data, as it can be easily recreated from the raw velodyne data, the applanix data and the configuration files. So use the `log_run` script in package `log_and_playback`:

`rosrun log_and_playback log_run /path/to/where/to/save/the/data/`

The script will record all the data (minus the projected velodyne data, the colored ladybug images, the colored point cloud, and all other data that can be recreated), in separate files: one for the applanix data, one for the velodyne data, one for the ladybug data, and one for all the other data. Separating the data in several files facilitates manipulating the data: the ladybug data is pretty big, so you will not want to download it from the server if you are not going to use it.

The `log_run` script is killed with Ctrl-C and it will ask you for the name of the data. This name will be prefixed with a timestamp for you.

On Junior, the data is usually stored on `/data/StorageX` on the nuthouse computer.

## Playing back the data

The data format has evolved over the time. In the old days of the IPC code (pre-ROS), the applanix data was stored in a file with extension .log.gz, the velodyne file in a .vlf file, the ladybug file in .llf file and all other data in a .blf file. Then we started recording as bag files. But during the 2012-2013 period, messages where of a different format. As of 2014, we are using version 2 of the code.

There is code in package `log_and_playback` to playback all of these different formats and somewhat convert between them.

### General mechanisms

We can either playback continuously the data, which simulates the data coming from the sensors. This is done using the `raw_playback` script. Or we can read the data offline, although random access in not yet possible.

### Caveat

The sensor data is read from the log files, and then passed to the processing code to produced higher level data such as the projected velodyne point cloud. This requires information such as the position of the velodyne on the car. This is information is stored in a file in the repository. The problem is that this position has changed multiple times over time but was not recorded along with the data. So when playing back old data, you will have to find that information first...

### Converting between formats

The executable `bag2dgc` can convert data recorded in bag files to the DGC format (.log.gz, .vlf, .llf), which is useful to process new data with old code.

The version 1 of the bag data can be converted to the version 2 as follow:

`rosbag fix input_file.bag output_file.bag`

This is fairly fast. The main difference between v2 and v1 is that some of the message names (including the package names) have changed and some useless data was removed, some headers were added, etc.

When playing back v1 messages for v2 code, it is necessary to run `conversion` in package `velodyne` which converts from the old raw velodyne data message (`stdr_msgs/RawScans`) to the new velodyne data format (`velodyne_msgs/VelodyneScan`). This is handled by the `raw_playback` script mentioned earlier.

### raw_playback

The `raw_playback` script can be used more or less as `rosbag play`. In fact, when playing back bag files, it runs `rosbag play` with the given options. With the DGC data files, it accepts only the -r option to control the playback rate.

What the script does is detect the data format and runs the appropriate playback back end (`rosbag play` or `dgclog_playback`). It will also set `use_sim_time` to true and create a clock server. Finally it will launch the `raw_playback` launch files that are needed to recreate the data.