As with the fuerte version, it is necessary to run a roscore first, and load some parameters. This is best achieved by:

```
roslaunch stdr_common junior2.launch
```

Next, playback some data; for example:

```
rosrun log_and_playback raw_playback run3-*.*
```

Also, for most of the packages, they will need some static transforms:

```
roslaunch stdr_common junior2-static_transforms.launch
```

Ignore the errors related to camera5, it's just that the transform is not defined (yet) for the top camera, and it does not matter as we don't use it.

Now, to visualize the data, run rviz:

```
rosrun rviz rviz
```