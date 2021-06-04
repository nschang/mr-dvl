# Position Hold Function (BETA)

This work is currently based on . A DVL integration plan to ArduSub Navigation is currently under active develooment (As of 03 June 2021, latest commit in May 2021) in a BETA stage and is currently unsupported for normal use. The Waterlinked A50 DVL is the only supported DVL at the moment in the beta integration.

There are current two documented attempts by the BlueROV team to achieve this. 

1. [**Successful position hold of BlueROV using a NortekDVL**](https://discuss.ardupilot.org/t/rov-position-hold-and-non-gps-navigation/53131)
2. [**ArduSub DVL integration (BETA)**](https://www.ardusub.com/developers/dvl-integration.html) which only works for Waterlinked A50 DVL (as of 04 June 2021).

## position hold of BlueROV using a NortekDVL
ArduPilot has a series of libraries available for non-GPS navigation. The two libraries that were most applicable for integrating a DVL are Optical Flow and Visual Odometry. The [Visual Odom Library](https://github.com/ArduPilot/ardupilot/tree/master/libraries/AP_VisualOdom) was chosen by the BlueROV team.
The Nortek DVL was tested for position hold using this [python code](https://github.com/Williangalvani/ardupilot/blob/e1d009555e7cadaf69c1d901e5b5ef5fc4b5c3ca/nortekdvl.py), written by the Blue Robotics software team to convert the position sentences from the DVL into the `VISION_POSITION_DELTA` messages that could be interpreted by the ArduSub firmware. 

## ArduSub Firmware

The DVL data is sent by the [VISION_POSITION_DELTA](https://mavlink.io/en/messages/ardupilotmega.html#VISION_POSITION_DELTA) mavlink message. This message is supported in ArduSub 4.1-beta and onward. The ArduSub 4.1-beta firmware is recommended for testing with DVL.

## Installing the ArduSub beta firmware

Click **beta** under the update pixhawk section in the [System Page](<http://192.168.2.2:2770/system>).

**[VISION_POSITION_DELTA](https://mavlink.io/en/messages/ardupilotmega.html#VISION_POSITION_DELTA)**

|    Field Name   |   Type   | Units |                                                            Description                                                           |
|:---------------:|:--------:|:-----:|:--------------------------------------------------------------------------------------------------------------------------------:|
| time_usec       | uint64_t | us    | Timestamp (synced to UNIX time or since system boot).                                                                            |
| time_delta_usec | uint64_t | us    | Time since the last reported camera frame.                                                                                       |
| angle_delta     | float[3] | rad   | Defines a rotation vector [roll, pitch, yaw] to the current MAV_FRAME_BODY_FRD from the previous MAV_FRAME_BODY_FRD.             |
| position_delta  | float[3] | m     | Change in position to the current MAV_FRAME_BODY_FRD from the previous FRAME_BODY_FRD rotated to the current MAV_FRAME_BODY_FRD. |
| confidence      | float    | %     | Normalised confidence value from 0 to 100.                                                                                       |

## PIDs and Other Important Parameters

According to the [ArduSub Doc](https://www.ardusub.com/developers/dvl-integration.html), These parameters **must** be configured to the recommended values for DVL integration to perform correctly:

Use mavlink2:
```
SERIAL0_PROTOCOL 2
```

Use EKF3 for the odometry support
```
EK3_ENABLE 1
AHRS_EKF_TYPE 3
EK2_ENABLE 0
```
Enable odometry support
```
VISO_TYPE 1
```
Disable external gps in ekf3
```
EK3_GPS_TYPE 3
```
Suggested gains
```
PSC_POSXY_P 2.5
PSC_POSZ_P  1.0
PSC_VELXY_D 0.8
PSC_VELXY_I 0.5
PSC_VELXY_P 5.0
PSC_VELZ_P  5.0
```

## Operation

After successful setup of everything suggested in [ArduSub Doc](https://www.ardusub.com/developers/dvl-integration.html), the following functions should have been enabled. However, there is so far no success yet, as we are unable to toggle the `Position Hold` Mode in QGroundContorl. The QGC Version used is latest provided by BlueROV at [here](https://s3.amazonaws.com/downloads.bluerobotics.com/QGC/latest/QGroundControl.dmg) or [here](https://discuss.bluerobotics.com/t/software-updates/1128)

### Position Hold (NO SUCCESSFUL ATTEMPT YET)

After setup, reboot the ROV. The *Status* field in the Waterlinked page should read **Running...**. The QGC will announce "EKF3 IMU0 STARTED RELATIVE AIDING" and then "EKF3 IMU0 FUSING ODOMETRY". This means the DVL input is being fused and you can switch to POSHOLD mode to use it.

### Tracking on map (NO SUCCESSFUL ATTEMPT YET)

To track the ROV position in the QGC map view, go to the Waterlinked page, place the pin in the starting position, and click "Set New Origin".

***Useful Links***

[SITL Software in the Loop Simulator](https://ardupilot.org/dev/docs/sitl-simulator-software-in-the-loop.html#sitl-simulator-software-in-the-loop)
[BlueROV Forum: Setting up a simulation environment](https://discuss.bluerobotics.com/t/setting-up-a-simulation-environment/2892)

*Gazebo*
[Installing gazebo_ros_pkgs (ROS 1)](http://gazebosim.org/tutorials?tut=ros_installing&cat=connect_ros)
[Install Gazebo on Linux distributions (non Ubuntu)](http://gazebosim.org/tutorials?tut=install_other_linux&cat=install)
[freebuoyancy_gazebo](https://github.com/nschang/freebuoyancy_gazebo)

[ArduPilot Drone Setup for Non-GPS Navigation](https://ardupilot.org/copter/docs/common-zed.html) probably unrelated but maybe helpful.

