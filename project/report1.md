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

### Module Declaration Table

| Module/node | Functional Domain | Software Type | Description |
| :--- | :--- | :--- | :--- |
| LiDAR/Depth Camera | Perception | Library | Grabs raw LiDAR and RGB-D/depth data from TurtleBot 4 topics. |
| SLAM Toolbox | Estimation | Library | Registers new LiDAR frames to build and maintain an occupancy map. |
| Robot Localization | Estimation | Library | Fuses odometry and IMU data using an EKF for improved pose estimation. |
| Ghost Path Behavior | Game State | Custom | Determines ghost positioning and behavior based on live robot movement and map constraints. |
| Maze Generation | Game State | Custom | Generates a Pac-Man style maze on top of the live occupancy map and populates it with pellets. |
| Risk-Reward Robot Path Planning | Planning | Custom | Determines robot movement using a dynamic tradeoff between pellet rewards and ghost risks. |
| Diff-Drive Controller | Motion Control / Actuation | Library | Sends velocity commands to the TurtleBot 4 for physical motion execution. |

### Module Intent

#### LiDAR/Depth Camera
This module is responsible for acquiring raw environmental sensor data from the TurtleBot 4 platform, including 2D LiDAR scans and depth-enabled camera streams. These perception inputs are required for mapping, obstacle awareness, and future game-state reasoning. The LiDAR stream supports SLAM, obstacle detection, and environment boundary extraction, while depth data may later be used to improve situational awareness or enable visual overlays. Configuration and tuning for this module will focus on topic synchronization, frame rate, transform consistency, and filtering or range limits to improve performance in cluttered indoor environments.

#### SLAM Toolbox
We selected ROS 2 SLAM Toolbox as the primary mapping framework because it provides a mature and reliable method for generating a 2D occupancy grid map from LiDAR scans in real time. This module continuously registers incoming scans against prior observations to estimate map structure and maintain the robot’s relation to the environment. SLAM Toolbox is appropriate for this project because the Pac-Man style environment depends on a stable map that can support both robot navigation and virtual maze generation.

#### Robot Localization
We selected the `robot_localization` package to fuse odometry and IMU data using an Extended Kalman Filter (EKF), producing a more stable pose estimate than raw odometry alone. This module is essential because the planner and ghost interaction logic depend on a consistent estimate of the robot’s position and heading. Key parameters to tune include process noise covariance, measurement covariance, update frequency, and enabled state variables. Frame conventions and TF consistency must also be verified so the pose estimate can be used cleanly by downstream planning and control modules.

#### Ghost Path Behavior
This custom module simulates virtual ghost agents whose movement is constrained by the same occupancy structure and traversal limitations as the physical TurtleBot. Its purpose is to create dynamic adversaries that behave as though they are navigating the same mapped world, rather than arbitrarily moving through walls or obstacles. The algorithm will use the current occupancy map, robot pose, and potentially recent robot motion history to update each ghost’s location according to a constrained movement policy. Its output will be the ghost states and predicted near-term ghost trajectories that are passed to the planner as dynamic risk sources.

#### Maze Generation
This module constructs a virtual Pac-Man style maze representation on top of the live occupancy map produced by SLAM. Its purpose is to translate the robot’s physical environment into a game-compatible representation containing walls, corridors, traversable cells, and collectible pellet locations. The algorithm will derive a simplified maze graph or cell decomposition from the occupancy grid and populate valid paths with pellets. If the physical environment changes significantly, the maze representation may be updated to remain consistent with the live map.

#### Risk-Reward Robot Path Planning
This custom planning module governs the robot’s movement strategy by balancing reward and risk. Rather than always targeting the nearest pellet, the planner evaluates candidate paths using a utility-based objective that includes pellet value, path cost, ghost proximity, and predicted ghost motion. Under normal gameplay, ghosts are treated as dynamic hazards and penalized accordingly. If a power pellet mode is introduced later, ghost-related costs may be inverted or reduced to temporarily encourage pursuit behavior. This planner will likely be implemented as a modified graph-search method such as Weighted A* over a maze or occupancy-derived graph, with continuous replanning as the game state changes.

#### Diff-Drive Controller
This module is responsible for converting high-level planned motion into linear and angular velocity commands compatible with the TurtleBot 4 differential-drive base. We selected the platform’s existing diff-drive control interface because it provides a safe and standard way to actuate the robot while respecting hardware motion constraints. This allows the team to focus custom development effort on game logic, mapping integration, and risk-aware planning rather than low-level motor control.



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

#### E-Stop Trigger Condition
The E-Stop is triggered when:
- A LiDAR measurement from the scan topic falls below the critical safety distance, indicating an imminent collision risk.

- The scan-to-scan pose transformation from SLAM exceeds the maximum physically possible linear or angular motion of the TurtleBot within the elapsed time interval.

- Repeated abnormal pose transformations occur, indicating LiDAR scan misalignment or unstable localization rather than real robot motion.

#### E-Stop Response
When the E-Stop condition is met, the robot will:

- immediately publish zero velocity commands
- halt all navigation activity
- reject further movement commands until localization stabilizes or the system is reset

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
