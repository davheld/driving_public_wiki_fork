QtCreator is a free and popular IDE (Integrated Development Environment). Originally designed to create Qt applications, it is also very useful to edit C++ code in general.

The main benefits of working with an IDE are:
* code completion
* code indexing
* integration with the compiler and debugger

Eclipse is another popular IDE but we found that QtCreator was working better.

## Installing QtCreator

```sudo apt-get install qtcreator```

In order to be able to work with ROS code, QtCreator needs to be able to source the environment (your `.bashrc`). You can either launch it from a terminal, or create a custom `qtcreator.desktop` file:

First copy the standard `qtcreator.desktop` file:
```cp /usr/share/applications/qtcreator.desktop ~```

Then edit it and change the `Exec` line into:
```Exec=bash -i -c qtcreator %F```

Finally, remove the QtCreator icon from the launcher (if present) by right clicking it and selecting "Unlock from launcher"; drag and drop the `qtcreator.desktop` file you just created to the launcher.


## Configuring QtCreator

QtCreator offers lots of options. The most important one is to configure the code style to indent using 2 spaces.
1. Open "Tools > Options ..."
2. Under "C++ > Code Style", copy the GNU style (name the copy driving for instance), and edit it
3. Set the tab and indent size to 2 (spaces only)


## Opening a ROS project

1. Click "File > Open file or projet" and browse to the `CMakeLists.txt` at the root of your catkin source space (`~/catkin_ws/src/CMakeListst.txt`)
2. set the build location to the build space of the catking workspace (`~/catkin_ws/build`).
3. run cmake with no arguments


It should start parsing the packages in the project (as with `catkin_make`) and the list of packages should appear in the project view. Use `Ctrl+B` to build.



source: [[http://wiki.ros.org/IDEs#QtCreator]]