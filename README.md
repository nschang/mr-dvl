# WaterLinked DVL A50 wiki

Integration instruction and operation guides of WaterLinked DVL A50 with BlueROV2, by Siyuan Chang and Alex Tretyakov.

This is the repository of the DVL group for the course CA-RIS-801 Marine Robotics in Spring 2021 by Prof. Francesco Maurelli of Jacobs University Bremen. The file 'dvl-history.md' is the main working doc.

## introduction

DVL - Doppler Velocity Logger - is a Hydro-acoustic unit which uses acoustic beams to measure distance to bottom surface and the velocity which the unit is moving across the surface. The DVL estimates velocity relative to the sea bottom by sending acoustic waves from the four angled transducers and then measure the frequency shift (doppler effect) from the received echo. By combining the measurements of all four transducers and the time between each acoustic pulse, it is able to accurately estimate the speed and direction of the movement of vehicle.

## Status Check

### LED Signals

* No green light: Power is off.

* Flashing green light (slow): DVL loocking for bottom lock.

* Fixed green light: DVL has bottom lock. The LED is mostly on and blinks quickly to show we are alive.

# DVL A50 Quick Start

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

## ways to communicate with DVL and read data

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
