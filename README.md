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

For more detils on reading DVL data, go to dvl-data.

## Reading Data from DVL (json)

One can read data from DVL in the following ways: 

### json format:

read data in terminal:
  `nc 192.168.2.95 16171`

read data and save to text file:
  `nc 192.168.2.95 16171 > out.txt`
  
The output of a single TCP report will look like this: 
#### Example of TCP report. (invalid)

```
{"time":126.04666137695312,"vx":0.009757072664797306,"vy":0.002016076585277915,"vz":-0.0002864645794034004,"fom":0.00016600292292423546,"altitude":1.8710078001022339,
"transducers":[{"id":0,"velocity":-0.002349231392145157,"distance":2.041400194168091,"rssi":30.97574806213379,"nsd":19.906816482543945,"beam_valid":true},
{"id":1,"velocity":-0.0034521492198109627,"distance":1.9942001104354858,"rssi":38.42657470703125,"nsd":30.263219833374023,"beam_valid":true},
{"id":2,"velocity":0.00183798186480999,"distance":2.0886001586914062,"rssi":31.835567474365234,"nsd":22.06237030029297,"beam_valid":true},
{"id":3,"velocity":0.0029014351312071085,"distance":2.041400194168091,"rssi":37.41792678833008,"nsd":23.786922454833984,"beam_valid":true}],"velocity_valid":true,"status":0,"format":"json_v1"}
```
for more info on the format and meaning of variables, go to [dvl-a50-data](/dvl-a50-data)

### dvl-python
In companion, type:
```
  $ ping 192.168.2.2
  $ ssh pi@192.168.2.2
  $ cd ~/dvl-python
```

## DVL ROS messages

**To run the publisher that listens to the TCP port and sends the data to ROS**

```bash
rosrun waterlinked_a50_ros_driver publisher.py _ip:=TCP_IP
```

where TCP_IP should be replaced by the IP of the DVL. You can also display the raw DVL data in the terminal by specifying the argument "do_log_data":

**To run the publisher that listens to the TCP port, displays the raw data in the DVL and sends the data to ROS**

```bash
rosrun waterlinked_a50_ros_driver publisher.py _ip:=TCP_IP _do_log_data:=true
```

**To run a subscriber node that listens to the DVL topic.**

Helpful for debugging or checking if everything is running as it should be. Choose between "subscriber_gui.py" and "subscriber.py". The GUI makes reading data visually much easier. While the non-GUI version makes it easier to read through the code to see how you can implement code yourself.

```bash
rosrun waterlinked_a50_ros_driver subscriber_gui.py
```

for more info on DVL ROS setup, go to [dvl-a50-ros](/dvl-a50-ros)

## Operation within QGC

### For Position Hold (UNDER CONSTRUCTION)

  1. go to http://192.168.2.2:2770/waterlinked
  2. the "Status" field in the Waterlinked page should read Running.... 
  3. QGC will announce "EKF3 IMU0 STARTED RELATIVE AIDING" and then "EKF3 IMU0 FUSING ODOMETRY" (This means the DVL input is being fused.)
  4. switch to POSHOLD mode

### For Deadreckoning (track ROV position in QGC map view): (UNDER CONSTRUCTION)
The DVL data is sent by the VISION_POSITION_DELTA [mavlink message](https://mavlink.io/en/messages/ardupilotmega.html#VISION_POSITION_DELTA).

1. go to http://192.168.2.2:2770/waterlinked
2. place the pin in the starting position
3. click `Set New Origin`.

## Nice guides to follow
[BlueROV2 Software Setup](https://bluerobotics.com/learn/bluerov2-software-setup/#update-software)

***

Contributor: 
**Si-yuan Chang** and **Alex Tretyakov**.

This repository is in part coursework of CA-RIS-801 Marine Robotics in Spring 2021, Supervisor: Prof. Francesco Maurelli, Jacobs University Bremen. 
