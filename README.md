# WaterLinked DVL A50 Integration

## Content: 
The file 'dvl-history.md' is the main working doc.

- dvl-a50-data: contains DVL output in various settings of vehicle (json format only)
- dvl-a50-ros: ROS-related setup and output of dvl-a50
- Communication Protocol: Settings for communication with DVL

for Hardware integration, as well as other introductory content, please refer to [Wiki](https://github.com/nschang/mr-dvl/wiki) of this repo.

## Waterlinked DVL A50 - Quick Start

Follow the steps required to start using DVL with BlueROV: 
- Hardware integration
- Connection
  - DVL 8-pin connection
  - Connect landside terminal to BlueROV through Fathom-X Tether Interface (FXTI)
  - Make Sure BlueROV is set up correctly and running
- Software Setup
- Status Check
  - DVL LED: flashing (loonking for bottomlock) or fixed green light (bottomlocked)
  - when running dry on land, it is recommended to add additional cooling to DVL by water or wind.


In browser, go to 192.168.2.95, check for active data or error messages
In another tab, go to 192.168,2.2:2770/waterlinked for management page.
### web interface

dvl web-GUI: http://192.168.2.95/#/

dvl setting: http://192.168.2.2:2770/waterlinked

| Control Variables    | description                                                                 |
| ------------ | --------------------------------------------------------------------------- |
| Driver Enable | Enables or disables the DVL Driver | 
| DVL IP Address | Inital IP where the driver will try to find the DVL. The driver always attempts to find Waterlinked-dvl.local (mDNS hostname) in the local network, if that fails, it falls back to the IP address in this field. | 
| Orientation | Orientation of the DVL: Down is the suggested use, where the DVL points to the ocean floor, Forward is an untested mode where the dvl is mounted forward to lock position to a vertical surface in front of it. | 
| Use as Rangefinder | Allows the DVL to be used as a rangefinder (shown in QGC). | 
| Restart DVL Service | Restarts the DVL service. | 
| Set New Origin | Used for dead reckoning; this makes the ROV show up in QGC at the selected location. The position displayed in QGC is calculated by the autopilot independently of the position showed in the Water Linked DVL web interface, which is calculated by the DVL itself. | 
| Status | Shows the current Driver status, useful for troubleshooting. | 

For more detils on reading DVL data, go to dvl-data.

### nc

### dvl-python
In Terminal, 
ping 192.168.2.2
ssh pi@192.168.2.2
cd ~/dvl-python

## Operation within QGC

### For Position Hold

  1. go to http://192.168.2.2:2770/waterlinked
  2. the "Status" field in the Waterlinked page should read Running.... 
  3. QGC will announce "EKF3 IMU0 STARTED RELATIVE AIDING" and then "EKF3 IMU0 FUSING ODOMETRY" (This means the DVL input is being fused.)
  4. switch to POSHOLD mode

### For Deadreckoning (track ROV position in QGC map view): The DVL data is sent by the VISION_POSITION_DELTA mavlink message <https://mavlink.io/en/messages/ardupilotmega.html#VISION_POSITION_DELTA>

  1. go to http://192.168.2.2:2770/waterlinked 
  2. place the pin in the starting position
  3. click "Set New Origin".


Contributor: 
**Si-yuan Chang** and **Alex Tretyakov**.

This repository is in part coursework of CA-RIS-801 Marine Robotics in Spring 2021, Supervisor: Prof. Francesco Maurelli, Jacobs University Bremen. 
