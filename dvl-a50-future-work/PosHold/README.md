# Position Hold Function

This work is currently based on [this tutorial for position hold using NortekDVL](https://discuss.ardupilot.org/t/rov-position-hold-and-non-gps-navigation/53131).

ArduPilot has a series of libraries available for non-GPS navigation. The two libraries that were most applicable for integrating a DVL are Optical Flow and Visual Odometry. The [Visual Odom Library](https://github.com/ArduPilot/ardupilot/tree/master/libraries/AP_VisualOdom) was chosen by the BlueROV team.

The Nortek DVL was tested for position hold using this [python code](https://github.com/Williangalvani/ardupilot/blob/e1d009555e7cadaf69c1d901e5b5ef5fc4b5c3ca/nortekdvl.py), written by the Blue Robotics software team to convert the position sentences from the DVL into the `VISION_POSITION_DELTA` messages that could be interpreted by the ArduSub firmware. 
