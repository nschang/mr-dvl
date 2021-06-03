# Marine Robotics Presentation Notes

## What is a DVL:

## DVL Integration and Setup:

### General Setup:

> - The setup of the DVL begins with connecting to the DVL via the Tetherbox.
>   - We first setup the computer's public IP Address to `192.168.2.1` and the subnet mask to `255.255.255.0`. Afterwards, we navigate to the `http://192.168.2.2:2770/waterlinked` webpage to ensure the DVL is properly connected.
>   - Next we need to connect to the raspberry pi using the command `ssh pi@192.168.2.2` using `companion` as the password.
>   - Then we run the following command `rosrun waterlinked_a50_ros_driver subscriber_gui.py` to ensure that everything is up and running.
>   - Finally to run the publisher we use the following command `rosrun waterlinked_a50_ros_driver publisher.py _ip:=192.168.194.95 _do_log_data:=true` that listens to the TCP port, displays the raw data in the terminal, and sends the data to ROS.

**Data Output:**
Note: (insert table from page 5 from google docs)

### Issues encountered:

**General Issues**
> - Overheating issues
>   - Solved by submerging it into a liquid or using a floor fan to cool it down

> - Integrating the DVL with Raspberry Pi

> - How to read data from the DVL?

> - Mounting the DVL:
>   - Obtained a metal bracket and mounted the DVL underneath the Robot from the back side

**Hardware change log**

> - Added insulation to LAN switch
> - (insert images from page 7 in google docs)

**Hardware Issues**

> - Bad calibration:
>   - (Include image from page 7)
> - BATTERY CHECK LOW VOLTAGE
>   - Fixed by avoiding undercharging and overcharging the battery, not below 12V and not above 16.8V
>   - Since the BLUEROV2 does not have a low voltage cutoff, we should monitor the battery levels manually.

**Installation of additional tools**

> - `sudo apt-get install python-catkin-tools`
> - Issue: make package stuck at 91% (maybe low RAM)
>   - Fixed: Increasing SWAP SIZE from 100MB to 1024MB.
> - Built as a single package including its dependencies with `catkin_make --only-pkg-with-deps <target_package>`, then switched to `catkin_make -DCATKIN_WHITELIST_PACKAGES=""` to build remaining packages



#### Troubleshooting

### DVL LAN connection Setup

## Ability Demonstration:


## References:

**Ref: general setup**
> - Waterlinked DVL-A50 Wiki https://waterlinked.github.io/dvl/dvl-a50-details/
> - BlueROV dvl-a50 integration https://waterlinked.github.io/docs/dvl/bluerov-integration/
> - DVL Protocol https://waterlinked.github.io/docs/dvl/dvl-protocol/
> - DVL ROS package https://github.com/waterlinked/dvl-a50-ros-driver
> - BlueRobotics software setup https://bluerobotics.com/learn/bluerov2-software-setup/#testing-the-connection-to-the-embluerov2em
