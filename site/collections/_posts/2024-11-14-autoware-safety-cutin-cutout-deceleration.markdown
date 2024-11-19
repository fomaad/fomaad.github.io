---
title:  Autoware Safety Analysis in cutin, cutout, and deceleration scenarios
date:   2018-11-29 15:01:35 +0300
description: >-
  We propose a framework for runtime verification of ADSs, comprising a scenario description language, a runtime monitor, and an LTL property checker.
image:  '/images/aw-runtime-verif.png'
tags:   [Safety-Analysis, Autoware]
---
In this project, we conducted a bundle of experiments to evaluate the safety of Autoware, a leading open-source ADS platform. Autoware and Baidu Apollo are the only open-source autonomous driving platforms supporting full autonomy (by 2024). While there exist many studies subject to Baidu Apollo safety analysis, to the best of our knowledge, this study is the first to attempt either verification or automated testing of Autoware.
Unlike many existing studies that primarily focus on identifying and/or generating critical scenarios (that pose significant risks to ADSs or may lead to collisions), we base our approach on a well-established safety standard--[the JAMA standard](https://www.jama.or.jp/english/reports/docs/Automated_Driving_Safety_Evaluation_Framework_Ver3.0.pdf)--which offers a systematic methodology for evaluating ADS safety.

## JAMA standard
![JAMA Framework Overview](/images/JAMA-Overview.png){: width="1200" height="900"}

The JAMA standard takes into account comprehensive variations of all factors that may affect the ego vehicle’s operation, such as, road geometry variations and position as well as behavior variations of surrounding vehicles. All of these variations are combined and each combination instance serves as a scenario for evaluating ADS performance. We utilize the JAMA methodology, which provides comprehensive scenarios required to assess how ADS behave, the challenging scenarios among them, and the safety benchmarks. To the best of our knowledge, we are the first to use the JAMA standard to conduct safety testing or verification of ADSs.

We conducted a series of experiments to assess the safety of Autoware under different traffic disturbance variations defined by the JAMA standard in non-intersection road geometries, such as vehicles cutting in, cutting out, and decelerating. While no collision would occur under the competent and careful human driver model, which simulates a highly skilled and cautious human driver, Autoware failed to prevent collisions in some cases. JAMA established this competent and careful driver model as the minimum safety benchmark. An ADS must show a higher level of hazard detection and collision avoidance performance than this model; otherwise, the ADS is unsafe.

## Demonstration of some collisions
> Autoware collision avoidance is not as good as the careful driver model. 

In the experiments, we found that Autoware failed to prevent collisions in some cases, particularly during high-speed scenarios and fast lateral movements by other vehicles.
Below are demonstrations of some collisions.

### Cut-in scenarios
<p>
<video width="100%" preload="auto" muted controls>
  <source src="/videos/cutin-30-10-6.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</p>

In this scenario, the ego vehicle and the NPC vehicle travel at speeds of 30 km/h and 10 km/h (`Ve` and `Vo`), respectively, on two adjacent lanes. 
The NPC vehicle abruptly cuts in front of the ego vehicle.
The lateral velocity of the cut-in (`Vy`) is 1.6 m/s.
When the cut-in starts, the longitudinal distance between the two vehicles (`dx0`) is 11 meters.
A collision occurred (in both lidar-only and camera-lidar fusion perception modes), which can be seen in the video above.
Note that a collision is not expected under the good driver model.

### Cut-out scenarios
<p>
<video width="100%" preload="auto" muted controls>
  <source src="/videos/cutout-50-1.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</p>


In this scenario, the ego vehicle and the NPC vehicle travel at a speed of 50 km/h (`Ve` and `Vo`) on the same lane, with a longitudinal distance (`dx0`) of twice the Time Headway (THW) between them (THW = 2 seconds).
The NPC vehicle suddenly changes its lane to the adjacent lane to avoid a stopped vehicle (challenging vehicle).
The ego vehicle needs to decelerate to avoid a collision.
The lateral velocity of the cut-out (`Vy`) is 1.6 m/s.
The longitudinal distance between the ego vehicle and the challenging vehicle (static obstacle) at the cut-out starts is:

```
dx0 + dx0_f + length(NPC) = 2 * 13.889 + 14 + 4 = 45.778 (m)
```

Since achieving the 2-second THW constraint in Autoware is challenging (Autoware often maintains a greater distance to the vehicle ahead for safety), 
we propose to dynamically spawn the challenging vehicle at the moment the cut-out starts, ensuring that the distance from the spawning position to the ego vehicle equals the distance above.

A collision occurred in both perception modes (video above).

### Deceleration scenarios
<p>
<video width="100%" preload="auto" muted controls>
  <source src="/videos/decel-20.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</p>

In this scenario, the ego vehicle and the NPC vehicle ahead travel at a speed of 20 km/h (`Ve` and `Vo`) on the same traffic lane, with a longitudinal distance of twice the THW between them.
The NPC vehicle suddenly decelerates and stops at a deceleration of 1.0G.

Similar to the cut-out scenario, we propose to dynamically spawn the challenging vehicle to achieve the 2-second THW constraint in Autoware.

A collision occurred in the camera-lidar fusion perception mode (video above).

<!-- In contrast, the lidar-only mode could avoid a collision, see the following video: -->

All materials supporting this project are available at [https://github.com/duongtd23/AW-Runtime-Verification](https://github.com/duongtd23/AW-Runtime-Verification).