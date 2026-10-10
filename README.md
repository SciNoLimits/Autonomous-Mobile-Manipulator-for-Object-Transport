# MOMENT: Mobile Manipulation for Exploration, Navigation, and Transport

MOMENT is an autonomous mobile manipulation system designed to explore unknown environments, identify objects and destination locations, navigate dynamically changing surroundings, and transport objects to their designated destinations without relying on predefined paths.

## Inspiration

https://github.com/user-attachments/assets/d99a6a93-bf74-4fcf-906d-5d0776c2f01a

Source: [Engr Programmer](https://www.youtube.com/@engrprogrammer)

## MOMENT Setup Guide

This repository contains the ROS 2 description package for the MOMENT robot, combining a TurtleBot3 Waffle Pi mobile base with an OpenMANIPULATOR-X arm and gripper.

### Requirements

- Ubuntu 24.04 LTS
- ROS 2 Jazzy Jalisco
- `git`
- `python3-rosdep`
- `python3-colcon-common-extensions`
- `xacro`
- RViz 2

The package is intended to be used with the ROS 2 Jazzy distribution. Install and source ROS 2 before building the workspace.

### 1. Install ROS 2 dependencies

If `rosdep` has not been initialized on your system, run:

```bash
sudo rosdep init
rosdep update
```

Install the tools required to build and visualize the robot description:

```bash
sudo apt update
sudo apt install -y \
  git \
  python3-rosdep \
  python3-colcon-common-extensions \
  ros-jazzy-xacro \
  ros-jazzy-robot-state-publisher \
  ros-jazzy-joint-state-publisher-gui \
  ros-jazzy-rviz2
```

Source ROS 2 Jazzy in the current terminal:

```bash
source /opt/ros/jazzy/setup.bash
```

To source ROS 2 automatically in future terminals, add the following line to `~/.bashrc`:

```bash
source /opt/ros/jazzy/setup.bash
```

### 2. Create a ROS 2 workspace

Create the workspace and clone MOMENT into its `src` directory:

```bash
mkdir -p ~/moment_ws/src
cd ~/moment_ws/src
git clone https://github.com/SciNoLimits/moment.git
```

The expected workspace layout is:

```text
~/moment_ws/
└── src/
    └── moment/
        └── moment_description/
```

### 3. Install package dependencies

From the workspace root, use `rosdep` to resolve dependencies:

```bash
cd ~/moment_ws
rosdep install --from-paths src --ignore-src -r -y
```

### 4. Build the workspace

Build the MOMENT description package with `colcon`:

```bash
cd ~/moment_ws
source /opt/ros/jazzy/setup.bash
colcon build --symlink-install
```

Source the workspace after a successful build:

```bash
source ~/moment_ws/install/setup.bash
```

For convenience, source the workspace automatically in new terminals:

```bash
echo "source ~/moment_ws/install/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

### 5. Launch the robot description in RViz

Start the MOMENT visualization launch file:

```bash
ros2 launch moment_description moment_rviz.launch.py
```

This launch file starts:

- `robot_state_publisher`, which publishes the robot's transforms and URDF description.
- `joint_state_publisher_gui`, which provides sliders for inspecting arm joints.
- `rviz2`, which visualizes the robot model and its coordinate frames.

If the robot is not visible in RViz, confirm that the fixed frame is set to `base_link` and that the **RobotModel** display is enabled.

### 6. Verify the package

Confirm that ROS 2 can discover the package:

```bash
ros2 pkg prefix moment_description
```

Inspect the available launch file:

```bash
ros2 launch moment_description --show-args moment_rviz.launch.py
```

You can also validate the Xacro model directly:

```bash
ros2 run xacro xacro \
  ~/moment_ws/src/moment/moment_description/urdf/moment.urdf.xacro \
  > /tmp/moment.urdf
```

### Troubleshooting

#### `Package 'moment_description' not found`

Source both ROS 2 and the workspace in the same terminal, then retry:

```bash
source /opt/ros/jazzy/setup.bash
source ~/moment_ws/install/setup.bash
```

If the package still cannot be found, rebuild the workspace from its root:

```bash
cd ~/moment_ws
colcon build --symlink-install --packages-select moment_description
```

#### `xacro` or `robot_state_publisher` is missing

Install the missing ROS 2 package and rebuild if necessary:

```bash
sudo apt install -y ros-jazzy-xacro ros-jazzy-robot-state-publisher
```

#### Meshes or included Xacro files cannot be found

Make sure the package was built with the repository inside `~/moment_ws/src/moment` and that the workspace was sourced after building. The package installs its `urdf`, `meshes`, `rviz`, and `launch` directories into the package share directory during the build.

#### RViz opens but the model is not displayed

Check that:

1. `robot_state_publisher` is running without errors.
2. The RViz **Fixed Frame** is set to `base_link`.
3. The **RobotModel** display is enabled.
4. The Xacro validation command completes without errors.

## Package Contents

- `moment_description/urdf/` - MOMENT robot URDF and Xacro files.
- `moment_description/meshes/` - Visual and collision meshes.
- `moment_description/launch/` - ROS 2 launch files.
- `moment_description/rviz/` - RViz configuration files.

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.



