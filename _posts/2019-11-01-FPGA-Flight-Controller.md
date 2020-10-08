---
layout: post
title: "Multirotor Flight Controller Implemented on an FPGA"
excerpt_separator: <!--summary-->
date: 2019-11-01
---
This project was undertaken primarily to ease the learning curve for new RMIT students implementing control systems on new UAV configurations. Although FPGAs bring many performance and flexibility benefits for research applications.
Learning FPGA development along with the obstructive toolchain by myself was very challenging.
 <!--summary-->

[![Testbench](/assets/img/FPGA Testbench.jpg)](/assets/img/FPGA Testbench.jpg)
*The setup for testing ESC signal output and closed loop control. With the Altera/Intel FPGA visible in the center, and the propeller test stand on the left*

For my final year project I created a FPGA based hardware platform for use in Multirotor UAVs. I had no experience with FPGA development and no other students to work with. Which made development and debugging very challenging. 
However my supervisor and I came up with a really exciting design, and my enthusiasm for the project carried me through many weeks of tedious problem solving.

Along the way I learning a lot about hardware debugging and the general FPGA design concepts.

### Quick Feature Summary
* Offload interrupt driven tasks into hardware, reducing software complexity
	* Timers
	* Sensor Data Preprocessing
* Manage peripheral output postprocessing
	* ESC throttle
* Perform closed loop control of propeller speed
* Offload complex recurring operations into hardware
	* Kalman Filters
	* PID Controllers

[Please see the report for more information](/assets/doc/FPGA Final Report.pdf)


[![Project Poster](/assets/img/FPGA Poster.svg)](/assets/img/FPGA Poster.svg)
*I designed the project poster to attract attention, surprisingly, I still wished I'd made it brighter!*


[![Testbench System Diagram](/assets/img/FPGA System.svg)](/assets/img/FPGA System.svg)
*The overall layout of the system as implemented in the testbench shown above*


[![IO System Diagram](/assets/img/FPGA IO System.svg)](/assets/img/FPGA IO System.svg)
*The concept for how data flows into and out of the softcore, with pointers to areas of memory being allocated by the hardware before being read by the CPU*