# Future Work on DVL

This folder contains the work currently under development or planned for future on DVL an BlueROV2. 

## Hardware-Side

- ADD Nvidia Jetson Nano to replace RPi as onboard companion SBC. (for vastly greater computing performance and, running as low as 5 watts, equip the ROV with the power of modern AI, including accelerated libraries for deep learning, computer vision, graphics, multimedia, and more. This enables quite a number of interesting applications, such as automatic object identification / obstacle avoidance / real-time 3D mapping / potential for autonomous operations.) See discussion [here](https://discuss.bluerobotics.com/t/proposed-advanced-sub-surface-rov-control-platform/4747).
  
  **Specs comparison of an Jetson Nano with Rpi 3A+ & 3B+**

  <img src="https://miro.medium.com/max/3200/0*wG4G3yRftvRAUxu_.png" width="70%">
  <img src="https://www.maketecheasier.com/assets/uploads/2019/06/jetson-vs-pi-table-3.png" width="70%">
  
  **An example implementation of Nvidia Jetson Nano on the Deepsouth ROV II**
  <img src="https://aws1.discourse-cdn.com/business5/uploads/bluerobotics/optimized/2X/f/f3cc098c1ef960713b3400af177386711821814e_2_690x388.png">

- ADD quick-switch to battery housing to simplify operations with battery. Example here: 

  [High Pressure Waterproof Switch for ROVs by BlueRobotics](https://bluerobotics.com/store/comm-control-power/switch/switch-10-5a-r1/)
  
  <img src="https://bluerobotics.com/wp-content/uploads/2016/10/switch-4.png" width="30%" height="30%">

## Software-Side:

- ADD Set up `Position_Hold` and `Track_Path` Modes in QGC

- ADD Set up a Simulation Environment for equipment testing

- ADD DVL & Companion local Wiki (Operation guides, bugs, etc.)

- TRY ADCP Modification
