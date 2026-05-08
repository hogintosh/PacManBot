---
layout: default
title: "Report 3"
parent: Project
nav_order: 3
published: true
nav_exclude: false
math: mathjax
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

## 2. Algorithm

The core technical contribution of the PacManBot system is a custom game-state and decision-making layer built on top of the ROS 2 TurtleBot 4 and Nav2 navigation stack. Rather than replacing Nav2, the custom modules define the Pac-Man game behavior: pellet generation, pellet collection, Clyde ghost behavior, game-state transitions, scoring, sound feedback, light feedback, user interaction, and high-level goal selection.

This section begins with the final updated module declaration table, then presents the formal algorithmic logic for the custom modules using state-space notation, scoring equations, update rules, and event-mapping functions.

---

### 2.1 Final Updated Module Declaration Table

The PacManBot system uses both custom project modules and external ROS 2 library modules. Custom modules were developed specifically for the game logic, feedback system, pellet behavior, Clyde behavior, and game-state control. Library modules are provided by ROS 2, TurtleBot 4, Nav2, RViz, and sensor driver packages.

Compared to the previous milestone, the system has expanded beyond a basic pellet-navigation demo. The previous milestone listed `/audio_node`, `/game_event_mapper`, `/game_light_node`, `/pellet_manager`, and `/planner_stub` as the main custom project nodes, while ghost behavior and risk-reward planning were still listed as pending. The current implementation now includes additional custom modules such as `/game_controller`, `/clyde_ghost_node`, `/wait_for_amcl_pose`, `/game_state_demo_gui`, and a custom RViz plugin panel. The project has also progressed from simple nearest-pellet navigation into a more complete game architecture with Clyde-aware planning, game-state management, scoring, round progression, reset behavior, and user interface support.

| Module / Node | Functional Domain | Software Type | Description | Previous Milestone Status | Current Status | Change Since Previous Milestone |
|---|---|---|---|---|---|---|
| LiDAR Sensor / RPLiDAR | Perception | Library / Driver | Provides LiDAR scan data used by AMCL and Nav2 costmaps. | Completed | Completed | Active perception source for localization and navigation. |
| SLAM Toolbox | Mapping | Library | Used to build the saved occupancy grid map. | Completed | Completed | Runtime uses the saved map produced from SLAM. |
| AMCL / Robot Localization | Localization | Library | Estimates robot pose on the saved map. | Completed | Completed | Still required for planner and game-state logic. |
| [Map Server / `map_01.yaml`](https://github.com/GSandys7/PacManBot_ROS2/blob/main/pacmanbot_package/maps/map_01.yaml) | Mapping Support | Library / Configured | Publishes the saved occupancy map. | Completed | Completed | Used by Nav2 and custom map-based modules. |
| [Nav2 Planner Server](https://github.com/GSandys7/PacManBot_ROS2/blob/main/pacmanbot_package/launch/nav2_custom.yaml) | Navigation Planning | Library / Configured | Computes global paths to selected goals. | Completed | Completed | Integrated with custom pellet goal selection. |
| [Nav2 Controller Server](https://github.com/GSandys7/PacManBot_ROS2/blob/main/pacmanbot_package/launch/nav2_custom.yaml) | Motion Control | Library / Configured | Tracks paths and generates velocity commands. | Completed | Completed | Uses tuned controller behavior for game navigation. |
| [Local / Global Costmaps](https://github.com/GSandys7/PacManBot_ROS2/blob/main/pacmanbot_package/launch/nav2_custom.yaml) | Navigation | Library / Configured | Represent static and dynamic obstacles. | Completed | Completed | Clyde is now included as a dynamic obstacle source. |
| [Velocity Smoother](https://github.com/GSandys7/PacManBot_ROS2/blob/main/pacmanbot_package/launch/nav2_custom.yaml) | Motion Control | Library / Configured | Smooths velocity commands. | Completed | Completed | Tuned for smoother high-speed navigation. |
| [Collision Monitor](https://github.com/GSandys7/PacManBot_ROS2/blob/main/pacmanbot_package/launch/nav2_custom.yaml) | Safety | Library / Configured | Applies safety constraints near obstacles. | Completed | Completed | Included as a safety layer. |
| Diff-Drive Controller | Actuation | Library | Executes robot velocity commands. | Completed | Completed | No major change. |
| RViz2 | Visualization | Library Application | Visualizes the map, robot pose, costmaps, pellets, Clyde, and game state. | Completed | Completed | Now extended with custom PacManBot game panel. |
| [Sound Effects / `/audio_node`](https://github.com/GSandys7/PacManBot_ROS2/blob/main/pacmanbot_package/pacmanbot_package/audio_node.py) | Game Feedback | Custom | Plays game sounds using Create 3 audio note sequences. | Completed | Completed | Still valid from previous milestone. |
| [Audio Library / `audio_library.py`](https://github.com/GSandys7/PacManBot_ROS2/blob/main/pacmanbot_package/pacmanbot_package/audio_library.py) | Game Feedback | Custom | Stores named MIDI note sequences used by `/audio_node`. | Completed | Completed | Supports centralized sound definitions for game events. |
| [Light Effects / `/game_light_node`](https://github.com/GSandys7/PacManBot_ROS2/blob/main/pacmanbot_package/pacmanbot_package/game_light_node.py) | Game Feedback | Custom | Displays game states using the Create 3 lightring. | Completed | Completed | Still valid from previous milestone. |
| [Game Event Mapper / `/game_event_mapper`](https://github.com/GSandys7/PacManBot_ROS2/blob/main/pacmanbot_package/pacmanbot_package/game_event_mapper.py) | Event Coordination | Custom | Maps high-level events to sound, light, and motion. | Completed | Completed | Still valid from previous milestone. |
| [Pellet Manager / `/pellet_manager`](https://github.com/GSandys7/PacManBot_ROS2/blob/main/pacmanbot_package/pacmanbot_package/pellet_manager.py) | Game World | Custom | Generates, publishes, removes, and resets pellets. | Completed baseline | Completed | Removal and reset behavior now clearly integrated. |
| [Planner Stub / `/planner_stub`](https://github.com/GSandys7/PacManBot_ROS2/blob/main/pacmanbot_package/pacmanbot_package/planner_stub.py) | Strategic Planning | Custom | Selects pellet goals, sends Nav2 goals, handles collection and Clyde risk. | Completed baseline | Completed / Improved | Now includes Clyde-aware scoring, replanning, death detection, and return-home behavior. |
| [Ghost Path Behavior / `/clyde_ghost_node`](https://github.com/GSandys7/PacManBot_ROS2/blob/main/pacmanbot_package/pacmanbot_package/clyde_ghost_node.py) | Game AI | Custom | Moves Clyde through the map and publishes Clyde as a marker and obstacle. | Pending | Completed baseline | Major new module added. |
| [Risk-Reward Robot Path Planning](https://github.com/GSandys7/PacManBot_ROS2/blob/main/pacmanbot_package/pacmanbot_package/planner_stub.py) | Strategic Planning | Custom | Chooses pellets using distance, Clyde risk, and threat direction. | Pending | Completed baseline | Implemented as high-level pellet scoring plus Nav2 costmap avoidance. |
| [Game Controller / `/game_controller`](https://github.com/GSandys7/PacManBot_ROS2/blob/main/pacmanbot_package/pacmanbot_package/game_controller.py) | Game State | Custom | Manages menu, start, running, death, complete, score, round, and reset states. | Not listed | Completed baseline | New major module added. |
| [RViz Game Panel / `pacmanbot_rviz_plugins`](https://github.com/GSandys7/PacManBot_ROS2/tree/main/pacmanbot_rviz_plugins) | User Interface | Custom | Provides start/reset/status controls in RViz. | Not listed | Completed baseline | New interface module added. |
| [RViz Panel Source / `game_panel.cpp`](https://github.com/GSandys7/PacManBot_ROS2/blob/main/pacmanbot_rviz_plugins/src/game_panel.cpp) | User Interface | Custom | Implements the RViz game panel behavior. | Not listed | Completed baseline | New interface module added. |
| [Demo GUI / `/game_state_demo_gui`](https://github.com/GSandys7/PacManBot_ROS2/blob/main/pacmanbot_package/pacmanbot_package/game_state_demo_gui.py) | Testing / UI | Custom | Allows manual testing of game events, lights, and sounds. | Not listed | Completed demo tool | New support tool added. |
| [AMCL Wait Utility / `/wait_for_amcl_pose`](https://github.com/GSandys7/PacManBot_ROS2/blob/main/pacmanbot_package/pacmanbot_package/wait_for_amcl_pose.py) | Startup Support | Custom | Waits for valid localization before game behavior begins. | Not listed | Completed | New startup reliability utility. |
| [Package Entry Points / `setup.py`](https://github.com/GSandys7/PacManBot_ROS2/blob/main/pacmanbot_package/setup.py) | Build / Packaging | Custom Configuration | Registers the executable custom Python nodes. | Not listed | Completed | Confirms installed custom node entry points. |

The OAK-D camera is not listed as an active module because the current PacManBot implementation does not directly use OAK-D image, depth, or point cloud topics. Navigation currently relies on the saved map, AMCL localization, LiDAR scan data, Nav2 costmaps, and the custom Clyde obstacle topic.

Overall, the system has progressed from a functional navigation baseline into a playable game baseline. It can localize on a saved SLAM map, generate pellets in valid map space, visualize pellets in RViz, select pellet targets using Clyde-aware scoring, navigate using Nav2, remove collected pellets, track score, spawn and move Clyde, represent Clyde as a dynamic obstacle, detect Clyde collision, trigger sound and lighting effects, run start/death/win/reset sequences, return home after death or completion, and provide operator controls through GUI and RViz tools.

---

### 2.2 Formal Algorithm Logic

The PacManBot system is modeled as a hybrid autonomy and game-control architecture. The custom modules define the game rules and high-level decision-making, while Nav2 provides lower-level path planning, controller execution, and costmap-based obstacle avoidance.

The system is organized into the following layers:

1. **Game World Layer:** generates pellets and maintains game objects on the map.
2. **Ghost Layer:** controls Clyde, publishes his pose, and inserts him into the navigation costmap.
3. **Game Controller Layer:** manages game state, score, round number, start/reset flow, death condition, and completion condition.
4. **Strategic Planner Layer:** selects pellet goals using distance and Clyde-risk information.
5. **Navigation Layer:** uses Nav2 to plan and execute movement toward selected goals while avoiding static and dynamic obstacles.
6. **Feedback Layer:** maps game events to robot lights, sounds, and theatrical motion effects.
7. **User Interface Layer:** provides game start/reset and demonstration controls through RViz or a GUI.

---

#### 2.2.1 Overall System State

At time step \(t\), the overall PacManBot state is represented as:

$$
s_t =
\left[
\mathbf{x}_r(t),
\mathbf{x}_c(t),
P_t,
G_t,
S_t,
R_t
\right]
$$

where the robot pose estimated by AMCL is:

$$
\mathbf{x}_r(t) =
\begin{bmatrix}
x_r(t) \\
y_r(t) \\
\theta_r(t)
\end{bmatrix}
$$

Clyde's current position is:

$$
\mathbf{x}_c(t) =
\begin{bmatrix}
x_c(t) \\
y_c(t)
\end{bmatrix}
$$

The remaining pellet set is:

$$
P_t = \{p_1, p_2, ..., p_n\}
$$

The terms \(G_t\), \(S_t\), and \(R_t\) represent the current game state, score, and round number.

Each pellet is represented as:

$$
p_i =
\left[
x_i,\ y_i,\ id_i
\right]
$$

where \((x_i,y_i)\) is the pellet position in the map frame and \(id_i\) is the marker identifier used for visualization and removal.

The game state variable is modeled as:

$$
G_t \in
\{
\mathrm{Menu},
\mathrm{ReturningHome},
\mathrm{Starting},
\mathrm{Running},
\mathrm{Dying},
\mathrm{Complete}
\}
$$

---

#### 2.2.2 Pellet Manager Algorithm

The `pellet_manager` node generates game pellets from the saved occupancy grid map. It loads the map image, identifies free space, rejects locations near walls, converts valid pixels into world coordinates, and publishes the remaining pellets as RViz markers.

The saved map image is represented as:

$$
M(u,v)
$$

where \(u\) and \(v\) are pixel coordinates. A map pixel is treated as free space if:

$$
M(u,v) > T_f
$$

where the implemented free-space threshold is:

$$
T_f = 250
$$

A pellet candidate must be far enough from obstacles. Define a square neighborhood around each candidate pixel:

$$
\mathcal{N}_r(u,v)
=
\{(a,b): |a-u| \leq r,\ |b-v| \leq r\}
$$

where \(r\) is the wall-clearance radius in pixels.

A pellet candidate is valid if every cell in the neighborhood is free:

$$
\operatorname{valid}(u,v) =
\begin{cases}
1, & M(a,b) > T_f,\ \forall(a,b) \in \mathcal{N}_r(u,v) \\
0, & \mathrm{otherwise}
\end{cases}
$$

Valid pellet pixels are converted into map-frame world coordinates using:

$$
x = uR + x_0
$$

$$
y = (H-v)R + y_0
$$

where \(R\) is the map resolution in meters per pixel, \((x_0,y_0)\) is the map origin, and \(H\) is the map image height.

The initial pellet set is:

$$
P_0 =
\{(x_i,y_i,id_i): \operatorname{valid}(u_i,v_i)=1\}
$$

During runtime, if pellet \(p_i\) is collected, it is removed from the active pellet set:

$$
P_{t+1} = P_t \setminus \{p_i\}
$$

The `pellet_manager` then republishes the updated marker array so the pellet disappears from RViz and is no longer considered by the planner.

---

#### 2.2.3 Clyde Ghost Algorithm

The `clyde_ghost_node` implements Clyde as a virtual ghost moving through the map. Clyde affects the robot in two ways: Clyde's pose is used by the custom planner as a strategic risk source, and Clyde is published as an obstacle source so Nav2 can avoid him through the local costmap.

Clyde's state is represented as:

$$
\mathbf{x}_c(t) =
\begin{bmatrix}
x_c(t) \\
y_c(t)
\end{bmatrix}
$$

Clyde's current target is:

$$
\mathbf{x}_{c,\mathrm{goal}} =
\begin{bmatrix}
x_{\mathrm{goal}} \\
y_{\mathrm{goal}}
\end{bmatrix}
$$

Clyde is restricted to free map space. A Clyde cell is valid if it satisfies the same general free-space condition used by the pellet manager:

$$
M(u,v) > T_f
$$

A simplified continuous Clyde motion update is:

$$
\mathbf{x}_c(t+\Delta t)
=
\mathbf{x}_c(t)
+
v_c \Delta t
\frac{
\mathbf{x}_{c,\mathrm{goal}} - \mathbf{x}_c(t)
}{
\|\mathbf{x}_{c,\mathrm{goal}} - \mathbf{x}_c(t)\|
}
$$

where \(v_c\) is Clyde's speed, \(\Delta t\) is the update period, and \(\mathbf{x}_{c,\mathrm{goal}}\) is Clyde's current target.

Clyde is represented in the navigation system as a dynamic obstacle region:

$$
C_{\mathrm{clyde}}(t)
=
\{(x,y):
\sqrt{(x-x_c(t))^2 + (y-y_c(t))^2}
\leq r_c
\}
$$

where \(r_c\) is Clyde's obstacle radius.

The complete Nav2 costmap is represented as:

$$
C_t =
C_{\mathrm{static}}
\cup
C_{\mathrm{sensor}}(t)
\cup
C_{\mathrm{clyde}}(t)
$$

where \(C_{\mathrm{static}}\) contains walls and static map obstacles, \(C_{\mathrm{sensor}}(t)\) contains live LiDAR obstacles, and \(C_{\mathrm{clyde}}(t)\) contains Clyde's dynamic obstacle representation.

This allows Nav2 to perform local obstacle avoidance around Clyde without requiring the custom planner to manually generate every avoidance maneuver.

---

#### 2.2.4 Strategic Planner Algorithm

The `planner_stub` node performs high-level pellet selection and sends selected goals to Nav2. It does not directly compute low-level velocity commands. Instead, it chooses the most appropriate pellet target based on distance, Clyde proximity, and Clyde threat direction.

For each pellet \(p_i\), the robot-to-pellet distance is:

$$
d_r(p_i)
=
\sqrt{(x_i-x_r)^2 + (y_i-y_r)^2}
$$

The Clyde-to-pellet distance is:

$$
d_c(p_i)
=
\sqrt{(x_i-x_c)^2 + (y_i-y_c)^2}
$$

Pellets near Clyde receive a risk penalty:

$$
R_c(p_i)
=
\max(0,\ r_g - d_c(p_i))w_g
$$

where \(r_g\) is the ghost-risk radius and \(w_g\) is the ghost-risk weight.

The implemented values are:

$$
r_g = 2.0\ \mathrm{m}
$$

$$
w_g = 3.0
$$

The robot-Clyde distance is:

$$
d_{rc}(t)
=
\sqrt{(x_r(t)-x_c(t))^2 + (y_r(t)-y_c(t))^2}
$$

Clyde's closing speed is approximated by:

$$
v_{\mathrm{close}}(t)
=
\frac{d_{rc}(t-\Delta t)-d_{rc}(t)}{\Delta t}
$$

Clyde is considered a threat if he is very close or if he is approaching within a warning radius:

$$
\operatorname{threat}(t)
=
\begin{cases}
1, & d_{rc}(t) \leq r_e \\
1, & d_{rc}(t) \leq r_w \ \mathrm{and}\ v_{\mathrm{close}}(t) \geq v_{\min} \\
0, & \mathrm{otherwise}
\end{cases}
$$

The implemented values are:

$$
r_e = 2.0\ \mathrm{m}
$$

$$
r_w = 3.0\ \mathrm{m}
$$

$$
v_{\min} = 0.03\ \mathrm{m/s}
$$

When Clyde is threatening, the planner penalizes pellets that lie in the same general direction as Clyde. Let:

$$
\mathbf{v}_{rc}
=
\begin{bmatrix}
x_c-x_r \\
y_c-y_r
\end{bmatrix}
$$

and:

$$
\mathbf{v}_{rp_i}
=
\begin{bmatrix}
x_i-x_r \\
y_i-y_r
\end{bmatrix}
$$

The directional alignment is:

$$
A(p_i)
=
\frac{
\mathbf{v}_{rc} \cdot \mathbf{v}_{rp_i}
}{
\|\mathbf{v}_{rc}\|\|\mathbf{v}_{rp_i}\|
}
$$

The direction penalty is:

$$
D(p_i)
=
\begin{cases}
w_d(1+A(p_i)), & \operatorname{threat}(t)=1 \ \mathrm{and}\ A(p_i)>0 \\
0, & \mathrm{otherwise}
\end{cases}
$$

where the implemented direction penalty weight is:

$$
w_d = 5.0
$$

The complete score for each pellet is:

$$
J(p_i)
=
d_r(p_i)
+
R_c(p_i)
+
D(p_i)
$$

The selected pellet is:

$$
p^*
=
\arg\min_{p_i \in P_t} J(p_i)
$$

After selecting a pellet, the planner sends it to Nav2 as a `NavigateToPose` goal:

$$
g_t = p^*
$$

The lower-level Nav2 behavior can be represented as:

$$
u_t =
\pi_{\mathrm{Nav2}}(\mathbf{x}_r(t), g_t, C_t)
$$

where \(u_t\) is the velocity command, \(\pi_{\mathrm{Nav2}}\) is the Nav2 navigation policy, \(\mathbf{x}_r(t)\) is the robot pose, \(g_t\) is the selected pellet goal, and \(C_t\) is the active costmap.

The planner only replans if a new pellet is better than the current target by a margin:

$$
J(p_{\mathrm{new}}) + m < J(p_{\mathrm{current}})
$$

where:

$$
m = 0.3
$$

A pellet is considered collected if the robot is within the pickup radius:

$$
\operatorname{collected}(p_i)
=
\begin{cases}
1, & d_r(p_i) \leq r_p \\
0, & d_r(p_i) > r_p
\end{cases}
$$

The implemented pickup radius is:

$$
r_p = 0.25\ \mathrm{m}
$$

The robot is caught by Clyde when:

$$
d_{\mathrm{death}}(t)
=
\sqrt{(x_r(t)-x_c(t))^2 + (y_r(t)-y_c(t))^2}
$$

and:

$$
d_{\mathrm{death}}(t) \leq r_{\mathrm{death}}
$$

The implemented death radius is:

$$
r_{\mathrm{death}} = 0.25\ \mathrm{m}
$$

The game transition is:

$$
G_{t+1}
=
\begin{cases}
\mathrm{Death}, & d_{\mathrm{death}}(t) \leq r_{\mathrm{death}} \\
G_t, & \mathrm{otherwise}
\end{cases}
$$

---

#### 2.2.5 Game Controller Algorithm

The `game_controller` node manages the overall game state. This separates game logic from navigation logic. The planner only runs when the game controller publishes the command to enable autonomy.

The main game-state transition is:

$$
\mathrm{Menu}
\rightarrow
\mathrm{ReturningHome}
\rightarrow
\mathrm{Starting}
\rightarrow
\mathrm{Running}
$$

During the running state, two main terminal conditions are monitored:

$$
\mathrm{Running}
\rightarrow
\mathrm{Complete}
\quad \mathrm{if} \quad |P_t| = 0
$$

$$
\mathrm{Running}
\rightarrow
\mathrm{Dying}
\quad \mathrm{if} \quad d_{\mathrm{death}}(t) \leq r_{\mathrm{death}}
$$

After death, the robot returns home:

$$
\mathrm{Dying}
\rightarrow
\mathrm{ReturningHome}
\rightarrow
\mathrm{Menu}
$$

After round completion, the robot also returns home and prepares the next round:

$$
\mathrm{Complete}
\rightarrow
\mathrm{ReturningHome}
\rightarrow
\mathrm{Menu}
$$

When a pellet is collected:

$$
S_{t+1} = S_t + R_p
$$

where \(R_p\) is the pellet reward. In the current implementation:

$$
R_p = 1
$$

The number of collected pellets updates as:

$$
N_{\mathrm{collected}}(t+1)
=
N_{\mathrm{collected}}(t)+1
$$

The game is complete when:

$$
N_{\mathrm{collected}}(t) \geq N_{\mathrm{spawned}}
$$

The game controller increases Clyde's speed as rounds progress. Clyde's speed for round \(R_t\) is:

$$
v_c(R_t)
=
v_{\mathrm{base}}
+
(R_t-1)v_{\mathrm{step}}
$$

where:

$$
v_{\mathrm{base}} = 0.25\ \mathrm{m/s}
$$

and:

$$
v_{\mathrm{step}} = 0.05\ \mathrm{m/s}
$$

When the start service is called, the game controller resets round variables, commands the planner to return home, starts the theatrical start sequence, waits for Nav2 to be available, enables the planner, and transitions into the running state. When the reset service is called, the game controller disables the planner, resets pellets, resets Clyde, clears score and pellet counters, and returns the game state to menu.

---

#### 2.2.6 Game Event Mapper Algorithm

The `game_event_mapper` node converts high-level game events into coordinated light, sound, and motion commands. It acts as an event dispatcher.

A game event is represented as:

$$
e_t \in E
$$

where:

$$
E =
\{
\mathrm{Start},
\mathrm{StartTheatrical},
\mathrm{Pellet},
\mathrm{PowerPellet},
\mathrm{ClydeKilled},
\mathrm{Death},
\mathrm{GameOver},
\mathrm{Win},
\mathrm{Reset}
\}
$$

The event mapper applies a function:

$$
f_{\mathrm{event}}: E \rightarrow (L_t, A_t, M_t)
$$

where \(L_t\) is the light command, \(A_t\) is the audio command, and \(M_t\) is the optional motion behavior.

Examples include:

$$
f_{\mathrm{event}}(\mathrm{Pellet})
=
(\mathrm{PelletLight},\ \mathrm{PelletSound},\ \varnothing)
$$

$$
f_{\mathrm{event}}(\mathrm{Death})
=
(\mathrm{GameOverLight},\ \mathrm{DeathSound},\ \mathrm{ShakeMotion})
$$

$$
f_{\mathrm{event}}(\mathrm{StartTheatrical})
=
(\mathrm{StartLights},\ \mathrm{ThemeSound},\ \mathrm{SpinMotion})
$$

For start spin:

$$
\omega_z(t) =
\omega_{\mathrm{start}}
$$

where:

$$
\omega_{\mathrm{start}} = 1.5\ \mathrm{rad/s}
$$

for a duration of:

$$
T_{\mathrm{start}} = 8.0\ \mathrm{s}
$$

For death shake, the angular velocity alternates sign:

$$
\omega_z(t)
=
(-1)^k \omega_{\mathrm{shake}}
$$

where:

$$
\omega_{\mathrm{shake}} = 1.5\ \mathrm{rad/s}
$$

and the direction flips every:

$$
T_{\mathrm{flip}} = 0.25\ \mathrm{s}
$$

---

#### 2.2.7 Game Light Node Algorithm

The `game_light_node` converts simple string commands into Create 3 lightring patterns. A lightring command is represented as:

$$
L_t \in
\{
\mathrm{Start},
\mathrm{GameOver},
\mathrm{PowerPellet},
\mathrm{Win},
\mathrm{Pellet},
\mathrm{ClydeCaught},
\mathrm{Off},
\mathrm{SolidColor}
\}
$$

The lightring has six LEDs, so the output light state is:

$$
\Lambda_t =
\{
\ell_1,\ell_2,\ell_3,\ell_4,\ell_5,\ell_6
\}
$$

Each LED is represented as an RGB vector:

$$
\ell_i =
\begin{bmatrix}
r_i \\
g_i \\
b_i
\end{bmatrix}
$$

A static light command maps directly to a constant LED pattern:

$$
\Lambda_t =
\{ \ell,\ell,\ell,\ell,\ell,\ell \}
$$

An animated command is represented as a sequence of frames:

$$
A =
\{\Lambda_1,\Lambda_2,...,\Lambda_k\}
$$

The animation timer publishes frames in order:

$$
\Lambda(t) = A[j]
$$

where:

$$
j = t \bmod k
$$

for looping animations.

---

#### 2.2.8 Audio Node Algorithm

The `audio_node` converts named sound commands into Create 3 audio note sequences. A sound command is represented as:

$$
A_t \in
\{
\mathrm{Start},
\mathrm{PacmanTheme},
\mathrm{Pellet},
\mathrm{Powerup},
\mathrm{Death},
\mathrm{Win}
\}
$$

Each sound is stored as a sequence of MIDI notes and durations:

$$
Q =
\{(m_1,\tau_1),(m_2,\tau_2),...,(m_n,\tau_n)\}
$$

where \(m_i\) is a MIDI note number and \(\tau_i\) is the note duration.

The MIDI note is converted into frequency using:

$$
f(m)
=
440 \cdot 2^{\frac{m-69}{12}}
$$

The final audio command is:

$$
A_t =
\{(f(m_1),\tau_1),(f(m_2),\tau_2),...,(f(m_n),\tau_n)\}
$$

A busy flag prevents multiple sound sequences from overlapping:

$$
\operatorname{play}(A_t)
=
\begin{cases}
1, & \mathrm{busy}=0 \\
0, & \mathrm{busy}=1
\end{cases}
$$

---

#### 2.2.9 GUI and RViz Control Logic

The `game_state_demo_gui` provides manual buttons for publishing game events, light commands, and sound commands. The GUI applies a simple command-publishing function:

$$
u_{\mathrm{gui}} \rightarrow e_t
$$

where \(u_{\mathrm{gui}}\) is a user button press and \(e_t\) is the resulting game event or command.

The custom RViz panel provides integrated game controls and status visualization. It allows the operator to start or reset the game and view game progress, score, and round information while also seeing the robot, map, pellets, and Clyde in RViz.

The UI layer does not directly control low-level robot motion. Instead, it interacts with the game controller through services and topics:

$$
u_{\mathrm{rviz}}
\rightarrow
\mathrm{GameServiceCall}
\rightarrow
G_{t+1}
$$

---

#### 2.2.10 AMCL Wait Utility Logic

The `wait_for_amcl_pose` utility node supports system startup by waiting until localization is available. Since many game behaviors require a valid robot pose, dependent nodes should not begin active behavior until AMCL has produced a pose estimate.

This requirement is written as:

$$
\mathbf{x}_r(t) \neq \varnothing
$$

Only after receiving a valid pose can the game safely initialize the home position and begin autonomous navigation.

---

#### 2.2.11 Complete System Algorithm

The complete PacManBot custom algorithm is summarized below.

1. Load the saved occupancy map \(M\).
2. Generate the initial pellet set \(P_0\) from valid free-space cells.
3. Initialize Clyde in valid map space.
4. Wait for AMCL to provide robot pose \(\mathbf{x}_r(t)\).
5. Initialize the game controller in the menu state.
6. When the start command is received:
   1. Disable planner autonomy.
   2. Reset pellets and Clyde.
   3. Command the robot to return home.
   4. Trigger the theatrical start event.
   5. Enable planner autonomy.
   6. Set \(G_t = \mathrm{Running}\).
7. While \(G_t = \mathrm{Running}\):
   1. Update robot pose \(\mathbf{x}_r(t)\).
   2. Update Clyde pose \(\mathbf{x}_c(t)\).
   3. Publish Clyde as a costmap obstacle \(C_{\mathrm{clyde}}(t)\).
   4. Update remaining pellet set \(P_t\).
   5. For each pellet \(p_i \in P_t\), compute score \(J(p_i)\).
   6. Select \(p^* = \arg\min_{p_i \in P_t} J(p_i)\).
   7. Send \(p^*\) to Nav2 as a navigation goal.
   8. Nav2 computes robot motion using \(C_t = C_{\mathrm{static}} \cup C_{\mathrm{sensor}}(t) \cup C_{\mathrm{clyde}}(t)\).
   9. If the robot enters a pellet pickup radius, remove the pellet.
   10. Update score and publish the pellet event.
   11. If Clyde catches the robot, publish the death event and disable autonomy.
   12. If all pellets are collected, publish the win event and disable autonomy.
8. Map game events to lights, sounds, and optional motion effects.
9. After death or completion, return the robot home.
10. Wait for reset or next start command.

---

#### 2.2.12 Module Interaction Summary

The custom modules work together through ROS 2 topics, services, and actions.

| Module | Inputs | Outputs | Algorithmic Role |
|---|---|---|---|
| `pellet_manager` | Map image, remove/reset requests | Pellet markers | Generates and maintains pellet world model. |
| `clyde_ghost_node` | Map image, reset/speed commands | Clyde pose, marker, obstacle cloud | Simulates ghost motion and dynamic obstacle behavior. |
| `planner_stub` | AMCL pose, pellets, Clyde pose, game commands | Nav2 goals, pellet removal, game status | Selects goals and monitors collection/death. |
| `game_controller` | Game status, pellet markers, start/reset services | Game commands, game state, score, round, Clyde speed | Controls full game state machine. |
| `game_event_mapper` | Game events | Light commands, sound commands, motion commands | Converts abstract events into robot behaviors. |
| `game_light_node` | Light commands | Create 3 lightring messages | Produces visual game feedback. |
| `audio_node` | Sound commands | Create 3 audio note actions | Produces audio game feedback. |
| `game_state_demo_gui` | User button presses | Manual game/light/sound commands | Supports testing and demonstration. |
| `wait_for_amcl_pose` | AMCL pose | Startup readiness | Ensures localization is available before gameplay. |
| `pacmanbot_rviz_plugins` | User input/game topics | Start/reset/status interface | Provides integrated RViz control and monitoring. |

---

#### 2.2.13 Algorithm Summary

The PacManBot system is a hybrid autonomy and game-control architecture. The custom software does not attempt to replace existing ROS 2 navigation libraries. Instead, it adds a game-specific intelligence layer above Nav2.

The `pellet_manager` creates the game objectives from the map. The `clyde_ghost_node` creates a moving ghost that acts both as a game threat and as a dynamic navigation obstacle. The `planner_stub` selects pellet goals using distance and Clyde-risk scoring, while Nav2 handles path planning and local obstacle avoidance. The `game_controller` manages the overall game state, including start, reset, score, round progression, death, and completion. The `game_event_mapper`, `game_light_node`, and `audio_node` translate game events into physical feedback through robot lights, sound, and motion.

This division of responsibility allows the system to combine custom Pac-Man game logic with robust library-based robot navigation.
---

## 3. Benchmarking & Results

The sidebar will automatically highlight the section you are currently viewing.

### 3.1 Observations of Pacman Success 
We wanted to evaluate the rate of success of the first round of the Pacman game under round 1 conditions. This data was a simple log of how many pellets were collected for each trial. This was to evaluate the escape and pathing behavior of pacman for 1 Ghost (Clyde).

![Pellets Collected Per Trial]({{ site.baseurl }}/assets/images/Pellects_Collected_Per_Trial.png)

![Pellet Collection Frequency Chart]({{ site.baseurl }}/assets/images/Pellet_Collection_Frequency_Chart.png)

### 3.2 Conclusion
Under the baseline conditions for Clydes speed (0.25m/s), which is initialized for round 1, Pacman was able to complete the first round 30% of the time. However this rate of success can be improved given the qualitative notes that were taken during these trials. 30% of the trials only collected 7 pellets as the final pellet was located very far away from the robots starting position. Additionally, it seems like the last pellet was located in an area of the house that could easily be "Puppy Guarded" by Clyde. An image below shows this foul play.

![Puppy Guarding]({{ site.baseurl }}/assets/images/Puppy_Guarding.png)

This made the reliability of the custom Ghost-Aware path planning algorithm faulty. However if we exclude the last pellet collection, the path planning success rate jumps to 60%.

---

## 4.Ethical Impact Statement

### Privacy and Data Handling
PacManBot uses ROS 2 topics, localization data, map files, and logged robot data to operate and evaluate performance. In this implementation, the main privacy concern is not facial recognition, but the storage of indoor environment information through maps, AMCL pose logs, trajectory logs, and demo recordings. The map used for navigation may reveal the layout of an indoor space, and ROS logs may record where the TurtleBot 4 moved during testing. Future versions that add cameras or cloud connectivity would increase privacy risk and should include data minimization, local-only processing, restricted access to logs, and blurring/anonymization for any recorded video.

### Bias and Hardware Limitations
PacManBot depends heavily on TurtleBot 4 localization, LiDAR sensing, maps, and Nav2 navigation. This creates hardware-related bias because the robot may perform well in clear, mapped environments but poorly around glass, reflective surfaces, clutter, narrow passages, or areas where LiDAR returns are unreliable. AMCL localization may also become inaccurate if the map does not match the real environment. This means the robot’s performance is not equally reliable in all spaces.

### Safety and Kinetic Energy Management
PacManBot moves autonomously using the TurtleBot 4 platform, ROS 2 Nav2, AMCL localization, and goal commands from the planner node. The main safety risk is unintended robot motion, such as navigating toward an incorrect pellet goal, localization drift, delayed stopping, or collision with walls, objects, or nearby users. The package also includes event-based motion in game_event_mapper.py, such as spinning or shaking during start/death events, so these motions should remain slow and time-limited.
 The project primarily relies on TurtleBot 4 navigation and Nav2 path planning to generate controlled robot movement rather than directly commanding unrestricted motion. The system sends navigation goals through ROS 2 action interfaces, allowing the Nav2 stack to manage path execution and obstacle-aware navigation. Additionally, custom game-event motion behaviors were designed with safety considerations in mind, avoiding forward linear motion during spin and animation sequences to reduce the likelihood of unintended impacts with nearby objects or users. Stop commands are also published during reset and shutdown operations to ensure the robot safely halts movement when behaviors complete.

### Bias and Hardware Limitations
PacManBot depends heavily on TurtleBot 4 localization, LiDAR sensing, maps, and Nav2 navigation. This creates hardware-related bias because the robot may perform well in clear, mapped environments but poorly around glass, reflective surfaces, clutter, narrow passages, or areas where LiDAR returns are unreliable. AMCL localization may also become inaccurate if the map does not match the real environment. This means the robot’s performance is not equally reliable in all spaces.

### Utilitarian Test
From a utilitarian perspective, PacManBot is ethically justified because it provides educational value in ROS 2, Nav2, localization, autonomous planning, and human-robot interaction. However, these benefits only outweigh the risks if the system is tested safely, robot speed is controlled, and collected data is handled responsibly. In the scenario where there is no oversight with testing, it would fail the utilitarian test since the potential exposure of people/places would be detrimental.

### Justice Test
From a justice perspective, the robot should operate reliably across different indoor environments. Since LiDAR, AMCL, and maps can fail under certain conditions, the system may be less reliable in spaces with glass, reflective objects, or poor map quality. Future work should improve robustness through better testing, sensor fusion, and clearer documentation of limitations. Based on the potential inconsistency in certain environmental conditions, it would mostly fail this test.

### Virtue Test
From a virtue ethics perspective, the engineers should be honest about what the robot can and cannot do. For this project, that means clearly reporting Nav2 failures, localization drift, pellet-planning limitations, and safety risks instead of overstating performance. Responsible engineers should prioritize safe testing, transparent results, and continuous improvement. Since testing used LiDAR obstacle detection, reactive navigation, controlled robot movement, and indoor testing, it would pass the virtue test.


You can include images by placing them in the `assets/images/` folder.

![Alt text](../assets/images/logo.png){: width="500" }

*Figure 1: Class Logo*

---

## 5. Custom Module Code Links

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
