---
layout: post
title: "CubeX 3D Printer Conversion and Calibration"
excerpt_separator: <!--summary-->
date: 2020-02-01
---
3D Systems was one of the first 3D printer manufactures. They made large scale multi-extruder printers, like the "CubeX", and tried to lock the filament market down with cartridge hardware to ensure you only used the genuine filament.
That didn't work out for them, but after I was given with an old single extruder CubeX I thought it might be worth having a go at modernising it.
 <!--summary-->

The CubeX has a 290x290x~300mm physical print volume, with a large, heavy metal carriage with linear bearing on steel rails. It has two NEMA 17 steppers for the Y axis, one for the X axis, and one large NEMA 23 stepper for the Z. The Z axis moves by way of a leadscrew, while the X and Y use toothed belts. For endstops its uses magnetic reed switches, the Z axis reed switch is triggered by a magnet under the bed at a certain location. The extruder and hotend are designed for 1.70mm filament, which is nonstandard. The fixed extruder also features filament runout and jam detection by way of a sensor on the extruders driven wheel.

[![CubeX Gantry](/assets/img/CubeX Gantry.jpg)](/assets/img/CubeX Gantry.jpg)

One of the most fascinating parts of the printers construction is how low-volume and cheaply assembled it is. Using one size of solid steel rod, plate, and 3 sizes of round steel billet to construct the metal frame.
To attach the extruder cooling fans, rubber pin rivets are used. Which was a method of fastening that was new to me, and demonstrates the costs savings the company tried to achieve during assembly.

The low-volume, mostly billet construction of the printer provides a ridged, maintainable platform for bringing the rest of the printer up to date with some more recent FDM printer technology. As well as removing its reliance on specific filament cartridges that are no longer produced.

The modifications include:
* 24V power supply
* 32 bit controller
* 128 microstep stepper drivers
* Polypropylene bed coating
* Industry standard hotend

[![Bench Setup](/assets/img/CubeX Desk.jpg)](/assets/img/CubeX Desk.jpg)

Learned:
Crimping
New wiring loom
Machining a hotend adaptor
Compiling Marlin
Tuning printer parameters

Issues:
Z Axis Microsteps
Cooling Fans
Filament Tube
Z Axis Homing
Arch Linux autoinstall 
