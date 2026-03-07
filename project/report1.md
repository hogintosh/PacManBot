---
layout: default
title: "Report 1"
parent: Project
nav_order: 1
---

# Report 1: Milestone 1

{: .no_toc }

This page demonstrates the core capabilities of the Just the Docs theme, including navigation, mathematical typesetting, and technical diagrams.

---

## Table of Contents

{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 1. Mission Statement

We aim to build a Pac-Man inspired autonomous mobile robot. This robot will localize and construct a 2D map of its environment and replans it motion to collect virtual pellets. While doing so the Pac-Man robot will have to avoid virtual ghosts using a dynamic risk-based path planning algorithm. These virtual ghosts will adhere to the same movement restrictions of the robot, as to not move through walls or objects.

---

## 2. Technical Specifications
### Overview: 
This project implements a Turtlebot 4 that is capable of mapping an indoor environment while performing dynamic path planning and execution. The robot integrates perception, state-estimation, and risk-aware pathing to win the virtual game of Pac-Man.

### Robot Platform:
Hardware - Turtlebot 4
Onboard Computer - Raspberry Pi 4 Model B

### Kinematic Model: Differential Drive
Robot state vector represented as:

$$
\mathbf{x} =
\begin{bmatrix}
x \\
y \\
\theta
\end{bmatrix}
$$

The robot state is represented by its pose in the world frame:

$$
x = \text{robot position along the x-axis}
$$

$$
y = \text{robot position along the y-axis}
$$

$$
\theta = \text{robot orientation (heading angle)}
$$

The differential drive kinematic equations for the robot, tell us how the robots pose changes over time as velocity commands are applied:

$$
\dot{x} = v \cos(\theta)
$$

$$
\dot{y} = v \sin(\theta)
$$

$$
\dot{\theta} = \omega
$$

### Perception Stack:
LiDAR - RPLidar A1
Stereo Depth Camera - Luxonis OAK-D-Lite

### Operating System:
Ros2 Jazzy Running on Ubuntu 24.04

---

## 3. High-Level System Architecture
### Flow Diagram:
![HighLevelDiagram](/assets/images/HighLevelDiagram.png)



---

## 4. Saftey and Operational Protocol
### Deadman Switch (Communication Timeout)

To prevent uncontrolled robot motion in the event of communication loss, the system implements a software Deadman Switch. The TurtleBot must continuously receive velocity commands from the navigation or planning node in order to move.

If no new velocity command is received within a predefined timeout interval, the system assumes communication has been lost. When this occurs, the safety node immediately publishes zero linear and angular velocity to /cmd_vel, stopping the robot. The robot will remain stopped until valid commands are received again.

This protects the robot from continuing motion if:
- a ROS node crashes
- the navigation planner fails
- communication between ROS nodes is interrupted

### LiDAR Misalignment Detection
This saftey approach is based on LiDAR misalignment rejection during SLAM registration. In this method, the TurtleBot monitors the pose transformations produced by the SLAM system between consecutive scans. Since SLAM estimates the robot’s motion by aligning LiDAR scans over time, incorrect scan matching or sensor misalignment can sometimes generate unrealistic jumps in the robot’s estimated position or orientation.

If the robot were to trust these incorrect transformations, it could attempt unsafe movements based on corrupted localization data. To prevent this, the system compares the SLAM-estimated scan-to-scan motion against the known physical motion limits of the TurtleBot. Because the robot has maximum achievable linear and angular speeds, it should not appear to move farther or rotate more than those limits allow within a given time interval.

If the estimated transformation exceeds what the TurtleBot could physically accomplish in that time, the update will be treated as a LiDAR or SLAM misalignment, and the system will trigger an emergency stop. This method functions as a localization sanity check, ensuring the robot does not continue moving when its pose estimate becomes physically impossible.

---

## 5. Git Infrastructure

---

# Markdown Features

## Callouts
> This is a note
{: .note }

> This is a warning
{: .warning }

## Buttons
[Main Button](assignment1.html){: .btn .btn-primary }
[Blue Button](assignment2.html){: .btn .btn-blue }
[Blue Button](assignment3.html){: .btn .btn-red }

## Tables

| Header | Header |
| :--- | :--- |
| Cell | Cell |

---
