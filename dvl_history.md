# Command History - DVL

## 20-05-2021

12:30 Entered Duckietown

### working on new pi

13:00 Ubuntu Login

14:00 connected to Wifi.

```bash

    sudo nano /etc/netplan/50-cloud-init.yaml
    sudo netplan generate
    sudo netplan apply

```

14:10 performed system upgrade

```bash

    sudo dpkg-reconfigure -plow unattended-upgrades
    sudo dpkg --configure -a
    sudo apt update && sudo apt -f install && sudo apt full-upgrade
    sudo dpkg-reconfigure -plow unattended-upgrades

```

14:30 DVL

```bash
    $ pip install crcmod pyserial
    $ git clone https://github.com/waterlinked/dvl-python.git
    $ cd /home/ubuntu/dvl-python # folder with the setup.py file
    $ pip install -e .
    $ python
        >>>  from wldvl import WlDVL
        >>>  dvl = WlDVL("/dev/ttyUSB0")
        >>>  dvl.read()
```

15:00 begin installing ROS noetic-desktop.

```bash
    sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
    sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
    sudo apt update
    sudo apt install ros-noetic-desktop
```

15:20 ROS environment setup

```bash
    source /opt/ros/noetic/setup.bash
    echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc
    source ~/.bashrc

```

15:30 Dependencies for building packages

```bash
    sudo apt install python3-rosdep python3-rosinstall python3-rosinstall-generator python3-wstool build-essential
    sudo apt install python3-rosdep
    sudo rosdep init
    rosdep update
```

15:40 successfully installed ROS noetic-desktop. After install:

```bash
    $ df-h
        Filesystem      Size  Used Avail Use% Mounted on
        udev            406M     0  406M   0% /dev
        tmpfs            91M  4.0M   87M   5% /run
        /dev/mmcblk0p2  117G  5.2G  108G   5% /
        tmpfs           455M     0  455M   0% /dev/shm
        tmpfs           5.0M     0  5.0M   0% /run/lock
        tmpfs           455M     0  455M   0% /sys/fs/cgroup
        /dev/loop0       49M   49M     0 100% /snap/core18/2002
        /dev/loop1       49M   49M     0 100% /snap/core18/2068
        /dev/loop2       62M   62M     0 100% /snap/lxd/19040
        /dev/loop3       29M   29M     0 100% /snap/snapd/11584
        /dev/loop4       28M   28M     0 100% /snap/snapd/11843
        /dev/loop5       60M   60M     0 100% /snap/lxd/20330
        /dev/mmcblk0p1  253M  120M  133M  48% /boot/firmware
        tmpfs            91M     0   91M   0% /run/user/1000
    $ printenv | grep ROS
        ROS_VERSION=1
        ROS_PYTHON_VERSION=3
        ROS_PACKAGE_PATH=/opt/ros/noetic/share
        ROSLISP_PACKAGE_DIRECTORIES=
        ROS_ETC_DIR=/opt/ros/noetic/etc/ros
        ROS_MASTER_URI=<http://localhost:11311>
        ROS_ROOT=/opt/ros/noetic/share/ros
        ROS_DISTRO=noetic
```

15:50 create ROS workspace

```bash
    $ mkdir -p ~/catkin_ws/src
    $ cd~/catkin_ws/
    $ catkin_make
    $ source devel/setup.bash
    $ echo $ROS_PACKAGE_PATH
        /home/ubuntu/catkin_ws/src:/opt/ros/noetic/share
```

15:52 dvl ROS

```bash
    cd~/catkin_ws/src
    git clone -b master <https://github.com/waterlinked/dvl-a50-ros-driver.git>
    cd~/catkin_ws
    catkin_make
```

16:06 creating a symlink to python 3

```bash
    sudo ln -s /usr/bin/python3 /usr/bin/python
    sudo shutdown now
```

## working on companion pi

16:20 turned on ROV for system work, switched to my computer.
    performed update on web.
        original version: 0.0.24-14-gd62f472
        updated version: 0.0.26
    then re-do dvl integration at <http://192.168.2.2:2770/git>
16:35 ssh into pi
17:00 dvl is not working. trouble shooting.
17:15 solved. dvl power cable is loose! reconnecting.
18:00 re-installing ros-kinetic (ros-comm, as recommended by [ROS wiki](https://wiki.ros.org/ROSberryPi/Installing%20ROS%20Kinetic%20on%20the%20Raspberry%20Pi) on system.

```bash
    $ rosversion -d
        kinetic
    $ cat /etc/os-release
        PRETTY_NAME="Raspbian GNU/Linux 8 (jessie)"
        NAME="Raspbian GNU/Linux"
        VERSION_ID="8"
        VERSION="8 (jessie)"
        ID=raspbian
        ID_LIKE=debian
        HOME_URL="http://www.raspbian.org/"
        SUPPORT_URL="http://www.raspbian.org/RaspbianForums"
        BUG_REPORT_URL="http://www.raspbian.org/RaspbianBugs"
    $ sudo sh -c 'echo "deb <http://packages.ros.org/ros/ubuntu> $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
    $ sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
    $ sudo apt-get update && sudo apt-get upgrade
    $ sudo apt-get install -y python-rosdep python-rosinstall-generator python-wstool python-rosinstall build-essential cmake
    $ rosdep update
    $ rosinstall_generator ros_comm --rosdistro kinetic --deps --wet-only --tar > kinetic-ros_comm-wet.rosinstall
    $ wstool init src kinetic-ros_comm-wet.rosinstall

```

18:30 resolving unavailable dependencies

```bash
    $ mkdir -p ~/ros_catkin_ws/external_src
    $ cd~/ros_catkin_ws/external_src
    $ wget <http://sourceforge.net/projects/assimp/files/assimp-3.1/assimp-3.1.1_no_test_models.zip/download> -O assimp-3.1.1_no_test_models.zip 
    # unzip assimp-3.1.1_no_test_models.zip
    $ cdassimp-3.1.1
    $ cmake .
    $ make
    $ sudo make install
    $ cd~/ros_catkin_ws
    $ rosdep install -y --from-paths src --ignore-src --rosdistro kinetic -r --os=debian:jessie
    $ sudo ./src/catkin/bin/catkin_make_isolated --install -DCMAKE_BUILD_TYPE=Release --install-space /opt/ros/kinetic

```

20:00 finished installation

```bash
    mv -i kinetic-desktop-full-wet.rosinstall kinetic-desktop-full-wet.rosinstall.old
    rosinstall_generator desktop_full --rosdistro kinetic --deps --wet-only --tar > kinetic-desktop-full-wet.rosinstall
    diff -u kinetic-desktop-full-wet.rosinstall kinetic-desktop-full-wet.rosinstall.old
    wstool merge -t src kinetic-desktop-full-wet.rosinstall
    wstool update -t src
    ./src/catkin/bin/catkin_make_isolated --install
    source ~/catkin_ws/devel/setup.bash
    cd~/catkin_ws
    rosinstall_generator ros_comm ros_control joystick_drivers --rosdistro kinetic --deps --wet-only --tar > kinetic-custom_ros.rosinstall
    wstool merge -t src kinetic-custom_ros.rosinstall
    wstool update -t src
    rosdep update
    cd/catkin_ws && catkin_make

```

### encountered problem with catkin_make: missing package

CMake Warning at /opt/ros/kinetic/share/catkin/cmake/catkinConfig.cmake:76 (find_package):

```bash
    Could not find a package configuration file provided by "geometry_msgs" with any of the following names:

    geometry_msgsConfig.cmake
    geometry_msgs-config.cmake

    Add the installation prefix of "geometry_msgs" to CMAKE_PREFIX_PATH or set "geometry_msgs_DIR" to a directory containing one of the above files.  If "geometry_msgs" provides a separate development package or SDK, be sure it has been installed.
Call Stack (most recent call first):
    bluerov/CMakeLists.txt:5 (find_package)

-- Could not find the required component 'geometry_msgs'. The following CMake error indicates that you either need to install the package with the same name or change your environment so that it can be found.
CMake Error at /opt/ros/kinetic/share/catkin/cmake/catkinConfig.cmake:83 (find_package):
    Could not find a package configuration file provided by "geometry_msgs"
    with any of the following names:

    geometry_msgsConfig.cmake
    geometry_msgs-config.cmake

    Add the installation prefix of "geometry_msgs" to CMAKE_PREFIX_PATH or set "geometry_msgs_DIR" to a directory containing one of the above files.  If "geometry_msgs" provides a separate development package or SDK, be sure it has been installed.
Call Stack (most recent call first):
    bluerov/CMakeLists.txt:5 (find_package)

-- Configuring incomplete, errors occurred!
See also "/home/pi/catkin_ws/build/CMakeFiles/CMakeOutput.log".
See also "/home/pi/catkin_ws/build/CMakeFiles/CMakeError.log".
Makefile:318: recipe for target 'cmake_check_build_system' failed
make: *** [cmake_check_build_system] Error 1
Invoking "make cmake_check_build_system" failed
```

---END OF DAY 20210520---

## 21-05-2021

9:30 entered Duckietown

---working on companion pi---

### backed up home dir following <https://www.raspberrypi.org/documentation/linux/filesystem/backup.md>

10:00 try to install ROS kinetic-desktop-full. Reason for re-install: `catkin_make`bluerov package returns Cmake error, missing two packages: "geometry_msgsConfig.cmake" and "geometry_msgs-config.cmake" which are both included in the desktop_full install.

```bash
    $ sudo sh -c 'echo "deb <http://packages.ros.org/ros/ubuntu> $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
    $ sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
    $ sudo apt-get update && sudo apt-get upgrade
    $ sudo apt-get install -y python-rosdep python-rosinstall-generator python-wstool python-rosinstall build-essential cmake
    $ rosdep update
        cmake "-DCMAKE_CXX_FLAGS=- stdlib=libc++" /path/to/rtags
        -- Performing Test HAVE_CXX_WNO_INCONSISTENT_MISSING_OVERRIDE - Failed
        -- Performing Test HAVE_CXX_WNO_UNUSED_CONST_VARIABLE - Failed
        -- Performing Test HAVE_CXX_WNO_SHORTEN_64_TO_32 - Failed
        -- Performing Test HAVE_CXX_WNO_TAUTOLOGICAL_UNDEFINED_COMPARE - Failed
        -- Performing Test HAVE_CXX_WNO_MISSING_PROTOTYPES - Failed
```

16:00 uninstalled Python 3

18:00 initiated: building catkin workspace (LONG PROCESS > 3h) ABORTED AT ERROR

```bash
    cd ~/catkin_ws && sudo ./src/catkin/bin/catkin_make_isolated --install -DCMAKE_BUILD_TYPE=Release --install-space /opt/ros/kinetic
```

22:00 solving tf2 error (SOLVED)

```bash
    <== Failed to process package 'tf2':
        Command '['/opt/ros/kinetic/env.sh', 'make', '-j4', '-l4']' returned non-zero exit status 2

    Reproduce this error by running:
    ==> cd /home/pi/catkin_ws/build_isolated/tf2 && /opt/ros/kinetic/env.sh make -j4 -l4
```

seems to be another code revision incompatibility, the internet says that logError became CONSOLE_BRIDGE_logError. The fix is going into the source file buffer_core.cpp and add in a macro to redefine logWarn and logError. Fixed with following command:

```bash
cd /home/pi/catkin_ws/build_isolated/tf2 && sudo /opt/ros/kinetic/env.sh make -j4 -l4
```

- original

```bash
    #include "tf2/buffer_core.h"
    #include "tf2/time_cache.h"
    #include "tf2/exceptions.h"
    #include "tf2_msgs/TF2Error.h"

    #include <assert.h>
    #include <console_bridge/console.h>
    #include "tf2/LinearMath/Transform.h"
    #include <boost/foreach.hpp>

    namespace tf2
    {

    ...

    rest of the code
```

- updated

```bash
    #include "tf2/buffer_core.h"
    #include "tf2/time_cache.h"
    #include "tf2/exceptions.h"
    #include "tf2_msgs/TF2Error.h"

    #include <assert.h>
    #include <console_bridge/console.h>
    #include "tf2/LinearMath/Transform.h"
    #include <boost/foreach.hpp>

    #ifndef logError // added by frank26080115
    #define logError CONSOLE_BRIDGE_logError
    #endif

    #ifndef logWarn // added by frank26080115
    #define logWarn CONSOLE_BRIDGE_logWarn
    #endif

    namespace tf2
    {

    ...

    rest of the code
```

22:10 reinitiated: building catkin workspace (ABORTED AT ERROR)

```bash
    cd~/catkin_ws && sudo ./src/catkin/bin/catkin_make_isolated --install -DCMAKE_BUILD_TYPE=Release --install-space /opt/ros/kinetic

```

---working on new pi---

13:00 setting commandline startup for new pi

```bash
        GRUB_CMDLINE_LINUX_DEFAULT="text"
        GRUB_TERMINAL=console

```

save changes, then run:

```bash
    sudo update-grub
    sudo systemctl enable multi-user.target --force
    sudo systemctl set-default multi-user.target
```

To undo `sudo systemctl set-default multi-user.target` simply type:
    $ sudo systemctl enable graphical.target --force
    $ sudo systemctl set-default graphical.target

---working on Qground Control---

read later: troubleshooting relating to parameter setting <https://discuss.bluerobotics.com/t/water-linked-dvl-a50-support/9123>

change parameters/PID in QGroundControl:
    original:

```
    AHRS_EKF_TYPE 3
    EK2_ENABLE 0
    EK3_ENABLE 1
    VISO_TYPE 1
    EK3_GPS_TYPE 31
    PSC_POSXY_P 1.0
    PSC_POSZ_P 3.0
    PSC_VELXY_D 0.0
    PSC_VELXY_I 0.5
    PSC_VELXY_P 1.0
    PSC_VELZ_P 8.0
```

new, per recommendations in the PR for creating the DVL service <https://www.ardusub.com/developers/dvl-integration.html>:

```
    AHRS_EKF_TYPE 3
    EK2_ENABLE 0
    EK3_ENABLE 1
    VISO_TYPE 1
    EK3_GPS_TYPE 3
    PSC_POSXY_P 2.5 # ERROR! QGC shows max 2.0?? force changed
    PSC_POSZ_P 1.0
    PSC_VELXY_D 0.8
    PSC_VELXY_I 0.5
    PSC_VELXY_P 5.0
    PSC_VELZ_P 5.0
```

The command history until 00:00 22 May 2021 is saved with the following command, at the external backup SD Card named backup_companion

```bash
    $ cd/backup_companion
    history > history_for_print.txt
```

---END OF DAY 20210521---

NEXT:
0. continue installation

1. take rov to OceanLab for testing position keeping & DVL data output for Nav
2. try to connect two pi. Problem: how to power the second pi?

### read later

[Using ROS on Multiple Machines](https://wiki.ros.org/ROS/Tutorials/MultipleMachines)
[Controlling a robot using 2 raspberry pi's](https://www.raspberrypi.org/forums/viewtopic.php?t=202489)

For Communication between two pi's: maybe connect with LAN and use TCP sockets to send and receive messages? A solution: First ping to second pi from first pi. Then use the other Pi's GPIO open a connection in the script on first Pi with

```bash
    remote_pi = pigpio.pi("192.168.1.2")
```

and then use remote_pi to reference the others GPIO, e.g.

```bash
    remote_pi.read(4)
```

or

```bash
    remote_pi.set_PWM_dutycycle(4, 64)
```

etc.

Other options are:

- Python socket examples
- MQTT examples
- Use pigpio Python module <http://abyz.me.uk/rpi/pigpio/python.html>

Easiest solution: get everything to work on the first pi. However, a reason for additional pi: Externalize heavy computation.
> "you would probably have 2 planners: one local real-time planner, doing basic stuff like react to unexpected obstacles and avoid crashing into things. The other planner (let’s call it the global planner) would do some more heavy computation, and provide a complete path to follow every 5 seconds, depending on all the possible variables that are available from all sensors in the environment.
> As you can guess, the real-time planner needs less computation power, but a really high frequency loop execution. So, keeping it on the Raspberry Pi is certainly the best choice to make here.
> As for the global planner, you don’t need a high execution frequency (one execution every few seconds), but you need some heavy computations to be made, which is not something your Raspberry Pi can handle easily.
> In this scenario, you could externalize the global planner to a standard computer/laptop, which is working remotely from the robot. Every 5 seconds, the fast computer will compute the new updated path and send it to the Raspberry Pi, which will try to follow it, and react accordingly to obstacles with the real-time planner.
> There is a simple way to do that: you can use the multi-machine functionality from ROS. Basically you’d launch a ROS master in one of the two machines, and then connect the machines together, so they can exchange data through topics, services, actions, etc.
> To go further, let’s imagine you now have a robot fleet, with 10 robots powered by Raspberry Pi, plus one central computer. The latter will be used to monitor all robots, and create a path for each one so they do not collide with the environment and other robots.
> You’d create a multi-machine environment, where each robot follows the order from the “master” computer. Each robot would just run the necessary code to interact with hardware, and let the master computer do the heavy work."

## 22-05-2021

12:30 entered Duckietown
13:00 powered on.

```bash
    # install missing package from source
    $ cd~/catkin_ws/src
    git clone <https://github.com/ros-geographic-info/geographic_info.git>
    $ cd~/catkin_ws
    rosdep install --from-paths src/ --ignore-src --rosdistro kinetic
    # however encountered error:
        ERROR: the following packages/stacks could not have their rosdep keys resolved to system dependencies:
        bluerov_apps: Cannot locate rosdep definition for [pcl_ros]
        geographic_msgs: Cannot locate rosdep definition for [uuid_msgs]
        mavros: Cannot locate rosdep definition for [tf2_eigen]
        mavros_extras: Cannot locate rosdep definition for [tf2_eigen]
        test_mavros: Cannot locate rosdep definition for [control_toolbox]
        geodesy: Cannot locate rosdep definition for [uuid_msgs]
    catkin_make
```

18:50 Installing Python 3.6 on Raspbian, following this guide <https://gist.github.com/dschep/24aa61672a2092246eaca2824400d37f>

```bash
    $ sudo apt-get update
    $ sudo apt-get install build-essential tk-dev libncurses5-dev libncursesw5-dev libreadline6-dev libdb5.3-dev libgdbm-dev libsqlite3-dev libssl-dev libbz2-dev libexpat1-dev liblzma-dev zlib1g-dev
    wget <https://www.python.org/ftp/python/3.6.5/Python-3.6.5.tar.xz>
    tar xf Python-3.6.5.tar.xz
    $ cdPython-3.6.5
    ./configure
    make -j 4 # takes about 15 minutes (it produces 8 warnings to `stderr`)
    $ sudo make altinstall
    takes about 2 minutes
    wget <https://bootstrap.pypa.io/get-pip.py>
    python3.6 ./get-pip.py
    export $PATH
    export PATH=$PATH:/home/pi:/home/pi/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/games:/usr/games
```

To use python3.6, use pip3.6 and python3.6 instead.

## 25-05-2021

12:00 entered Duckietown, found battery charger on & hot, shows "High Voltage" when plugging in both ROV batteries.

Check [ROS-kinetic system requirements](https://www.ros.org/reps/rep-0003.html#kinetic-kame-may-2016-may-2021):

```
Kinetic Kame (May 2016 - May 2021)
Required Support for:
    Ubuntu Wily (15.10)
    Ubuntu Xenial (16.04)
Recommended Support for:
    Debian Jessie
    Fedora 23
    Fedora 24
Minimum Requirements:
    C++11
        GCC 4.9 on Linux, as it's the version that Debian Jessie ships with
    Python 2.7
        Python 3.4 not required, but testing against it is recommended
    Lisp SBCL 1.2.4
    CMake 3.0.2
        Debian Jessie ships with CMake 3.0.2
    Boost 1.55
        Debian Jessie ships with Boost 1.55
Exact or Series Requirements:
    Ogre3D 1.9.x
    Gazebo 7
    PCL 1.7.x
    OpenCV 3.x
    Qt 5.3.x
    PyQt5
Build System Support:
    catkin:
        build from source
        release for binary packaging
        wiki documentation
        continuous integration
    rosbuild:
        build from source

```

12:38

```bash
    $ cdcatkin_ws
    wstool merge kinetic-ros_comm-wet.rosinstall -t src
    wstool update -j 4 -t src
```

13:00 updated to include:

```bash
    sudo nano /etc/apt/sources.list.d/ros-latest.list
```

```bash
    deb http://packages.ros.org/ros/debian jessie main non-free contrib
    deb-src http://packages.ros.org/ros/debian jessie main non-free contrib

    deb http://mirror.pregi.net/debian/ jessie main non-free contrib
    deb-src http://mirror.pregi.net/debian/ jessie main non-free contrib

    deb http://security.debian.org/ jessie/updates main contrib non-free
    deb-src http://security.debian.org/ jessie/updates main contrib non-free

deb <http://packages.ros.org/ros/debian> jessie main

deb <http://mirror.pregi.net/debian/> jessie main non-free contrib
deb-src <http://mirror.pregi.net/debian/> jessie main non-free contrib

deb <http://security.debian.org/> jessie/updates main contrib non-free
deb-src <http://security.debian.org/> jessie/updates main contrib non-free
```

ROS delete package:
For removing packages: Just delete them (and rebuild). They'll be gone.
For removing a workspace: Just delete it (there are no other traces).

```bash
    sudo apt-get remove ros-$Distro-$package_name
```

ERROR:

```bash
    IOError: [Errno 13] Permission denied: '/home/pi/ros_catkin_ws/build_isolated/.built_by'
```

Reason:
    It's likely you ran catkin_make_isolated as root at some point (using `sudo`). Seeing as you now attempt to run it as a regular user, you get permission errors, as you are not allowed to access the files and/or directories owned by root.
fixed issue:

```bash
    $ sudo chown $USER: -R /home/pi/ros_catkin_ws
    which will make $USER the owner of all the files in the workspace again.
```

To make sure your workspace is properly overlayed by the setup script, make sure ROS_PACKAGE_PATH environment variable includes the directory you're in:

```bash
    source devel/setup.bash
    echo $ROS_PACKAGE_PATH
```

ROS Kinetic installation guide:

```bash
        $ sudo sh -c 'echo "deb <http://packages.ros.org/ros/ubuntu> $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
        wget <http://packages.ros.org/ros.key> -O - | sudo apt-key add -
        $ sudo apt-get update
        # NO LONGER WORK! NEED TO INSTALL FROM SOURCE.
        $ sudo apt-get install ros-kinetic-desktop-full
        $ sudo rosdep init
        rosdep update
        # Step 8: Setting up the ROS environment variables
        echo "source /opt/ros/kinetic/setup.bash" >> ~/.bashrc
        source ~/.bashrc
        # Step 9: Create and initialize the catkin workspace
        mkdir -p ~/catkin_ws/src
        $ cdcatkin_ws/src
        catkin_init_workspace
        $ cd~/catkin_ws/
        catkin_make
        # Step 10: Add the catkin_workspace to your ROS environment
        source ~/catkin_ws/devel/setup.bash
        echo “source ~/catkin_workspace/devel/setup.bash” >> ~/.bashrc
        # Step 11: Check the ROS environment variables
        export | grep ROS
        # Step 12: Check ROS installation
        roscore
        rosrun turtlesim turtlesim_node
```

BlueROV Simulation

- [Tutorial on how to set up Gazebo simulations of the BlueROV2 using SITL and ArduSub, and a package for use with mavros](https://gist.github.com/monabf/bc04b7ab366f812c645bf0aa6f22c8de)
- [How to start bluerov simulator](https://risc.readthedocs.io/4-marine-2.html)
- [ROS for BlueROV](https://robots.ros.org/bluerov/)

14:30 `rm -rf *isolated`

Instructions to install from source for ROS Kinetic on Debian Jessie:

```bash
    mkdir -p ~/ros_catkin_ws/external_src
    cd ~/ros_catkin_ws/external_src
    wget <http://sourceforge.net/projects/assimp/files/assimp-3.1/assimp-3.1.1_no_test_models.zip/download> -O assimp-3.1.1_no_test_models.zip
    unzip assimp-3.1.1_no_test_models.zip
    cd assimp-3.1.1
    cmake .
    make -j2
    sudo make install -j2
```

How to install ROS Package from git:

```bash
    cd ~/catkin_ws/src
    git clone -b <branch> <address>
    cd ~/catkin_ws
    catkin_make
```

Resolve Dependency:

```bash
    cd ~/catkin_ws
    rosdep install -y --from-paths src --ignore-src --rosdistro kinetic -r --os=debian:jessie
```

Build Catkin Workspace:

```bash
    cd ~/catkin_ws
    catkin build --dry-run    # Show the package build order
    sudo ./src/catkin/bin/catkin_make_isolated --install -DCMAKE_BUILD_TYPE=Release --install-space /opt/ros/kinetic -j2
    source /opt/ros/kinetic/setup.bash
    echo "source /opt/ros/kinetic/setup.bash" >> ~/.bashrc
```

When building speficic packages:

```bash
    cd /catkin_ws
    catkin build dvl-a50-ros-driver        # Build dvl-a50-ros-driver and its dependencies
```

Repair:

```bash
    apt-get install -f
    sudo apt-get -f install
```

Maintenance

```bash
    sudo apt-get update ; sudo apt-get upgrade
    sudo apt-get clean ; sudo apt-get autoclean ; sudo apt-get autoremove
```

Workspace Maintenance:

```bash
    # Cleaning Products from Missing Packages
    $ catkin clean --orphans
```

TASKS

- FIRST: install ROS workspace
- SECOND: install DVL to output ROS message <https://github.com/waterlinked/dv-a50-ros-driver>

run ROS
changed first line of publisher.py to .../python2

22:28 traversing 211 packages in topological order
    ~~  - catkin
    ~~  - genmsg
    ~~  - gencpp
    ~~  - geneus
    ~~  - genlisp
    ~~  - gennodejs
    ~~  - genpy
    ~~  - bond_core
    ~~  - cmake_modules
    ~~  - class_loader
    ~~  - common_msgs
    ~~  - common_tutorials
    ~~  - cpp_common
    ~~  - desktop
    ~~  - diagnostics
    ~~  - eigen_stl_containers
    ~~  - executive_smach
    ~~  - geographic_info
    ~~  - geometry
    ~~  - geometry_tutorials
    ~~  - gl_dependency
    ~~  - joystick_drivers
    ~~  - mavlink (plain cmake)
    ~~  - media_export
    ~~  - message_generation
    ~~  - message_runtime
    ~~  - mk
    ~~  - nodelet_core
    ~~  - octomap (plain cmake)
    ~~  - opencv3 (plain cmake)
    ~~  - orocos_kdl (plain cmake)
    ~~  - python_orocos_kdl (plain cmake)
    ~~  - qt_dotgraph
    ~~  - qt_gui
    ~~  - qt_gui_py_common
    ~~  - qwt_dependency
    ~~  - random_numbers
    ~~  - robot
    ~~  - robot_model
    ~~  - ros
    ~~  - ros_base
    ~~  - ros_comm
    ~~  - ros_core
    ~~  - ros_environment
    ~~  - ros_tutorials
    ~~  - rosbag_migration_rule
    ~~  - rosbash
    ~~  - rosboost_cfg
    ~~  - rosbuild
    ~~  - rosclean
    ~~  - roscpp_core
    ~~  - roscpp_traits
    ~~  - roscreate
    ~~  - rosgraph
    ~~  - roslang
    ~~  - roslint
    ~~  - roslisp
    ~~  - rosmake
    ~~  - rosmaster
    ~~  - rospack
    ~~  - roslib
    ~~  - rosparam
    ~~  - rospy
    ~~  - rosservice
    ~~  - rostime
    ~~  - roscpp_serialization
    ~~  - python_qt_binding
    ~~  - roslaunch
    ~~  - rosunit
    ~~  - angles
    ~~  - libmavconn
    ~~  - rosconsole
    ~~  - pluginlib
    ~~  - qt_gui_cpp
    ~~  - resource_retriever
    ~~  - rosconsole_bridge
    ~~  - roslz4
    ~~  - rosbag_storage
    ~~  - rostest
    ~~  - rqt_action
    ~~  - rqt_bag
    ~~  - rqt_bag_plugins
    ~~  - rqt_common_plugins
    ~~  - rqt_console
    ~~  - rqt_dep
    ~~  - rqt_graph
    ~~  - rqt_gui
    ~~  - rqt_logger_level
    ~~  - rqt_moveit
    ~~  - rqt_msg
    ~~  - rqt_nav_view
    ~~  - rqt_plot
    ~~  - rqt_pose_view
    ~~  - rqt_publisher
    ~~  - rqt_py_console
    ~~  - rqt_robot_dashboard
    ~~  - rqt_robot_monitor
    ~~  - rqt_robot_plugins
    ~~  - rqt_robot_steering
    ~~  - rqt_runtime_monitor
    ~~  - rqt_service_caller
    ~~  - rqt_shell
    ~~  - rqt_srv
    ~~  - rqt_tf_tree
    ~~  - rqt_top
    ~~  - rqt_topic
    ~~  - rqt_web
    ~~  - smach
    ~~  - smclib
    ~~  - std_msgs
    ~~  - actionlib_msgs
    ~~  - bond
    ~~  - diagnostic_msgs
    ~~  - geometry_msgs
    ~~  - eigen_conversions
    ~~  - kdl_conversions
    ~~  - nav_msgs
    ~~  - rosgraph_msgs
    ~~  - rosmsg
    ~~  - rqt_py_common
    ~~  - shape_msgs
    ~~  - smach_msgs
    ~~  - std_srvs
    ~~  - tf2_msgs
    ~~  - tf2
    ~~  - trajectory_msgs
    ~~  - control_msgs
    ~~  - unique_identifier
    ~~  - urdf_parser_plugin
    ~~  - uuid_msgs
    ~~  - geographic_msgs
    ~~  - visualization_msgs
    ~~  - geometric_shapes
    ~~  - visualization_tutorials
    ~~  - viz
    ~~  - webkit_dependency
    ~~  - xmlrpcpp
    ~~  - roscpp
    ~~  - bondcpp
    ~~  - bondpy
    ~~  - nodelet
    ~~  - nodelet_tutorial_math
    ~~  - pluginlib_tutorials
    ~~  - roscpp_tutorials
    ~~  - rosout
    ~~  - diagnostic_aggregator
    ~~  - diagnostic_updater
    ~~  - diagnostic_common_diagnostics
    ~~  - dynamic_reconfigure
    ~~  - filters
    ~~  - joint_state_publisher
    ~~  - message_filters
    ~~  - rosnode
    ~~  - rospy_tutorials
    ~~  - rostopic
    ~~  - roswtf
    ~~  - rqt_gui_cpp
    ~~  - rqt_gui_py
    ~~  - rqt_reconfigure
    ~~  - self_test
    ~~  - smach_ros
    ~~  - tf2_py
    ~~  - topic_tools
    ~~  - rosbag
    ~~  - actionlib
    ~~  - actionlib_tutorials
    ~~  - diagnostic_analysis
    ~~  - nodelet_topic_tools
    ~~  - rqt_launch
    ~~  - sensor_msgs
    ~~  - cv_bridge
    ~~  - image_transport
    ~~  - joy
    ~~  - map_msgs
    ~~  - mavros_msgs
    ~~  - pcl_msgs
    ~~  - ps3joy
    ~~  - rqt_image_view
    ~~  - spacenav_node
    ~~  - stereo_msgs
    ~~  - tf2_ros
    ~~  - mavros
    ~~  - tf
    ~~  - interactive_markers
    ~~  - interactive_marker_tutorials
    ~~  - tf2_geometry_msgs
    ~~  - laser_geometry
    ~~  - tf2_kdl
    ~~  - tf_conversions
    ~~  - turtlesim
    ~~  - turtle_actionlib
    ~~  - turtle_tf
    ~~  - turtle_tf2
    ~~  - unique_id
    ~~  - geodesy
    ~~  - urdf
    ~~  - collada_parser
    ~~  - collada_urdf
    ~~  - kdl_parser
    ~~  - mavros_extras
    ~~  - robot_state_publisher
    ~~  - rviz
    ~~  - librviz_tutorial
    ~~  - rqt_rviz
    ~~  - rviz_plugin_tutorials
    ~~  - rviz_python_tutorial
    ~~  - test_mavros
    ~~  - visualization_marker_tutorials
    ~~  - waterlinked_a50_ros_driver
    ~~  - wiimote
    ~~  - xacro

22:30 re-installing ros-kinetics_comms-wet

```bash
    mv ~/catkin_ws ~/ros_catkin_ws
    rosdep install --from-paths src --ignore-src --rosdistro kinetic -y
    ./src/catkin/bin/catkin_make_isolated --install -DCMAKE_BUILD_TYPE=Release
    source ~/ros_catkin_ws/install_isolated/setup.bash
```

Blue ROV ROS Packages
install ROS kinetic dependencies

```bash
sudo apt-get install -y ros-kinetic-image-common ros-kinetic-robot-state-publisher ros-kinetic-joint-state-publisher ros-kinetic-image-transport-plugins ros-kinetic-mavros ros-kinetic-mavros-msgs ros-kinetic-mavros-extras ros-kinetic-joy
```

```bash
    E: Unable to locate package ros-kinetic-image-common
    E: Unable to locate package ros-kinetic-robot-state-publisher
    E: Unable to locate package ros-kinetic-joint-state-publisher
    E: Unable to locate package ros-kinetic-image-transport-plugins
    E: Unable to locate package ros-kinetic-mavros
    E: Unable to locate package ros-kinetic-mavros-msgs
    E: Unable to locate package ros-kinetic-mavros-extras
    E: Unable to locate package ros-kinetic-joy
```

install from source because above commands don't work anymore (Tutorial on installing ROS packages from source <http://wiki.ros.org/qb_hand/Tutorials/ROS%20Packages%20Installation>)

```bash
    $ sudo gpasswd -a pi dialout
    cd ~/catkin_ws/src
    wstool init
    git clone --branch hydro-devel https://github.com/ros-perception/image_common.git
    git clone --branch indigo-devel https://github.com/ros-perception/image_transport_plugins.git
    git clone --branch indigo-devel https://github.com/ros-drivers/joystick_drivers.git
    git clone --branch kinetic-devel https://github.com/ros/robot_state_publisher.git
    git clone --branch kinetic-devel https://github.com/ros/joint_state_publisher.git
    git clone --branch master https://github.com/mavlink/mavros.git
```

Also, ros-kinetic-ros-controllers, ros-kinetic-transmission-interface, ros-kinetic-joint-limits-interface, ros-kinetic-combined-robot-hw
    NOT WORKING! Each of the packages prevents `catkin_make`from finishing and returns various errors.
install ros_control

```bash
    wstool merge https://raw.github.com/ros-controls/ros_control/kinetic-devel/ros_control.rosinstall
    wstool update
    cd ..
    rosdep install --from-paths . --ignore-src --rosdistro kinetic -y
    catkin_make
```

build single ROS package using catkin_make

```bash
    catkin_make --only-pkg-with-deps <target_package>
```

Don't forget to switch back to building all packages when you are done

```bash
    catkin_make -DCATKIN_WHITELIST_PACKAGES=""
```

clone BlueROV packages

```bash
    mkdir ~/repos
    git clone https://github.com/bluerobotics/bluerov-ros-pkg.git ~/repos/bluerov-ros-pkg
    ln -s ~/repos/bluerov-ros-pkg/bluerov ~/catkin_ws/src/bluerov
    ln -s ~/repos/bluerov-ros-pkg/bluerov_apps ~/catkin_ws/src/bluerov_apps
```

udev rules

```bash
    sudo cp ~/catkin_ws/src/bluerov/debian/99-bluerov.rules /etc/udev/rules.d/
    sudo cp ~/catkin_ws/src/bluerov_apps/debian/99-bluerov-apps.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules && sudo service udev restart && sudo udevadm trigger
```

build

```bash
    cd ~/catkin_ws
    source devel/setup.bash
    echo $ROS_PACKAGE_PATH
    catkin_make
```

INSTALLING DVL_ROS_DRIVER

```bash
    cd ~/catkin_ws/src
    git clone -b master https://github.com/waterlinked/dvl-a50-ros-driver.git
    cd ~/catkin_ws
    catkin_make
```

installing DVL python

```bash
    pip install crcmod pyserial
    cd /home/pi/
    git clone https://github.com/waterlinked/dvl-python.git
    cd ~/dvl-python
    pip install -e .
    python
```

```python
        >>>  from wldvl import WlDVL
        >>>  dvl = WlDVL("/dev/ttyUSB0")
        >>>  dvl.read()
            {'fom': 0.002, 'time': 40.57, 'vy': 0.004, 'vz': -0.002, 'vx': -0.003, 'valid': True, 'altitude': 0.14}
```

freeing up space on companion pi by deleting legacy ROS workspace (backed up)

```bash
    rm -rf ros_catkin_ws
```

Also, about [which build/make tool is the best](<https://answers.ros.org/question/320613/catkin_make-vs-catkin_make_isolated-which-is-preferred/>)
> `catkin_make` was the first script around to build catkin workspace and is therefore used in many tutorials. It has several down sides (requiring non-standard logic in packages to declared cross-package target dependencies) and limitation (can't process plain CMake packages, requires all targets across all packages in a workspace to be unique). Therefore I wouldn't recommend to use it.
> catkin_make_isolated is the script which was developed next which addresses all these shortcomings. It comes at the cost of being slower since it processes all packages sequentially. It is being used on build.ros.org in the devel and PR jobs. I would recommend using this if you want the most reliable solution (exclusively for ROS 1).
> catkin_tools (called catkin build above) is similar to catkin_make_isolated but addresses the performance limitation by processing packages in parallel where possible. It also has a lot of usability features which makes it much easier to use and configure. On the downside this tool is not being actively maintained for the past years so I wouldn't recommend it either.
> colcon is the new build tool developed for ROS 2 and works similar to catkin_tools with less usability features at the moment but being able to build any kind of packages (catkin, ament, CMake, Python setuptools, gradle, bazel, cargo, ...) on all major platforms (Linux, macOS, Windows). While developed for ROS 2 it in principle also works for ROS 1. If you are willing to use something more bleeding edge (which might come with quirks which haven't been resolved / polished yet) this might be an option. The big advantage is that the tool is very modular and actively developed and extended by multiple parties.
Good resource: [catkin-tools](https://catkin-tools.readthedocs.io/en/latest.html)

The command history until 02:00 26 May 2021 is saved with the following command, at the external backup SD Card named backup_companion

```bash
    cd /backup_companion
    $ sudo history > history_for_print.txt
```

---END OF DAY 20210525---

## 26-05-2021

### creating a backup

```bash
    sudo rsync -av --delete /home/pi /backup_companion/home/pi
```

#### rosrun encountered error: WARNING: ROS_MASTER_URI [http://danter-eth:11311/] host is not set to this machine. solved with following command

```bash
    export ROS_MASTER_URI=https://localhost:11311
    catkin_make
```

```bash
        errors encountered: missing package from ros_controls. Solved with following command.
```

weirdly, it prompts rosdep is not installed. re-installing

```bash
    sudo apt-get install python-rosdep
```

install package: ros_controls

```bash
    wstool init
    wstool merge https://raw.github.com/ros-controls/ros_control/kinetic-devel/ros_control.rosinstall
    wstool update
    cd ..
    rosdep install --from-paths . --ignore-src --rosdistro kinetic -y
    source ~/catkin_ws/devel/setup.bash
    catkin_make --only-pkg-with-deps ros_control
```

install package: urdf

```bash
    cd ~/catkin_ws/src
    git clone -b kinetic-devel https://github.com/ros/urdf.git
    wstool update
    cd ~/catkin_ws
    rosdep install --from-paths . --ignore-src --rosdistro kinetic -y
    source ~/catkin_ws/devel/setup.bash
    catkin_make --only-pkg-with-deps urdf
```

switch to build everything

```bash
    catkin_make -DCATKIN_WHITELIST_PACKAGES=""
        returns errors.
```

install four_wheel_control_msgs

```bash
    cd ~/catkin_ws/src
    git clone -b master https://github.com/ros-drivers/four_wheel_steering_msgs.git
    wstool update -j4 -t src
    wstool update
    cd ~/catkin_ws
    rosdep install --from-paths . --ignore-src --rosdistro kinetic -y
    source ~/catkin_ws/devel/setup.bash
    catkin_make --only-pkg-with-deps four_wheel_steering_msgs
```

install tf

```bash
    cd ~/catkin_ws/src
    git clone -b indigo-devel https://github.com/ros/geometry.git
    wstool update -j4 -t src
    wstool update
    cd ~/catkin_ws
    rosdep install --from-paths . --ignore-src --rosdistro kinetic -y
    source ~/catkin_ws/devel/setup.bash
    catkin_make --only-pkg-with-deps geometry
```

install tf returns error: tf2_rosConfig.cmake tf2_ros-config.cmake NOT FOUND. Solved with

```bash
    cd ~/catkin_ws
    rosdep install --from-paths ./src --ignore-src --rosdistro kinetic
        ERROR: the following packages/stacks could not have their rosdep keys resolved to system dependencies:
        bluerov_apps: Cannot locate rosdep definition for [pcl_ros]
        gripper_action_controller: Cannot locate rosdep definition for [xacro]
        diff_drive_controller: Cannot locate rosdep definition for [xacro]
        mavros: Cannot locate rosdep definition for [geographic_msgs]
        ackermann_steering_controller: Cannot locate rosdep definition for [xacro]
        mavros_msgs: Cannot locate rosdep definition for [geographic_msgs]
        mavros_extras: Cannot locate rosdep definition for [tf2_eigen]
        test_mavros: Cannot locate rosdep definition for [tf2_ros]
        tf: Cannot locate rosdep definition for [tf2_ros]
        joint_trajectory_controller: Cannot locate rosdep definition for [xacro]
        robot_state_publisher: Cannot locate rosdep definition for [tf2_kdl]
        four_wheel_steering_controller: Cannot locate rosdep definition for [urdf_geometry_parser]
    source /opt/ros/kinetic/setup.bash
```

to solve the above ERROR

```bash
    cd ~/catkin_ws/src
    git clone -b kinetic-devel https://github.com/ros-perception/perception_pcl.git 
    git clone -b kinetic-devel https://github.com/ros/xacro.git
    git clone -b master https://github.com/ros-geographic-info/geographic_info.git
    git clone -b indigo-devel https://github.com/ros/geometry2.git
    git clone -b kinetic-devel https://github.com/ros-controls/urdf_geometry_parser.git
    git clone -b master https://github.com/ros-geographic-info/unique_identifier.git
    git clone -b kinetic-devel https://github.com/ros-simulation/gazebo_ros_pkgs.git
    git clone -b kinetic-devel https://github.com/ros/metapackages.git
    cd ~/catkin_ws
    wstool update -j4 -t src
    wstool update
    rosdep install --from-paths . --ignore-src --rosdistro kinetic -y
    rosdep install -y --from-paths src --ignore-src --rosdistro kinetic -r --os=debian:jessie
        ERROR (non-fatal): 
            `sudo -H apt-get install -y libgazebo7-dev` failed with missing dependencies: libgazebo7-dev : 
            Depends: libtbb-dev but it is not installable
            Depends: libsimbody-dev but it is not going to be installed
            Depends: libgazebo7 (= 7.0.0+dfsg-2osrf1~jessie1) but it is not going to be installed
            Depends: gazebo7-plugin-base (= 7.0.0+dfsg-2osrf1~jessie1) but it is not going to be installed
```

SOLVED WITH FOLLOWING (Uses Aplitude which offers you a solution to your dependencies):

```bash
    $ sudo aptitude install gazebo7
    source ~/catkin_ws/devel/setup.bash
    catkin_make --only-pkg-with-deps perception-pcl
```

```bash
    catkin_make --only-pkg-with-deps geometry2
```

returns further errors "No package 'bullet' found". solved with following:

```bash
    $ sudo apt-get install libbullet-dev
    catkin_make --only-pkg-with-deps tf2_bullet
```

returns errors with `tf2` and `geometry2/tf2/CMakeFiles/tf2.dir/src/buffer_core.cpp.o`.
Solved with [following](https://eleccelerator.com/wiki/index.php?title=Raspbian_Buster_ROS_RealSense#Problem:_logWarn_or_logError_not_declared_in_scope) This is another code revision incompatibility. It seems that `logError` became `CONSOLE_BRIDGE_logError`. The fix is going into the source file `buffer_core.cpp` and add in a macro to redefine `logWarn` and `logError`:

```bash
    sudo nano ~/catkin_ws/src/geometry2/tf2/src/buffer_core.cpp
```

```bash
    #include "tf2/buffer_core.h"
    #include "tf2/time_cache.h"
    #include "tf2/exceptions.h"
    #include "tf2_msgs/TF2Error.h"

    #include <assert.h>
    #include <console_bridge/console.h>
    #include "tf2/LinearMath/Transform.h"
    #include <boost/foreach.hpp>

    #ifndef logError // added by frank26080115
    #define logError CONSOLE_BRIDGE_logError
    #endif

    #ifndef logWarn // added by frank26080115
    #define logWarn CONSOLE_BRIDGE_logWarn
    #endif


    namespace tf2
    {

    ...

    rest of the code
```

```bash
    catkin_make --only-pkg-with-deps urdf_geometry_parser
    catkin_make --only-pkg-with-deps geographic_info
    catkin_make --only-pkg-with-deps xacron
    catkin_make --only-pkg-with-deps unique_identifier
    catkin_make --only-pkg-with-deps geodesy
        ERROR
```

SOLVED WITH:

```bash
    cd ~/catkin_ws/src/perception_pcl
    git clone -b indigo-devel https://github.com/ros-perception/pcl_conversions.git
    git clone -b indigo-devel https://github.com/ros-perception/pcl_msgs.git
    wstool update -j4 -t src
    wstool update
    cd ~/catkin_ws
    rosdep install --from-paths . --ignore-src --rosdistro kinetic -y
    rosdep install -y --from-paths src --ignore-src --rosdistro kinetic -r --os=debian:jessie
    source ~/catkin_ws/devel/setup.bash
    catkin_make --only-pkg-with-deps pcl_conversions
    catkin_make --only-pkg-with-deps pcl_msgs

```

Solving unavailable dependencies: Compilation of collada_urdf will fail per <http://answers.ros.org/question/48084/urdf_to_collada-undefined-reference-to-vtable-for-assimpiosystem/>

```bash
    mkdir -p ~/catkin_ws/external_src
    cd ~/catkin_ws/external_src
    wget http://sourceforge.net/projects/assimp/files/assimp-3.1/assimp-3.1.1_no_test_models.zip/download -O assimp-3.1.1_no_test_models.zip
    unzip assimp-3.1.1_no_test_models.zip
    cd assimp-3.1.1
    cmake .
    make
    $ sudo make install
```

Resolving Dependencies (maintenance, why not?)

```bash
    cd ~/catkin_ws
    rosdep install -y --from-paths src --ignore-src --rosdistro kinetic -r --os=debian:jessie
        ERROR: the following packages/stacks could not have their rosdep keys resolved to system dependencies:
        bluerov_apps: Cannot locate rosdep definition for [pcl_conversions]
        geographic_msgs: Cannot locate rosdep definition for [uuid_msgs]
        perception_pcl: Cannot locate rosdep definition for [pcl_msgs]
        robot_state_publisher: Cannot locate rosdep definition for [kdl_parser]
        ackermann_steering_controller: Cannot locate rosdep definition for [gazebo_ros]
        geodesy: Cannot locate rosdep definition for [uuid_msgs]
        pcl_ros: Cannot locate rosdep definition for [pcl_msgs]
```

set up tf

```bash
    cd ~/catkin-ws/src
    catkin_create_pkg robot_setup_tf roscpp tf geometry_msgs
    catkin_make --only-pkg-with-deps four_wheel_steering_controller

$ sudo apt-get install -y ros-kinetic-ros-control
    ros-kinetic-ros-controllers
    ros-kinetic-gazebo-ros-pkgs
    ros-kinetic-gazebo-ros-control
    gazebo2
    libsdformat1
    ros-kinetic-gazebo-plugins
    ros-kinetic-gazebo-ros
    ros-kinetic-joystick-drivers
rosdep install
    robot_state_publisher
    urdf
    xacro
    controller_manager
    geometry_msgs
    joy
    ros_control
    ros_controllers
    sensor_msgs
    std_msgs
    gazebo_msgs
    gazebo_plugins
    gazebo_ros
    gazebo_ros_control
    joy
```

The command history until 23:00 26 May 2021 is saved with the following command, at the external backup SD Card `/backup_companion`.

```bash
    cd /backup_companion
    $ sudo -i
    history > history_for_print_20200526.txt
    shutdown now
```

---END OF DAY 20210526---

TO DO 20210527:

1. `catkin_make` no errors

## 27-05-2021

10:00 meet alex to assemble robot
11:00 move robot to ocean lab for a swim. Take with: Robot, Joystick, Battery, Connection, GoPro,
---TESTING IN OCEAN LAB---
12:34 start output json file to out1.txt

did not work out!

Workflow of position hold test:
    1. go to <http://192.168.2.2:2770/waterlinked>
    2. the "Status" field in the Waterlinked page should read Running....
    3. QGC will announce "EKF3 IMU0 STARTED RELATIVE AIDING" and then "EKF3 IMU0 FUSING ODOMETRY" (This means the DVL input is being fused.)
    4. switch to POSHOLD mode

## 28-05-2021

Problem: No video feed on mac QGC (downloaded from QGC website) SOLVED
    Solved by replacing current version with the QGC provided by BlueRobotics <https://docs.bluerobotics.com/downloads/#ardusub-firmware-files>

20:00 entered Ocean Lab for testing
21:00 out of disk space! (13.95 out of 14) use ncdu to make room

```bash
    $ sudo ncdu -rx / # check large files
    lsblk
    $ sudo mkdir /media/usb
    $ sudo mount /dev/sdb1 /media/usb # mount back up disk
    $ sudo find /tmp -type f -atime +10 -delete # deletes tmp files older than 10 days
    $ sudo rsync -av /backup_companion /media/usb
    rm -rf /backup_companion # do this AFTER DOUBLE CHECK COPIED FILE
    $ sudo umount /media/usb # don't forget to unmount
```

goals:

1. `catkin_make`successful build
2. dvl ros successful run
3. get dvl ros message
4. interpret dvl ros message
5. dvl position hold
6. finish ppt script

ERROR `catkin_make`returns errors: missing gazeboConfig.CMake
solved!!!added repos:

```bash
    sudo nano /etc/apt/sources.list
```

```bash
    deb <http://archive.debian.org/debian/> jessie main contrib non-free
    # deb-src <http://archive.debian.org/debian/> jessie main
    deb <http://mirrordirector.raspbian.org/raspbian/> jessie main contrib non-f$
    # Uncomment line below then 'apt-get update' to enable 'apt-get source'
    # deb-src <http://archive.raspbian.org/raspbian/> jessie main contrib non-fre$
    deb <http://security.debian.org/> jessie/updates main contrib non-free
    # deb-src <http://security.debian.org/> jessie/updates main contrib non-free
    deb <http://ftp.de.debian.org/debian/> jessie main contrib non-free
    #deb-src <http://ftp.de.debian.org/debian/> jessie main contrib non-free
```

```bash
    $ sudo apt-get update && sudo apt-get upgrade && sudo apt-get dist-upgrade
    $ sudo apt-get install libgazebo7-dev
    $ sudo apt-get install ros-kinetic-gazebo7-ros-pkgs

    catkin_make --only-pkg-with-deps waterlinked-a50-ros-driver
```

solving NO_PUBKEY during apt-get update

```bash
    gpg --recv-keys AA8E81B4331F7F50
    gpg --export AA8E81B4331F7F50| apt-key add -

wget <http://mirrordirector.raspbian.org/raspbian/pool/main/h/hdf5/hdf5-helpers_1.8.13+docs-15+deb8u1_armhf.deb>
$ sudo dpkg -i hdf5-helpers_1.8.13+docs-15+deb8u1_armhf.deb
$ sudo apt-get install libgazebo7-dev

git commit -m
git push
```

## 31-05-2021

### Error: When `catkin_make`freezes the pi

Recover by: ctrl+c
Potential solution, try again using

```bash
        catkin_make -j1 #single-thread compilation
```

or

```bash
        catkin_make -j2 #double-thread compilation
```

or

```bash
        catkin_make -j4 -l4 #quadruple-thread compilation
```

### Error

Fix:

```bash
        sudo nano /usr/include/tbb/machine/gcc_armv7.h
```

go to line 38
original:  

```bash
    38 #if !(__ARM_ARCH_7A__)
    39 #error compilation requires an ARMv7-a architecture.   
    40 #endif
```

after fix:

```bash
    38 //#if !(__ARM_ARCH_7A__)
    39 //#error compilation requires an ARMv7-a architecture.   
    40 //#endif*/
```

then go to line 64
original:

```bash
    64 #define __TBB_full_memory_fence() __asm__ __volatile__("dmb ish": : :"memo    ry")
```

after fix:

```bash
    64 #define __TBB_full_memory_fence() 0xffff0fa0  // __asm__ __volatile__("dmb ish": : :"memo    ry")
```

reason behind:
    <https://stackoverflow.com/questions/30131032/compile-opencv-with-tbb-on-raspberry-pi-2>
    The alternative for using dmb is to call the Linux kernel __kuser_memory_barrier. The__kuser_memory_barrier helper operation is found in all ARM kernels 2.6.15 and later and provide a way to issue a memory barrier that will work across all ARM arch.__kuser_memory_barrier helper function found at address 0xffff0fa0
QGC: Missing params: 1:COMPASS_PRIMARY

Versions:
    QGC Version: downloaded from BlueROV page. Recommended.
    ArduSub version: 4.1.0 (beta)
Cause:
    Parameter for Compass changed in ArduSub. QGC not caught up with latest change in parameter. However current version of QGC is recommended by BlueROV & am still able to calibrate without the parameter. Not worthy to solve.
Fix: IGNORED!
Pixahawk connection

Gripper: Channel 3

## 01-06-2021

```bash
    roscore
    rosrun waterlinked_a50_ros_driver publisher.py _ip:=192.168.2.95 _do_log_data:=true
    rostopic list -v
```

```bash
Published topics:
    * /rosout [rosgraph_msgs/Log] 1 publisher
    * /rosout_agg [rosgraph_msgs/Log] 1 publisher

Subscribed topics:
    * /rosout [rosgraph_msgs/Log] 1 subscriber
    * /dvl/json_data [std_msgs/String] 1 subscriber
    * /dvl/data [waterlinked_a50_ros_driver/DVL] 1 subscriber
```

how to publish ros message in terminal:

```bash
        rosmsg show waterlinked_a50_ros_driver/DVL
```

```bash
    std_msgs/Header header
        uint32 seq
        time stamp
        string frame_id
    float64 time
    geometry_msgs/Vector3 velocity
        float64 x
        float64 y
        float64 z
    float64 fom
    float64 altitude
    waterlinked_a50_ros_driver/DVLBeam[] beams
        int64 id
        float64 velocity
        float64 distance
        float64 rssi
        float64 nsd
        bool valid
    bool velocity_valid
    int64 status
    string form
```

```bash
    ### standard package installation procedure
    rosdep update
    cd ~/catkin_ws/src
    wstool merge https://raw.github.com/knowrob/knowrob/master/rosinstall/knowrob-base.rosinstall
    wstool update
    rosdep install --ignore-src --from-paths .
    cd ~/catkin_ws
    catkin_make
```

Replace "<package-name>" with the name of desired package (including the brackets!). It may be a subpackage so search for it first:
go to \<http://wiki.ros.org/>><package-name>?distro=kinetic\

```bash
    cd ~/catkin_ws/src
    git clone -b kinetic-devel <https://github.com/ros/><package-name>.git
    cd ~/catkin_ws
    wstool update -j4 -t src #Alternative: wstool update
    rosdep install -y --from-paths src --ignore-src --rosdistro kinetic -r --os=debian:jessie #Alternative: rosdep install --from-paths . --ignore-src --rosdistro kinetic -y
```

### installation with wstool

```bash
    cd ~/gilbreth_ws/src
    wstool init . #create local .rosinstall file
    wstool merge https://raw.githubusercontent.com/swri-robotics/gilbreth/kinetic-devel/gilbreth.rosinstall #merge .rosinstall file to local
    wstool update #download the file
```

content of an example .rosinstall file (gilbreth.rosinstall):

```bash
    - git:
        local-name: gilbreth
        uri: <https://github.com/swri-robotics/gilbreth.git>
        version: kinetic-devel
    - git:
        local-name: universal_robot
        uri: <https://github.com/ros-industrial/universal_robot.git>
        version: kinetic-devel
```

### list all installed ROS packages

```bash
    rospack list-names
```

### make full system back up

```bash
    lsblk #check if backup disk plugged in and mountable
    $ sudo mount /dev/sdb1 /media/usb
    lsblk #check if backup disk mounted correctly
    $ sudo tar cvfpz /media/usb/backup-companion-20210601.tar.gz --exclude=/backup.tar.gz --exclude=/media/usb --one-file-system /
        command dissection: 
            tar - is the command that creates the archive. It is modified by each letter immediately following, each is explained bellow.
                c - create a new backup archive.
                v - verbose mode, tar will print what it's doing to the screen.
                p - preserves the permissions of the files put in the archive for restoration later.
                z - compress the backup file with 'gzip' to make it smaller.
                f <filename> - specifies where to store the backup, backup.tar.gz is the filename used in this example. It will be stored in the current working directory, the one you set when you used the cd command.
            --exclude=/backup.tar.gz --exclude=/media/usb: exclude existing backups
            --one-file-system: only backup files in one filesystem, excludes unnecessary files
            /: backup root directory
```

### to restore the backup

```bash
$ sudo mount /dev/sdb1 /media/usb
lsblk #check if backup disk mounted correctly
tar xvfpz /media/usb/backup-companion-20210601.tar.gz
```

### Successful backup at 18:30 01-06-2021

## 02-06-2021

### problem with joystick

problem one: unable to control camera after a few working commands (camera stops moving or turning up/down erratically that only stopped when powered off)
problem two: QGC does not recognize joystick in X mode. (Newton SeaGripper Wiki recommends to use X mode). Does it matter?
    Changed Joystick button assignment:
        First use Joystick show, detect LT & RT are button 7&8.
        Go to Vehicle setting -> Joystick -> Button Assignment
            original:
                7: mount_center Disabled
                8: input_hold_set Disabled
                9: mount_tilt_down mount_pan_left
                10: mount_tilt_up mount_pan_right
            New:
                7: mount_center Disabled
                8: input_hold_set Disabled
                9: servo_3_min_momentary mount_tilt_down
                10: servo_3_max_momentary mount_tilt_up
            It worked!
        but "shifting" the buttons via the center button (with Logitech Logo) only works in Windows.
    <https://aws1.discourse-cdn.com/business5/uploads/bluerobotics/original/2X/7/70cd1c739db137407c58c29e6418d7089eadbc42.jpg>
Notes on X (XInputMode):
    In XInput mode, the gamepad uses standard Windows XInput gamepad drivers. It is not necessary to install the included software CD unless you will be using the gamepad in DirectInput mode.

---

## good resources for ROS kinetic

[Industrial ROS Kinetic Trainer](https://industrial-training-master.readthedocs.io/en/kinetic/)

### Good to know: How to NOT build package(s)

"List of ';' separated packages to build"

```bash
    `catkin_make`-DCATKIN_BLACKLIST_PACKAGES="foo;bar"
```

---

## Status as of 29-05-2021 16:00

`catkin_make` succeeds without errors.

---

## Status as of 26-05-2021 15:11

Current status of the companion pi: the currently installed version is ros-kinetic_comms-wet, a bare bone installation with basic communication functions. The active workspace is catkin_ws. `catkin_make`returns no error in its current state. A disk mirror of /home/pi has been made on 26-05-2021 15:11.
Also a note on installing packages, Debian Jessie is End-of-Life and build farm support has been turned off, so apt-get commands won't work on the companion pi and all ros packages need to be installed from source (awful lot of work).
The currently installed 108 packages are:
        -- ~~  - genmsg
        -- ~~  - gencpp
        -- ~~  - geneus
        -- ~~  - genlisp
        -- ~~  - gennodejs
        -- ~~  - genpy
        -- ~~  - cmake_modules
        -- ~~  - cpp_common
        -- ~~  - image_common (metapackage)
        -- ~~  - image_transport_plugins (metapackage)
        -- ~~  - joint_state_publisher_gui
        -- ~~  - joystick_drivers (metapackage)
        -- ~~  - message_generation
        -- ~~  - message_runtime
        -- ~~  - mk
        -- ~~  - ros (metapackage)
        -- ~~  - ros_comm (metapackage)
        -- ~~  - ros_control (metapackage)
        -- ~~  - ros_controllers (metapackage)
        -- ~~  - ros_environment
        -- ~~  - rosbash
        -- ~~  - rosboost_cfg
        -- ~~  - rosbuild
        -- ~~  - rosclean
        -- ~~  - roscpp_traits
        -- ~~  - roscreate
        -- ~~  - rosgraph
        -- ~~  - roslang
        -- ~~  - roslisp
        -- ~~  - rosmake
        -- ~~  - rosmaster
        -- ~~  - rospack
        -- ~~  - roslib
        -- ~~  - rosparam
        -- ~~  - rospy
        -- ~~  - rosservice
        -- ~~  - rostime
        -- ~~  - roscpp_serialization
        -- ~~  - roslaunch
        -- ~~  - rosunit
        -- ~~  - libmavconn
        -- ~~  - rosconsole
        -- ~~  - roslz4
        -- ~~  - rosbag_storage
        -- ~~  - rostest
        -- ~~  - rqt_controller_manager
        -- ~~  - rqt_joint_trajectory_controller
        -- ~~  - std_msgs
        -- ~~  - controller_manager_msgs
        -- ~~  - rosgraph_msgs
        -- ~~  - rosmsg
        -- ~~  - std_srvs
        -- ~~  - control_msgs
        -- ~~  - urdf_parser_plugin
        -- ~~  - xmlrpcpp
        -- ~~  - roscpp
        -- ~~  - bluerov_auto
        -- ~~  - realtime_tools
        -- ~~  - rosout
        -- ~~  - hardware_interface
        -- ~~  - combined_robot_hw
        -- ~~  - controller_interface
        -- ~~  - controller_manager
        -- ~~  - force_torque_sensor_controller
        -- ~~  - forward_command_controller
        -- ~~  - four_wheel_steering_controller
        -- ~~  - joint_state_publisher
        -- ~~  - message_filters
        -- ~~  - position_controllers
        -- ~~  - rosnode
        -- ~~  - rostopic
        -- ~~  - roswtf
        -- ~~  - topic_tools
        -- ~~  - rosbag
        -- ~~  - control_toolbox
        -- ~~  - controller_manager_tests
        -- ~~  - combined_robot_hw_tests
        -- ~~  - camera_calibration_parsers
        -- ~~  - image_transport
        -- ~~  - camera_info_manager
        -- ~~  - compressed_depth_image_transport
        -- ~~  - compressed_image_transport
        -- ~~  - cv_camera
        -- ~~  - imu_sensor_controller
        -- ~~  - joint_state_controller
        -- ~~  - joy
        -- ~~  - bluerov_apps
        -- ~~  - mavros_msgs
        -- ~~  - mavros
        -- ~~  - polled_camera
        -- ~~  - ps3joy
        -- ~~  - robot_state_publisher
        -- ~~  - bluerov
        -- ~~  - spacenav_node
        -- ~~  - theora_image_transport
        -- ~~  - transmission_interface
        -- ~~  - urdf
        -- ~~  - diff_drive_controller
        -- ~~  - ackermann_steering_controller
        -- ~~  - effort_controllers
        -- ~~  - gripper_action_controller
        -- ~~  - joint_limits_interface
        -- ~~  - joint_trajectory_controller
        -- ~~  - mavros_extras
        -- ~~  - test_mavros
        -- ~~  - velocity_controllers
        -- ~~  - waterlinked_a50_ros_driver
        -- ~~  - wiimote
