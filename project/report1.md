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

Perception Stack:
LiDAR - RPLidar A1
Stereo Depth Camera - Luxonis OAK-D-Lite

### Operating System:
Ros2 Jazzy Running on Ubuntu 24.04

---

## 3. High-Level System Architecture

---

## 4. Saftey and Operational Protocol

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
