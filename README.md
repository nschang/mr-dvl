# BlueROV DVL wiki

Integration instruction and operation guides of WaterLinked DVL A50 with BlueROV2, by Siyuan Chang and Alex Tretyakov.

This is the repository of the DVL group for the course CA-RIS-801 Marine Robotics in Spring 2021 by Prof. Francesco Maurelli of Jacobs University Bremen. The file 'dvl-history.md' is the main working doc.

# BlueROV DVL wiki

This is the repository of the DVL group for Marine Robotics at Jacobs University. The file 'dvl-history.md' is our main working doc.

## introduction

DVL - Doppler Velocity Logger - is a Hydro-acoustic unit which uses acoustic beams to measure distance to bottom surface and the velocity which the unit is moving across the surface.

The DVL estimates velocity relative to the sea bottom by sending acoustic waves from the four angled transducers and then measure the frequency shift (doppler effect) from the received echo. By combining the measurements of all four transducers and the time between each acoustic pulse, it is possible to very accurately estimate the speed and direction of movement.

## DVL A50 Specs: 
range: 0.05-50 m

## Hardware

### LED Signals

* No green light: Power is off.

* Flashing green light (slow): DVL loocking for bottom lock.

* Fixed green light: DVL has bottom lock. The LED is mostly on and blinks quickly to show we are alive.

## DVL data

## Serial Protocol

The serial communication format is 115200 8-N-1 (no hardware flow control).

Packets sent to and received from the DVL start with a `w` and end with LF or CR+LF. The packet format is:

| Start byte | Direction        | Command  | Options (0 to many)  | Checksum | End byte       |
|------------|------------------|----------|----------------------|----------|----------------|
| `w`        | `c` or `r`       | `x`      | `,[option]`          | `*xx`    | `\n` or `\r\n` |

Direction is command (`c`) for commands issued to the DVL and the DVL replies with direction set to response (`r`).
The commands can be sent as a string or entered one char at a time from a terminal.

The protocol can support Water Linked DVLs with different feature sets.
The DVL always returns a checksum. The checksum algorithm is CRC-8 and it is formatted as a hexadecimal number using 2 lower-case charaters (ex: `*c3`).

### Commands

Commands in the table are shown **without** the checksum for readability.

| Command | Description | Response | Description |
|---------|-------------|----------|-------------|
| `wcv`   | Get protocol version | `wrv,`*[major],[minor],[patch]* | Protocol version. eg: `wrv,2.1.0` |
| `wcw`   | Get product detail | `wrw,`*[name]*,*[version]*,*[chipID]*,*[IP address]* | Where type is dvl, name is product name, version is software version, chip ID is the chip ID and _optionally_ the IP address if connected to DHCP server: eg: `wrw,dvl-a50,1.4.0,0xfedcba98765432` or `wrw,dvl-a50,1.4.0,0xfedcba98765432,10.11.12.140` |
|         |             |          |              |
|         |             | `wrx,`*[details below]* | Velocities measured. See details below |
|         |             | `wr?` | Malformed request: Response when packet cannot be understood |
|         |             | `wr!` | Malformed request: Packet does not match the given checksum |
#### Ethernet protocol (TCP)
The DVL supports sending velocity updates using the Transmission Control Protocol (TCP). The DVL runs a TCP server on port 16171.
Each packet sent contains a velocity report from the DVL on JSON format.
##### Velocity Report
Velocity report is outputted after each measurement has been completed. The expected update rate varies depending on the altitude and will be in the range is from 2-26 Hz. The X, Y, Z axis are oriented according to the marking on the DVL. The messages are delimited by newline. 
The velocities measured response is on the following format:
`wrx,[time],[vx],[vy],[vz],[fom],[altitude],[valid],[status]`
`wrx,`*[time],[vx],[vy],[vz],[fom],[altitude],[valid],[status]*

| Variables    | Unit     | description                                                                 |
| ------------ | -------- | --------------------------------------------------------------------------- |
| `time`       | ms       | Milliseconds since last velocity report                                     |
| `vx`         | m/s      | Measured velocity in x direction (m/s)                                      |
| `vy`         | m/s      | Measured velocity in y direction                                            |
| `vz`         | m/s      | Measured velocity in z direction                                            |
| `fom`        | m/s      | Figure of merit, a measure of the accuracy of the measured velocities (m/s) |
| `altitude`   | m        | measurement of the distance between the transducer and the bottom (m)       |
| `valid`      | boolean  | bottomlock and valid altitude and velocities (y/n)                          |
| `status`     |          | 0 for normal operation, 1 for high temperature warning                      |

Velocity report is outputted after each measurement has been completed. The expected update rate varies depending on the altitude and will be in the range is from 2-26 Hz. The X, Y, Z axis are oriented according to the marking on the DVL.

Example where velocities are valid:

```
wrx,112.83,0.007,0.017,0.006,0.000,0.93,y,0*d2
wrx,140.43,0.008,0.021,0.012,0.000,0.92,y,0*b7
wrx,118.47,0.009,0.020,0.013,0.000,0.92,y,0*54
```

Example where velocity and altitude is not valid and high temperature warning is given:

```
wrx,1075.51,0.000,0.000,0.000,2.707,-1.00,n,1*04
wrx,1249.29,0.000,0.000,0.000,2.707,-1.00,n,1*6a
wrx,1164.94,0.000,0.000,0.000,2.707,-1.00,n,1*39
```
### Transducer report

Transducer report is outputted after each measurement has been completed. The expected update rate varies depending on the altitude and will be in the range is from 2-26 Hz.

The distances measured form each transducer is on the following format:
`wrt,`*[dist_1],[dist_2],[dist_3],[dist_4]*

| Variable | Description |
|----------|-------------|
| dist_1 | Measured distance to bottom from trancduser 1 (m) |
| dist_2 | Measured distance to bottom from trancduser 2 (m) |
| dist_3 | Measured distance to bottom from trancduser 3 (m) |
| dist_4 | Measured distance to bottom from trancduser 4 (m) |

Example where velocities are valid:

```
wrt,15.00,15.20,14.90,14.20*b1
wrt,14.90,15.10,14.80,14.10*ac
```

Example where distance is not valid on transducer 4:

```
wrt,14.90,15.10,14.80,-1.00*53
wrt,15.00,15.20,14.90,-1.00*71
```
### Example of TCP report. (valid)
```
{"time":126.04666137695312,"vx":0.009757072664797306,"vy":0.002016076585277915,"vz":-0.0002864645794034004,"fom":0.00016600292292423546,"altitude":1.8710078001022339,
"transducers":[{"id":0,"velocity":-0.002349231392145157,"distance":2.041400194168091,"rssi":30.97574806213379,"nsd":19.906816482543945,"beam_valid":true},
{"id":1,"velocity":-0.0034521492198109627,"distance":1.9942001104354858,"rssi":38.42657470703125,"nsd":30.263219833374023,"beam_valid":true},
{"id":2,"velocity":0.00183798186480999,"distance":2.0886001586914062,"rssi":31.835567474365234,"nsd":22.06237030029297,"beam_valid":true},
{"id":3,"velocity":0.0029014351312071085,"distance":2.041400194168091,"rssi":37.41792678833008,"nsd":23.786922454833984,"beam_valid":true}],"velocity_valid":true,"status":0,"format":"json_v1"}
```
### Example of TCP report. (invalid)
```
{"time":429.1646423339844,"vx":0,"vy":0,"vz":0,"fom":2.661566972732544,"altitude":-1,
"transducers":[{"id":0,"velocity":0,"distance":-1,"rssi":-37.079925537109375,"nsd":25.96938133239746,"beam_valid":false},
{"id":1,"velocity":0,"distance":-1,"rssi":-46.16178894042969,"nsd":19.031171798706055,"beam_valid":false},
{"id":2,"velocity":0,"distance":-1,"rssi":-38.94984436035156,"nsd":25.094785690307617,"beam_valid":false},
{"id":3,"velocity":0,"distance":-1,"rssi":-37.11658477783203,"nsd":26.229419708251953,"beam_valid":false}],"velocity_valid":false,"status":0,"format":"json_v1"}
```

### Example of TCP report. (indented for readability)

```
{
  "time": 170.52674865722656,
  "vx": -0.00563613697886467,
  "vy": -0.007631152402609587,
  "vz": -0.007641898933798075,
  "fom": 0.001959984190762043,
  "altitude": 0.6173566579818726,
  "transducers": [
    {
      "id": 0,
      "velocity": -0.007625679485499859,
      "distance": 0.6769760251045227,
      "rssi": 38.66838836669922,
      "nsd": 18.295578002929688,
      "beam_valid": true
    },
    {
      "id": 1,
      "velocity": -0.0034413286484777927,
      "distance": 0.6769760251045227,
      "rssi": 35.403541564941406,
      "nsd": 19.518909454345703,
      "beam_valid": true
    },
    {
      "id": 2,
      "velocity": -0.006717036943882704,
      "distance": 0.6653040051460266,
      "rssi": 41.03888702392578,
      "nsd": 20.25017738342285,
      "beam_valid": true
    },
    {
      "id": 3,
      "velocity": -0.01045388076454401,
      "distance": 0.6536320447921753,
      "rssi": 31.09071922302246,
      "nsd": 17.366933822631836,
      "beam_valid": true
    }
  ],
  "velocity_valid": true,
  "status": 0,
  "format": "json_v1"
}

```

## ROS Message
WaterLinked provides a package that turns the json output to ROS message. 
