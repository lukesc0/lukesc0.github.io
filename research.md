---
layout: default
---

# QSIB Manufacturing Research

### Overview

I spent one of my summers and an academic year working as a student researcher at the Querrey Simpson Institute for Bioelectronics, a Northwestern laboratory also known as the Roger's Lab. The lab specializes in compact, wearable bioelectronic devices built upon flexible PCBs created by Dr. John Rogers. My research was centered around automating the fabrication of such devices using a Pick and Place machine, which was a challenge due to both the small scale of the device components as well as the flexible nature of the PCBs.

### Research

My research was based on the fabrication of 2 different devices, a brain patch and an imaging device, both of which used an array of nanometer-scale diodes to influence the behavior of lab rats. Both were built upon flexible PCBs. While there were some significant technical differences between the two devices, for my purposes they were different variations of the same array, as both used similar LED components.

[Image of brain patch]

[Image of imaging device]

Previously, most devices in the lab using components this size were built by hand, using tweezers under a microscope -- an arduous and time-consuming process. My goal was to fully automate the component placement process, so that a fellow researcher could follow simple PCB mounting and solderpaste screen-printing instructions, place the device into the Pick and Place machine, calibrate, and press play.

As such, I compiled 3 main goals:

1. Find the best-performing method of picking up components, which would mainly consist of testing various nozzle sizes as well as machine settings.
2. Develop a proper PCB mounting procedure to ensure accurate component placement.
3. Program the Pick and Place machine to build my two devices and provide instructions to fellow researchers so that my processes could be recreated.

### Methods

While pursuing my first goal, I followed a fairly straightforward process of trial and error, in which I would vary either the size of the nozzle or the amount of suction used (or both). I tested a range of different nozzle sizes, starting from the largest diameter that was still smaller than the width of the LEDs. As the LEDs were around 100x100 microns, this was already fairly small. I then worked my way down to the smallest nozzle available, ____. With each size, I also tested having the suction on or off -- as described in the results section, it turns out that with components this small Van-der-Waals forces are at times all you need.

For my second goal, I conceptualized a few different methods of PCB mounting and tested each using the optimal nozzle and settings I eventually found with my first goal. For each method, I attempted to use the machine to place LEDs ... I made sure to note both the variation in position and rotational angle...

For my third and final goal, I held brief training sections with the brain patch and imaging device teams instructing them on my processes and how to use the programs I wrote.

### Results

Not using suction, using van-der-waals instead.

Providing instructions to properly clean end bit of pick and place machine, using smallest bit possible.

Proper PCB mounting guidance -- perfect accuracy and orientation extremely important.

[find other images]

[back](./)
