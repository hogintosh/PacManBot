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
\subsection{Robot Kinematics}

The PacManBot is modeled as a differential-drive mobile robot with state

\[
\mathbf{x} =
\begin{bmatrix}
x \\ y \\ \theta
\end{bmatrix}
\]

where \(x\) and \(y\) denote the robot position in the global frame, and
\(\theta\) is the robot heading angle. Since the TurtleBot 4 uses an
iRobot Create 3 base, the robot follows differential-drive kinematics. Let
\(v_L\) and \(v_R\) denote the left and right wheel linear velocities,
respectively, \(r\) the wheel radius, and \(L\) the distance between the
wheels.

The body-frame linear and angular velocities are

\[
v = \frac{v_R + v_L}{2}
\]

\[
\omega = \frac{v_R - v_L}{L}
\]

If wheel angular velocities \(\dot{\phi}_L\) and \(\dot{\phi}_R\) are used
instead of linear wheel velocities, then

\[
v_L = r\dot{\phi}_L, \qquad v_R = r\dot{\phi}_R
\]

and therefore

\[
v = \frac{r}{2}\left(\dot{\phi}_R + \dot{\phi}_L\right)
\]

\[
\omega = \frac{r}{L}\left(\dot{\phi}_R - \dot{\phi}_L\right)
\]

The continuous-time kinematic model mapping control inputs to state rates is

\[
\dot{x} = v \cos\theta
\]

\[
\dot{y} = v \sin\theta
\]

\[
\dot{\theta} = \omega
\]

Equivalently, in matrix form,

\[
\begin{bmatrix}
\dot{x} \\
\dot{y} \\
\dot{\theta}
\end{bmatrix}
=
\begin{bmatrix}
\cos\theta & 0 \\
\sin\theta & 0 \\
0 & 1
\end{bmatrix}
\begin{bmatrix}
v \\
\omega
\end{bmatrix}
\]

For discrete-time implementation with timestep \(\Delta t\), the state update is

\[
x_{k+1} = x_k + v_k \cos(\theta_k)\Delta t
\]

\[
y_{k+1} = y_k + v_k \sin(\theta_k)\Delta t
\]

\[
\theta_{k+1} = \theta_k + \omega_k \Delta t
\]

Thus, the robot controller converts the wheel motions (or equivalently the
commanded linear and angular velocity inputs) into updates of the robot pose
\((x,y,\theta)\). This model is used for navigation, trajectory tracking, and
pose propagation in the PacManBot system.

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
