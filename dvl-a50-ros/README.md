# Water Linked DVL A50 - ROS Set Up

A repo cloned from [dvl-a50-ros-driver](https://github.com/nschang/dvl-a50-ros-driver) by Water Linked is available at [dvl-a50-ros-driver](/dvl-a50-ros/dvl-a50-ros-driver). Modified for local deployment. 

### Prerequisites
The package has been tested with ROS Kinetic and is working. The package is coded in Python for easier readability, as such you would need to have Python installed. Preferably **Python 2.7** as some distros of ROS doesn't support Python 3.

## Installation
In our catkin workspace `~/catkin_ws`, input the following command:
```bash
cd ~/catkin_ws/src
git clone -b master https://github.com/waterlinked/dvl-a50-ros-driver.git
cd ~/catkin_ws
catkin_make
```

### Usage

The DVL's IP address has been set to `192.168.2.95`. The DVL ROS package and it's components can be run by following these steps:

**To run the publisher that listens to the TCP port and sends the data to ROS**

```bash
  rosrun waterlinked_a50_ros_driver publisher.py _ip:=192.168.2.95
```
where _IP is replaced by the IP of the DVL, in our case `192.168.2.95`. You can also display the raw DVL data in the terminal by specifying the argument "do_log_data":

**To run the publisher that listens to the TCP port, displays the raw data in the DVL and sends the data to ROS**

```bash
  rosrun waterlinked_a50_ros_driver publisher.py _ip:=192.168.2.95 _do_log_data:=true
```

**To run a subscriber node that listens to the DVL topic.**
Helpful for debugging or checking if everything is running as it should be. Choose between "subscriber_gui.py" and "subscriber.py". The non-GUI version is used here due to limitations of companion pi with commandline interface. 

```bash
  rosrun waterlinked_a50_ros_driver subscriber.py
```
**Alternatively, publish DVL message as ROS message:**
```bash
  $ roscore
  # for server node:
  $ rosrun comm_tcp server_node 16171
  # for client node:
  $ rosrun comm_tcp client_node 192.168.2.95 16171
```
**The published ROS message looks like this:**

```
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
  int64 id    #Transducer ID
  float64 velocity    #Velocity reported by transducer
  float64 distance    #Distance value
  float64 rssi    #RSSI
  float64 nsd    #NSD
  bool valid    #Report if beam is locked on and providing reliable data
  bool velocity_valid
int64 status
string form
```

## The DVL-A50 ROS node
The DVL ROS node publishes data to the topics: "*dvl/json_data*" and "*dvl/data*".
* *dvl/json_data*: uses a simple String formated topic that publishes the raw json data coming from the DVL.
* *dvl/data*: Uses a custom message type that structures the parsed data following our protocol. Read more about the protocol here: [DVL Protocol](https://waterlinked.github.io/docs/dvl/dvl-protocol/)

![rqt_graph of the package in action](https://raw.githubusercontent.com/waterlinked/dvl-a50-ros-driver/master/img/a50_graph.png?raw=true "Graph of the package's node-to-node structure")

*The graph illustrates the topics and nodes created when the package is run.*

## Good learning resources

[Understanding ROS Nodes](https://wiki.ros.org/ROS/Tutorials/UnderstandingNodes)
[ROS Nodes_ROS Wiki](https://wiki.ros.org/Nodes)

