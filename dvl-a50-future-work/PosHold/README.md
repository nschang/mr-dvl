# Position Hold Function (BETA)

This work is currently based on [this tutorial for position hold using NortekDVL](https://discuss.ardupilot.org/t/rov-position-hold-and-non-gps-navigation/53131) and [ArduSub DVL integration (BETA)](https://www.ardusub.com/developers/dvl-integration.html). A DVL integration plan to ArduSub Navigation is currently under active develooment (As of 03 June 2021, latest commit in May 2021) in a BETA stage and is currently unsupported for normal use. The Waterlinked A50 DVL is the only supported DVL at the moment in the beta integration.

ArduPilot has a series of libraries available for non-GPS navigation. The two libraries that were most applicable for integrating a DVL are Optical Flow and Visual Odometry. The [Visual Odom Library](https://github.com/ArduPilot/ardupilot/tree/master/libraries/AP_VisualOdom) was chosen by the BlueROV team.

The Nortek DVL was tested for position hold using this [python code](https://github.com/Williangalvani/ardupilot/blob/e1d009555e7cadaf69c1d901e5b5ef5fc4b5c3ca/nortekdvl.py), written by the Blue Robotics software team to convert the position sentences from the DVL into the `VISION_POSITION_DELTA` messages that could be interpreted by the ArduSub firmware. 



**[VISION_POSITION_DELTA](https://mavlink.io/en/messages/ardupilotmega.html#VISION_POSITION_DELTA)**

|    Field Name   |   Type   | Units |                                                            Description                                                           |
|:---------------:|:--------:|:-----:|:--------------------------------------------------------------------------------------------------------------------------------:|
| time_usec       | uint64_t | us    | Timestamp (synced to UNIX time or since system boot).                                                                            |
| time_delta_usec | uint64_t | us    | Time since the last reported camera frame.                                                                                       |
| angle_delta     | float[3] | rad   | Defines a rotation vector [roll, pitch, yaw] to the current MAV_FRAME_BODY_FRD from the previous MAV_FRAME_BODY_FRD.             |
| position_delta  | float[3] | m     | Change in position to the current MAV_FRAME_BODY_FRD from the previous FRAME_BODY_FRD rotated to the current MAV_FRAME_BODY_FRD. |
| confidence      | float    | %     | Normalised confidence value from 0 to 100.                                                                                       |

***Useful Links***

[SITL Software in the Loop Simulator](https://ardupilot.org/dev/docs/sitl-simulator-software-in-the-loop.html#sitl-simulator-software-in-the-loop)
[BlueROV Forum: Setting up a simulation environment](https://discuss.bluerobotics.com/t/setting-up-a-simulation-environment/2892)

*Gazebo*
[Installing gazebo_ros_pkgs (ROS 1)](http://gazebosim.org/tutorials?tut=ros_installing&cat=connect_ros)
[Install Gazebo on Linux distributions (non Ubuntu)](http://gazebosim.org/tutorials?tut=install_other_linux&cat=install)
[freebuoyancy_gazebo](https://github.com/nschang/freebuoyancy_gazebo)

[ArduPilot Drone Setup for Non-GPS Navigation](https://ardupilot.org/copter/docs/common-zed.html) probably unrelated but maybe helpful.

