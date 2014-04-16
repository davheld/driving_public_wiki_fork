Usage:
```rndf_edit rndf_filename [imagery_path]```
e.g. ```rndf_edit VAIL_large_loop_rndf.txt ~/imagery```

If no imagery path is given, imagery is read from ```$VLR_ROOT/data/imagery```. The imagery root directory is defined in ```stdr_common/launch/junior2.launch```, however it appears that ```rndf_edit``` is currently not reading these parameters. It is recommended to define ```$VLR_ROOT``` in ```.bashrc```.

The GUI for ```rndf_edit``` is context based through the buttons at the top of the window. Contexts are "Waypoint", "Lane", "Segment", "Exit", "Perimeter Point", etc. Within each context you can perform add, move, select, etc. functions with the following keyboard / mouse events (note that all keyboard / mouse inputs use the <Ctrl> key):

* `<Ctrl> a`: append
* `<Ctrl> d`: delete
* `<Ctrl> s`: toggle Stop Point
* `<Ctrl> c`: toggle Check Point
* `<Ctrl> left-click`: selects and moves the item
* `<Ctrl> right-click`: selects and rotates the item, if sensible in that context
* `<Ctrl> <Shift> I`: toggles between imagery types (Google should be displayed) 

"Stop" point is for a Stop Sign.

For "Crosswalk", add the waypoint linked to that crosswalk in the window 'Linked Way Points' below, for example '1.1.2'.

For "Exit" context, select 'from' waypoint first, then select "Exit" context, click twice (slowly) on 'from' waypoint, drag to 'to' waypoint and click once. Delete exit does not seem to work currently, so you need to delete the associated waypoint or delete it directly in the RNDF file.