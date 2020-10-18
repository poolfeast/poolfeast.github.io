---
layout: post
title: "CNC and Hand Machining Portfolio"
excerpt_separator: <!--summary-->
date: 2019-10-18
---
The University Rover Challenge takes place once a year, at the Mars Desert Research Station in America. It requires teams to construct autonomous exploration robots thats compete in a number of tests requiring object manipulation, life detection, and autonomous traversal.
For RMIT's first entry into the URC we needed a software system that could be implemented quickly, but wouldn't hamper later development with architecture redesigns.
 <!--summary-->


[![Hammer](/assets/img/Machining Hammer.jpg)](/assets/img/Machining Hammer.jpg)
A soft-blow hammer made by hand, with aluminium heads. Features threading between each component, knurling on the handle, and slight mill use for the fancy hex on the bottom.
Great for separating driveshaft tripods without cracking the brittle steel.
The threads between the steel handle and aluminium shaft have galled, which is good. But the head to shaft thread needed some unholy force to get them to stay attached, some locktite would probably have worked better.

[![2D SR71](/assets/img/Machining SR71.jpg)](/assets/img/Machining SR71.jpg)
A 2D CNC model, SR-71 modelled in Catia and the toolpath generated in EdgeCAM, you might notice a little bit missing on the nacelles, which was due to machine homing variation. I made the cuts too close to the surface.
It involved four or five tool changes, with the path calculation method, size, and shape of the tool varying for each area of the part, for example using a ball end mill for the sloped sections of the fuselage. I managed to optimise the path enough to get it to 40-something minutes, but still have the fine details visible (like small vents on the engine).

[![2D YinYang](/assets/img/Machining YinYang.jpg)](/assets/img/Machining YinYang.jpg)
My first CNC part, done with hand-written g-codes. Some of the wood at the pointy sections was too thin and chipped off. Otherwise its pretty good.

G90 G21 G17
G40 G80
G91 G28 X0. Y0. Z0.
G90 T3 M06 
M04 S15000 F500

G00 X75. Y5.
G01 Z-4 F500
G02 X75. Y5. I0 J45.
G03 X75. Y50. I0 J22.5
G02 X75. Y95. I0 J22.5
G00 Z5.
G00 Y77.5
G01 Z-4.
G02 X75. Y77.5 I0. J-5.
G00 Z5.
G00 Y32.5
G01 Z-4.0
G02 X75. Y32.5 I.0 J-5.
G00 Z5.

G49 M05 
G91 G28 X0. Y0. Z0.
G90 
M30