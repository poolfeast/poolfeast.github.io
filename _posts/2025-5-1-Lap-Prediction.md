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
 - An ESP32 v3
 - 10Hz GPS ublocs module
 - A screen from a 3d printer

The installation process entails drilling into the dashboard, and applying cableties until it seems like it's not going anywhere: 

[![The HW](/assets/img/Datalogger HW.jpg)](/assets/img/Datalogger HW.jpg)

Unfortunately it did go somewhere:
{% include youtubePlayer.html id=page.youtubeId %}

# Predictive Laptime (and overcomplicating it)

