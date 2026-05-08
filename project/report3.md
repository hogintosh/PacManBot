---
layout: default
title: "Report 3"
parent: Project
nav_order: 3
published: false
nav_exclude: true
---

# Report 3: ZZ

{: .no_toc }

This page demonstrates the core capabilities of the Just the Docs theme, including navigation, mathematical typesetting, and technical diagrams.

---

## Table of Contents

{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 1.Graphical Abstract
The probability density function of a Gaussian distribution is defined as:

$$p(x) = \frac{1}{\sigma\sqrt{2\pi}} e^{-\frac{1}{2}\left(\frac{x-\mu}{\sigma}\right)^2}$$

Where:
- $$\mu$$ is the mean (peak location).
- $$\sigma$$ is the standard deviation (width of the "bell").

---

## 2.Algorithm:

Below is a snippet of the Python code used to process the assignment data.

```python
import numpy as np

def calculate_velocity(displacement, time):
    """Calculates average velocity."""
    return np.divide(displacement, time)

print(f"Result: {calculate_velocity(100, 20)} m/s")

```

---

## 3. Benchmarking & Results

The sidebar will automatically highlight the section you are currently viewing.

### 3.1 Observations

* Observation A: The system remained stable under load.
* Observation B: Latency increased during the second trial.

### 3.2 Conclusion

The experiment met all primary objectives. Future work should focus on optimizing the data pipeline.

---

## 4.Ethical Impact Statement

### Privacy and Data Handling
PacManBot uses ROS 2 topics, localization data, map files, and logged robot data to operate and evaluate performance. In this implementation, the main privacy concern is not facial recognition, but the storage of indoor environment information through maps, AMCL pose logs, trajectory logs, and demo recordings. The map used for navigation may reveal the layout of an indoor space, and ROS logs may record where the TurtleBot 4 moved during testing. Future versions that add cameras or cloud connectivity would increase privacy risk and should include data minimization, local-only processing, restricted access to logs, and blurring/anonymization for any recorded video.

### Bias and Hardware Limitations
PacManBot depends heavily on TurtleBot 4 localization, LiDAR sensing, maps, and Nav2 navigation. This creates hardware-related bias because the robot may perform well in clear, mapped environments but poorly around glass, reflective surfaces, clutter, narrow passages, or areas where LiDAR returns are unreliable. AMCL localization may also become inaccurate if the map does not match the real environment. This means the robot’s performance is not equally reliable in all spaces.


You can include images by placing them in the `assets/images/` folder.

![Alt text](../assets/images/logo.png){: width="500" }

*Figure 1: Class Logo*

---

## 5. Submission Checklist

* [x] Complete Markdown documentation
* [x] Verify LaTeX rendering
* [x] Generate Mermaid flowchart
* [ ] Peer review feedback

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
