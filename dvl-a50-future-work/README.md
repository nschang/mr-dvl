# Future Work on DVL

This folder contains the work currently under development or planned for future on DVL an BlueROV2. 

## Hardware-Side

- ADD Nvidia Jetson Nano to replace RPi as onboard companion SBC. (for vastly greater computing performance and, running as low as 5 watts, equip the ROV with the power of modern AI, including accelerated libraries for deep learning, computer vision, graphics, multimedia, and more. This enables quite a number of interesting applications, such as automatic object identification / obstacle avoidance / real-time 3D mapping / potential for autonomous operations.)
  
  **Specs comparison of an Jetson Nano with Rpi 3A+ & 3B+**
  ![](https://miro.medium.com/max/3200/0*wG4G3yRftvRAUxu_.png)
  ![Specs comparison of an Jetson Nano with Rpi 3A+ & 3B+](https://www.startpage.com/av/proxy-image?piurl=https%3A%2F%2Fwww.maketecheasier.com%2Fassets%2Fuploads%2F2019%2F06%2Fjetson-vs-pi-table-3.png&sp=1623156678T028a4d068ce8d7a774eb8fd68f53bc2bb6ce5666814befe90ee8b037dda73767)
  
  **An example implementation of Nvidia Jetson Nano on the Deepsouth ROV II**
  ![An example implementation of Nvidia Jetson Nano on the Deepsouth ROV II](https://aws1.discourse-cdn.com/business5/uploads/bluerobotics/optimized/2X/f/f3cc098c1ef960713b3400af177386711821814e_2_690x388.png)

- ADD quick-switch to battery housing to simplify operations with battery. Example here: 
  **High Pressure Waterproof Switch for ROVs by BlueRobotics**
  ![](https://bluerobotics.com/wp-content/uploads/2016/10/switch-4.png)
  
## Software-Side:

- ADD Set up `Position_Hold` and `Track_Path` Modes in QGC

- ADD Set up a Simulation Environment for equipment testing

- ADD DVL & Companion local Wiki (Operation guides, bugs, etc.)

- TRY ADCP Modification
