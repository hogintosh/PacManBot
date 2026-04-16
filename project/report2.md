---
layout: default
title: "Milestone 2"
parent: Project
nav_order: 2
published: true
nav_exclude: false
---
# Milestone 2

{: .no_toc }

This page demonstrates the core capabilities of the Just the Docs theme, including navigation, mathematical typesetting, and technical diagrams.

---

## Table of Contents

{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 1. Kinematics:
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

The control inputs to the robot are the left and right wheel velocities:

$$
v_L = \text{left wheel velocity}, \quad v_R = \text{right wheel velocity}
$$

These wheel velocities are mapped to the robot's linear and angular velocities by:

$$
v = \frac{v_R + v_L}{2}
$$

$$
\omega = \frac{v_R - v_L}{L}
$$

where \(L\) is the distance between the two wheels.

The differential drive kinematic equations describe how the robot's state updates over time as these control inputs are applied:

$$
\dot{x} = v \cos(\theta)
$$

$$
\dot{y} = v \sin(\theta)
$$

$$
\dot{\theta} = \omega
$$

## 2. System Achitecture:



---
## 3. Experimental Analysis & Validation
### 3.1 Noise & Uncertainty Analysis:

#### Experimental Setup:
Localization performance was evaluated on the Turtlebot4 using Nav2 with maps generated from slam_toolbox. Robot pose estimation was provided by Adaptive Monte Carlo Localization (AMCL). To evaluate the localization uncertainty, the Turtlebot4 was tested in a real indoor environment.

A map of the testing environment was first generated using slam_toolbox, and this map was used by the navigation stack for localization and path planning. The robot pose estimation used during navigation was obtained from Adaptive Monte Carlo Localization (AMCL). This works by fusing LiDAR data with odometry to estimate the robot's position in the map frame. 

The experiments to characterize localization:
1. Repeated Initialization Test (Pose Estimation Uncertainty)
2. Repeated Navigation Trial (Motion-Based Uncertainty

#### Test 1: Repeated Initialization (Stationary Test)
##### Methodology:
- Robot placed in a position in the generated map
- AMCL was reinitialized multiple times using Nav2 initial pose tool
- Log /amcl_pose into a csv file for later processing
- Repeat initialization 20 times producing 11 different AMCL estimates

##### Results:


#### Test 2: Repeated Navigation Trial (Motion-Based Uncertainty)
##### Methodology:
- Robot was placed by hand to a start location
- The robot pose was initialized through Nav2 - AMCL
- The robot goal coordinate was sent through Nav2
- Robot navigated to the goal through nav2
- /amcl_pose was logged and saved into a csv file
- repeated over 4 trials

##### Results:
##### Test 1: AMCL Pose Initialization Results
| Metric | Value |
|------|------|
| Number of Samples | 11 |
| Mean X (m) | -0.2616 |
| Mean Y (m) | 2.6982 |
| Std Dev X (m) | 0.0350 |
| Std Dev Y (m) | 0.0363 |
| Mean Radial Spread (m) | 0.0481 |
| Max Radial Spread (m) | 0.0735 |

The AMCL pose estimate was evaluated by repeatedly initializing the robot at the same physical location using the 2D Pose Estimate tool. A total of 11 samples were collected.

The results show a positional standard deviation of approximately 3.5 cm in both the X and Y directions, with a mean radial spread of 4.8 cm and a maximum deviation of 7.35 cm.

This indicates that AMCL initialization introduces a small but measurable localization uncertainty, even when the robot remains stationary.

##### Test 2: Navigation Trial Results

### 3.2 Run-Time Issues:
/amcl_pose was not continuously published while the robot was stationary unless big changes to the scan topic were observed, or if pose was reinitialized with Nav2.



### 3.3 Milestone Video

---

## 4. Project Management

### 4.1 Instructer Feedback Integration
#### Comments: 
Super cool project! Excellent write-up! You are the only team with a Table of Contents. Really well-thought out Safety Protocols.

#### Fixes: 
1. Remove the stale "Markdown Features" section at the bottom

#### Project Idea Feedback: 
1. I am sure you would need a visual system for debugging, maybe use that for also showing the demo.
2. You could add some auditory and visual (LED Ring) feedback when the robot interacts with virtual elements

#### Questions/Clarifications: 
1. Have you checked if there are any similar proejects out there in the AR/VR world?

### 4.2 Individual Contributions:

| Team Member         | Primary Technical Role (PTR) | Key Git Commits / PRs | Specific File(s) Authorship (Direct Links) |
|--------------------|------------------------------|------------------------|-------------------------------------------|
| Sean Vellequette   | PTR                          | Git                    | File                                      |
| Gabriel Sandys     | PTR                          | Git                    | Files                                     |
| Abdirahman Aden    | PTR                          | Git                    | Files                                     |

---
