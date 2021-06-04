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

You can also display the raw DVL data in the terminal by specifying the argument "do_log_data":

**To run the publisher that listens to the TCP port, displays the raw data in the DVL and sends the data to ROS**
```bash
rosrun waterlinked_a50_ros_driver publisher.py _ip:=192.168.2.95 _do_log_data:=true
```

**To run a subscriber node that listens to the DVL topic. Helpful for debugging or checking if everything is running as it should be. Choose between "subscriber_gui.py" and "subscriber.py". The GUI makes reading data visually much easier. While the non-GUI version makes it easier to read through the code to see how you can implement code yourself.**
```bash
rosrun waterlinked_a50_ros_driver subscriber_gui.py
```
![GUI Subscriber](img/a50_gui.png?raw=true "Interface as seen when running the GUI version of the subscriber")

## Documentation
The node publishes data to the topics: "*dvl/json_data*" and "*dvl/data*".
* *dvl/json_data*: uses a simple String formated topic that publishes the raw json data coming from the DVL.
* *dvl/data*: Uses a custom message type that structures the parsed data following our protocol. Read more about the protocol here: [DVL Protocol](https://waterlinked.github.io/docs/dvl/dvl-protocol/)

![rqt_graph of the package in action](img/a50_graph.png?raw=true "Graph of the package's node-to-node structure")

*The graph illustrates the topics and nodes created when the package is run.*
