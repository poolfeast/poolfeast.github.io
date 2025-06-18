---
layout: post
title: "Laptime Prediction and Lap Recorder"
excerpt_separator: <!--summary-->
date: 2025-5-1
---
Designing a real-time predictive laptime algorithm for a custom in-car lap recorder.
<!--summary-->



# What is a "Lap Recorder"?

A lot of racecar analytic data - about lap times, driver inputs and racing lines etc - can be derived using just a high rate GPS receiver. Which typically output position and velocity measurements at ~5-10Hz.

Hardware for collecting this telemetry often performs storage and displays real-time performance metrics as well.

EG: [Racelogic VBox](https://www.vboxmotorsport.co.uk/index.php/en/vbox-laptimer)

A friend and I made one with an ESP32 and a small LCD screen, and I implemented the real-time performance metrics.

Scroll to bottom for hardware details and race footage!

# Predictive Laptime

One useful and common metric is "predicted laptime".

The definition of predicted laptime, for the purpose of this project, is defined as:  
"If the remainder of your current lap was done as well as your best lap, what would that laptime be?"

You might be familiar with laptime prediction from the "lap delta" estimates on video games or race broadcasts.
It is often shown as seconds relative to best lap, so the user doesn't have to remember typical lap times for that track.

This value is very useful in real time.  
For example, if the value becomes more positive during a corner, you can be confident your strategy for the corner is worse than your best.

Also useful, especially for slower cars like ours, is a velocity delta value. Returning the difference between your current speed and the equivalent point in your best lap. 

# Algorithm Details

The process for predicting laptime is as follows:[^1]
1. New GPS position and velocity states are captured
2. The stored reference/best lap is queried to find the time in the reference lap where the vehicle was at a comparable point
3. The time remaining after this point in the reference lap is added to the current laptime

For step 2, two decisions need to be made. Reference lap representation and comparable point definition.

We'll start with lap representation, as this also heavily informs the algorithm for intersection finding.

[^1]: Calculation of current laptime and recording of best lap data is less interesting so I haven't covered it.

## Lap representation

If we assume that reference laps consist of GPS position and velocity values. Then the simplest way to represent the lap would be to find the most suitable reference GPS sample and work with that.

Unfortunately, the GPS data rate is not high enough for accurate lap times, and interpolation between samples is required.  
This will transform the discrete GPS samples into a continuous line approximating the vehicles path.

GPS sensors return both a position and velocity reading. These two readings are derived using different processes, and the velocity is not simply derived from position history like you might think.
Therefore using velocity information can reduce the overall error. [Nice Racelogic summary](https://vboxautomotive.co.uk/index.php/en/how-does-it-work-gps-accuracy)

The most obvious (to me) interpolation method would be linear interpolation of both velocity and position independently between each point.

There is no fundamental issue with this, and I never did any analysis to determine the magnitude of the errors you would see in practice.

However, this project was for fun not for work. So I thought I could do better. Linear intersections might have easy, closed form solutions. Which I wanted to avoid.

So I discovered a cubic interpolation method that combines both velocity and position data into a physically plausible shape. 

https://www.researchgate.net/publication/283199429_Kinematic_interpolation_of_movement_data
and 
https://github.com/jedalong/pathinterpolatr

It seems a bit too obscure, for something so generally applicable. Perhaps karman filters are used for this conventionally? Despite the unanswered questions, I pushed ahead with the first thing I liked. For science!

It produces smooth curves that look like this:

[![Interpolation](/assets/img/Datalogger Interpolation.gif)](/assets/img/Datalogger Interpolation.gif)

Here is the interpolation formula for a single axes, $$ x0 $$ representing the start position.
Where the other terms are constants defining the lap points on either end.

$$ f(t) = \mathtt{x0} + \frac{t^{3} \left( 2 \mathtt{x0} - 2 \mathtt{x1} + \mathtt{v0} \Delta + \mathtt{v1} \Delta \right)}{\Delta^{3}} + \frac{t^{2} \left(  - 3 \mathtt{x0} + 3 \mathtt{x1} + \left( \mathtt{v0} - \mathtt{v1} \right) \Delta - 3 \mathtt{v0} \Delta \right)}{\Delta^{2}} + t \mathtt{v0} $$

## Equivalent Lap State Algorithm and Approach


As each new GPS sample arrives, we need to "cut" our stored best lap path at a representative time within the lap.

What best lap time "represents", or matches the new position and velocity we've received most closely is a open to interpretation. 

I see at least two approaches, finding the time during the best lap that was...
1. closest overall to the new sample, a simple 2D distance calculation ignoring current velocity
2. laterally closest, the point on the best lap that lies on a line perpendicular to the current heading.

[![Rootfinding](/assets/img/Datalogger Rootfinding.gif)](/assets/img/Datalogger Rootfinding.gif)


The solution to the problem is the time within the best lap that produces a vehicle position on the line.

- Multiple solutions will exist, as the tracks are usually loops
- In a corner, it might not be correct to say that one car on the inside and one on the outside will have the same lap time. But we assume lateral position on a straight doesn't have an impact. 
- Drivers shouldn't be looking during a corner anyway

{diagram of a car on the track, with a line extending out the sides}


All we need is a formula for the distance between a point (the position returned by our interpolation above), and a line perpendicular to a given angle.

From: https://en.wikipedia.org/wiki/Distance_from_a_point_to_a_line#Line_defined_by_point_and_angle

$$ f() = \left(  - \mathtt{Px} + \mathtt{tx} \right) sind\left( \mathtt{heading} \right) + \left(  - \mathtt{Py} + \mathtt{ty} \right) cosd\left( \mathtt{heading} \right) $$

It's specified so that distances to points in the "heading" direction are positive.

This a few desirable properties:
- The vast majority of cases, these two lines will be effectively perpendicular.
- (The solution gradients are very favorable for gradient based methods)
- It should produce reasonable results during cornering (open to debate)

# The Implementation

{Image of the julia code converging}

{screenshot of the lap test data and the stdout}

{code sample, from the repo}

Uses Julia

Results are around 3 iterations for test data.

Uses floats for esp32 optimisations
Uses -Ofast, fusing floating point operations and reducing instructions by two thirds.


# The Hardware

 - ESP32 v3
 - Arduino SDcard shield
 - Ublox 10Hz GPS module, with external antenna
 - Screen from an old 3d printer
 - AA batteries
 - A rustic 3D printed enclosure

The installation process is simply drilling holes in the dashboard, and applying cable ties until it seems like it's not going anywhere: 

[![The HW](/assets/img/Datalogger HW.jpg)](/assets/img/Datalogger HW.jpg)

Unfortunately it did go somewhere, although as the day progressed we cobbled it back together:

<iframe width="560" height="315" src="https://www.youtube.com/embed/fwtsO8ZoLiw?si=4_dtyzdxjEijNT2K&amp;start=9588" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

(I'm the driver who didn't notice their gloves were missing... Woops!)

We didn't finish the code in time, we only had datalogging. Which actually didn't work properly either.