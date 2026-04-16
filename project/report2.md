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

The PacManBot is modeled as a differential-drive mobile robot with planar state

\[
\mathbf{x} =
\begin{bmatrix}
x \\
y \\
\theta
\end{bmatrix},
\]

where \(x\) and \(y\) are the robot position in the map frame, and \(\theta\) is the robot heading.

Let the right and left wheel angular velocities be \(\omega_r\) and \(\omega_l\), respectively.  
If \(r\) is the wheel radius and \(L\) is the distance between the wheels, then the body-frame linear and angular velocities are

\[
v = \frac{r}{2}(\omega_r + \omega_l),
\]

\[
\dot{\theta} = \omega = \frac{r}{L}(\omega_r - \omega_l).
\]

Thus, the continuous-time kinematic model is

\[
\dot{x} = v\cos\theta,
\]

\[
\dot{y} = v\sin\theta,
\]

\[
\dot{\theta} = \omega.
\]

Substituting the wheel-speed expressions gives

\[
\dot{x} = \frac{r}{2}(\omega_r + \omega_l)\cos\theta,
\]

\[
\dot{y} = \frac{r}{2}(\omega_r + \omega_l)\sin\theta,
\]

\[
\dot{\theta} = \frac{r}{L}(\omega_r - \omega_l).
\]

For a discrete-time implementation with timestep \(\Delta t\), the state update becomes

\[
x_{k+1} = x_k + v_k \cos(\theta_k)\Delta t,
\]

\[
y_{k+1} = y_k + v_k \sin(\theta_k)\Delta t,
\]

\[
\theta_{k+1} = \theta_k + \omega_k \Delta t.
\]

Equivalently, in terms of wheel angular velocities,

\[
x_{k+1} = x_k + \frac{r}{2}(\omega_{r,k} + \omega_{l,k})\cos(\theta_k)\Delta t,
\]

\[
y_{k+1} = y_k + \frac{r}{2}(\omega_{r,k} + \omega_{l,k})\sin(\theta_k)\Delta t,
\]

\[
\theta_{k+1} = \theta_k + \frac{r}{L}(\omega_{r,k} - \omega_{l,k})\Delta t.
\]

In the implemented ROS 2 system, the planner operates at the navigation level by sending target poses \((x,y)\) to Nav2, while localization provides the current robot pose \((x,y,\theta)\) through AMCL. The lower-level differential-drive controller then converts commanded body velocities \((v,\omega)\) into wheel motions.

---

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

#### Test 1: Repeated Intialization (Stationary Test)
##### Methodology:
- Robot placed in a position in the generated map
- AMCL was reinitialized multiple times using Nav2 inital pose tool
- Log /amcl_pose into a csv file for later processing
- Repeat initialization 20 times

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


### 3.2 Run-Time Issues:
/amcl_pose was not continously published while the robot was stationary unless big changes to the scan topic were observed.


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
