These instructions are currently being updated as the repository changes, if anything seems incorrect please contact Brice/Austin or if you know what you're doing correct it yourself.

# Operating Systems

The only tested system for running software is currently 64-bit Ubuntu 12.04. In general it should build on most *NIX distributions with the appropriate libraries and tools installed. It should even work in and Mac OSX. Note that you will need proper 3D graphics / hardware acceleration to be able to run rviz, the visualization software.

In particular, it will not work with Ubuntu 14.04 and older!

# Setup

## ROS

We currently use ROS Hydro. The code won't work with any other version of ROS.

```
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu precise main" > /etc/apt/sources.list.d/ros-latest.list'
wget http://packages.ros.org/ros.key -O - | sudo apt-key add -
sudo apt-get update
sudo apt-get install ros-hydro-desktop python-rosdep python-wstool
sudo rosdep init
```
Note: If you get the error:
```
ERROR: default sources list file already exists:
	/etc/ros/rosdep/sources.list.d/20-default.list
Please delete if you wish to re-initialize
```

Then run:
```
sudo rm -r /etc/ros
sudo rosdep init
```

## Gperftools

Gperftools is used as a profiler and for memory leak detection. Currently it needs to be installed separately.

```
wget http://gperftools.googlecode.com/files/gperftools-2.1.tar.gz
tar xzf gperftools-2.1.tar.gz
cd gperftools-2.1
./configure --enable-frame-pointers
make
sudo make install
```

## ccache (optional)

To speed up compilation it is recommended that you install ccache. From the  ccache website: "ccache is a compiler cache. It speeds up recompilation by caching previous compilations and detecting when the same compilation is being done again."

Run ```sudo apt-get install ccache```. Then add the following line to your ```.bashrc```:

```
export PATH=/usr/lib/ccache:$PATH
```

This folder contains drop in replacements for the gcc tools (gcc, g++, cc, etc.).

## Catkin

Catkin is the new build system for ROS that was introduced in groovy. Compared to the old build system (rosbuild):

* it needs a workspace with a special layout
* the manifest.xml has been replaced by a package.xml with a different structure
* the CMakeLists.txt has a different structure and uses less custom commands
* the whole workspace is considered as one large project, with each package in the workspace considered as a subdir by cmake
* build is done out of source
* you cannot build individual packages like what was possible with rosmake 

You can create your workspace anywhere you want and it can have any name. Here it will be ```~/catkin_ws```

```
source /opt/ros/hydro/setup.bash
mkdir -p ~/catkin_ws
cd ~/catkin_ws
wstool init src
catkin_make
source devel/setup.bash
```

Finally, add ```source ~/catkin_ws/devel/setup.bash``` in your ```.bashrc``` file so that your environment will be sourced each time you open a terminal.

# Getting and compiling the code

## Getting the code

Our code is now hosted on github as a set of repositories, some of which are private:
- [stdr_libraries](https://github.com/StanfordDrivingTeam/stdr_libraries): contains some library packages
- [tf](https://github.com/StanfordDrivingTeam/tf): a custom version of the TF package needed until we migrate to TF2
- [driving_public](https://github.com/StanfordDrivingTeam/driving_public): some public packages, that allow to work with our log files
- [driving](https://github.com/StanfordDrivingTeam/driving): a private repo that holds all our private code. To gain access, send you github user name (create an account if necessary) to Brice or Austin.

We use `wstool` to pull all those repos. Get the rosinstall (from the _driving_ or _driving_public_ repo) and copy it as `~/catkin_ws/src/.rosinstall`. Then from the src folder, do `wstool up`, which will clone / pull from the github repos.

## Installing the dependencies

Before building, you need to install the dependencies:

```
rosdep update
rosdep install --from-paths ~/catkin_ws/src -i -y
```

## Building

Finally you are ready to build:

```
cd ~/catkin_ws
catkin_make -j -l8
```

The -j -l8 option is optional. It is tells make to run several jobs in parallel for the build but limit the load to 8. Best to a number lower or equal to the number of cores on your machine.

Libraries and executables are placed in ```~/catkin_ws/devel/lib```

Additionally, some data needs to be downloaded from the CS server:

```
cd ~/catkin_ws/build
make download_extra_data
```

In the future, to rebuild the code, say after you have made some changes, you either use catkin_make from the workspace root folder, or use make from the build folder.



## Misc

On some systems (namely on nuthouse and oldpro), I had to fix an issue with the GL libraries:

cd /usr/lib/x86_64-linux-gnu
sudo ln -s /usr/lib/libGL.la
sudo ln -s /usr/lib/libGL.so
sudo ln -s /usr/lib/libGL.so.1
sudo ln -s /usr/lib/libGL.so.310.19

The environment variable ```VLR_ROOT``` needs to be defined (```export VLR_ROOT=~``` in your bashrc). It is used to locate the imagery data (```$VLR_ROOT/imagery```), but if you don't have the imagery data or don't need to work with it, you can set it to any location.
