---
layout: post
title: "Multirotor Flight Controller Implemented on an FPGA"
excerpt_separator: <!--summary-->
date: 2019-11-01
---
This project was undertaken primarily to ease the learning curve for new RMIT students implementing control systems on new UAV configurations. Although FPGAs bring many performance and flexibility benefits for research applications.
Learning FPGA development along with the obstructive toolchain by myself was very challenging 
 <!--summary-->
Complex software like Ardupilot can reduce the amount of time available for solution development due to the time involved 

# Requirements
* Offload interrupt driven tasks into hardware, reducing software complexity
	* Timers
	* Sensor Data Preprocessing
* Manage peripheral output postprocessing
	* ESC throttle
* Perform closed loop control of propeller speed
* Offload complex recurring operations into hardware
	* Kalman Filters
	* PID Controllers

# Solution
Naturally an FPGA implementation comes with its own set of problems, many of the design decisions surrounding an FPGA flight controller implementation are not straightforward, as the sheer flexibility of the hardware can overwhelm any attempts to pin down system details. 
Many architecture decisions are needed to lay out interfaces and system boundaries, which will allow the work to be divided into individually manageable portions. 

Furthermore, the creation and testing of FPGA “programs” requires specialist knowledge and experience with a suite of software tools unique to the platform. In order to complete the design implementation, a Development Methodology will be constructed detailing the tools and processes necessary to implement the design.
Testing and debugging of FPGA projects requires further work and a thorough process in order to combat the oftentimes obstructive tools. 



[![Testbench](/assets/img/FPGA Testbench.jpg)](/assets/img/FPGA Testbench.jpg)
*The setup for testing ESC signal output and closed loop control. With the Altera/Intel FPGA visible in the center*




[![Testbench System Diagram](/assets/img/FPGA System.svg)](/assets/img/FPGA System.svg)
*The overall layout of the system as implemented in the testbench shown above*




[![IO System Diagram](/assets/img/FPGA IO System.svg)](/assets/img/FPGA IO System.svg)
*The concept for how data flows into and out of the softcore, with pointers to areas of memory being allocated by the hardware before being read by the CPU*



[![Project Poster](/assets/img/FPGA Poster.svg)](/assets/img/FPGA Poster.svg)
*I designed the poster to attract attention, surprisingly, I still wished I'd made it brighter!*

# Results
Not everything was completed by the project deadline, with none of the planned hardware maths offloading modules being implemented

[FYP Report](/assets/doc/FPGA Final Report.pdf)