---
layout: post
title: "Laptime Prediction and GPS Datalogger"
excerpt_separator: <!--summary-->
date: 2025-5-1
youtubeId: fwtsO8ZoLiw #&t=173s
---
An algorithm for real-time comparisons against a stored fastest lap.
 <!--summary-->



# Introduction to the problem

A lot of racecar analytic data - about lap times, driver inputs and racing lines etc - can be derived using just an accurate and high rate GPS receiver.

So the hardware that collects this telemetry can cost less than 200 AUD. Which is competitive with something you can buy off-the-shelf. EG: Racelogic <LINK>

A friend and I decided to try and make one, as it seemed approachable, but with enough potential complexity to be fun.

Heres the hardware:
 - ESP32 v3
 - Arduino SDcard shield
 - 10Hz GPS ublocs module, with external antenna
 - Screen from a 3d printer
 - AA batteries
 - A last minute 3D printed enclosure 

The installation process entails drilling into the dashboard, then applying cable ties until it seems like it's not going anywhere: 

[![The HW](/assets/img/Datalogger HW.jpg)](/assets/img/Datalogger HW.jpg)

Unfortunately it did go somewhere, although as the day progressed we cobbled it back together:
{% include youtubePlayer.html id=page.youtubeId %}

(I'm the idiot who didn't notice his gloves were missing)

# Predictive Laptime


You might be familiar with laptime prediction from the "lap delta" estimates on video games.

The definition of predictive laptime for the purpose of this project is:
"If the remainder of your current lap was done as well as your best (recorded) lap, what would that laptime be?"
This is often displayed as a delta +/- seconds to the best lap. So most of the time you will have postive values.

This value is very useful in real time. For example, if the value above becomes more positive during a corner, you can be reasonably sure your strategy for the corner is worse than your best.

Also useful (especially for slower cars like ours) is the velocity delta value. Returning the difference between your current speed and the equivalent point in your best lap. 

With these two numbers, the driver gains instant insight into how they should be tackling each corner. Very useful for novices like us.


# How to Overcomplicate Predictive Laptime

The interesting part of this problem, for me, is finding the "cut point" between the best lap and the current lap.
IE, for each new state received from the GPS, we need to "cut" the best lap path at the point "equivalent" to that state.

In order to define an alorithm for this intersection finding, we first need to define how laps are represented.

## Lap representation

GPS sensors return both a position and velocity reading. These two readings are derived using different processes, and the velocity is not simply derived from position history like you might think.

{diagram with points that each have a position and velocity}

The most obvious (to me) intepolation method would be linear interpolation of both velocity and position independently between each point.

{diagram with points connected via straight lines}

There is no fundamental issue with this, and I never did any analysis to determine the magnitude of the errors you would see in practice.

However, this project was for fun not for work. So I thought I could do better. Linear intersections might have easy, closed form solutions. Which I wanted to avoid.

So I discovered an cubic interpolation method that combines both velocity and position data into a physically plausible shape. 

https://github.com/jedalong/pathinterpolatr

It seems a bit too obscure, for something so generally applicable. Perhaps karman filters are used for this conventionally? Despite the unanswered questions, I pushed ahead with the first thing I liked. For science!

It produces smooth curves that look like this:

{diagram of path data connected with splines}

## Calculating an "equivalient" point

I decided to take the velocity normal as a line perpendicular to the direction of travel. 
The solution to the problem is the time within the best lap that produces a vehicle position on the line.

- Multiple solutions will exist, as the tracks are usually loops
- In a corner, it might not be correct to say that one car on the inside and one on the outside will have the same lap time. But we assume lateral position on a straight doesn't have an impact. 
- Drivers shouldn't be looking during the corner anyway :)

{diagram of a car on the track, with a line extending out the sides}

$$ \nabla_\boldsymbol{x} J(\boldsymbol{x}) $$
$$ \mathtt{x0} + \frac{t^{3} \left( 2 \mathtt{x0} - 2 \mathtt{x1} + \mathtt{v0} \Delta + \mathtt{v1} \Delta \right)}{\Delta^{3}} + \frac{t^{2} \left(  - 3 \mathtt{x0} + 3 \mathtt{x1} + \left( \mathtt{v0} - \mathtt{v1} \right) \Delta - 3 \mathtt{v0} \Delta \right)}{\Delta^{2}} + t \mathtt{v0} $$

All we need is a formula for the distance between a point (the position returned by)

This a few desirable properties:
- The vast majority of cases, these two lines will be effectively perpendicular.
- (The solution gradients are very favourable for gradient based methods)
- It should produce reasonable results during cornering (open to debate)

# The Implementation

{Image of the julia code converging}

{screenshot of the lap test data and the stdout}

{code sample, from the repo}

Uses Julia

Results are around 3 iterations for test data.

Uses floats for esp32 optimisations
Uses -Ofast, fusing floating point operations and reducing instructions by two thirds.


# The Race

We didn't finish the code in time, we only had datalogging. Which actually didn't work properly either.