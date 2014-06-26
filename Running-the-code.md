It is necessary to run a (one and only one) roscore first, and load some parameters. This is best achieved by:

```
roslaunch stdr_common junior2.launch
```

## playing back data

Next, playback some data; for example:

```
rosrun log_and_playback raw_playback run3-*.*
```

## Static transforms

Also, for most of the packages, they will need some static transforms:

```
roslaunch stdr_common junior2-static_transforms.launch
```

Ignore the errors related to camera5, it's just that the transform is not defined (yet) for the top camera, and it does not matter as we don't use it.


## Visualization with rviz

Now, to visualize the data, run `rviz`:

```
rosrun rviz rviz
```

Within `rviz`, on the left, under `Global Options`, set `Fixed Frame` to `smooth` to view the world in smooth coordinates.

On the right, under `Current View`, change `Target Frame` to `base_link`.  Click `Zero` to center the screen on the car.

Under `Grid` on the left, change `Reference Frame` to `base_link`, change `Plane Cell Count` to 1000 and `Cell Size` to 10.

Next, click `Add`, click the tab `by topic`, and scroll down to: `/velodyne-> /points -> PointCloud2`

For the points, change:
* Selectable: No
* style: Points
* Decay Time: 0.15
* Color Transform: Intensity
* Use rainbow: No

Next, click "Add" and select `Passat` to view the vehicle.