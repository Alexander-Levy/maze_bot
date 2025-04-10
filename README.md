# Autonomous Maze Solver Diferential Drive Robot 

## Introduction

This is a ROS2 based project that is built to work on: 

 - [Ubuntu 22.04 LTS](https://releases.ubuntu.com/jammy/)
 - [ROS2 Humble Hawksbill](https://docs.ros.org/en/rolling/Releases/Release-Humble-Hawksbill.html)
 - [Gazebo Fortress](https://gazebosim.org/docs/fortress/ros_installation/)

The processing of data is distributed between a Raspberry Pi running on the robot and a laptop running on the same network

## How the package is organized

 - `config:` contains all of the necesary configuration files. This includes rviz views, parameters for the robot controller, slam and navigation, as well as configuration for the tele-operation and multiplexer. 

 - `description:` contains the robot's description written in xacro. Contains information about the robots dimensions, mass, actuators, sensors, etc. Also contains gazebo plugins for simulation purposes

 - `launch:` contains the launch files to startup the robot, the robot sensors, slam, navigation and simulation.

 - `worlds:` contains the world files used in simulation. An empty world, a world full of obstacles and a world where the robot spawns inside four walls.

## Usage
The package is designed with versitility in mind. It provides three main launch files to activate all of it funtionality both in simulation and real world use cases, these are:
```bash
ros2 launch maze_bot sim.launch.py      / launches the simulation
ros2 launch maze_bot maze.launch.py    / launches the control system of the robot
ros2 launch maze_bot monitor.launch.py  / launches rviz, slam and navigation
```
We will go over these in more detail in the following sections. 

### Simulation
The simulation can be run with the following command:
```bash
ros2 launch maze_bot sim.launch.py 
```

The simulation launch file offers serveral launch configurations to modify its behaviour, this is useful for testing. You can decide if the simulation(Gazebo) is visualized using the `headless` launch argument, the `world` launch argument lets you provide a path to a custom world, the `rviz` launch argument can be used to open rviz2 alongside the simulation, the `slam` launch argument can be used to decide if the robot maps its enviroment, and the `nav` launch argument can be used to automatically launch the navigation stack. Example with custom launch arguments:
```bash
ros2 launch maze_bot sim.launch.py world:=<path_to_world> headless:=False rviz:=True slam:=True nav:=True
```

By default the box world is loaded, the simulation is not visualized, and rviz2, slam and the navigation stack are launched as well. All of the launch configurations available are listed below:
```bash
Config      Options            Default                           
world:=     <path_to_world>    /src/maze_bot/worlds/empty.world 
headless:=  True/False         True
rviz:=      True/False         True
slam:=      True/False         True
nav:=       True/False         True
```

### Real World Use
The robot can be initialized with the following command on the Rasberry Pi. This will launch the robot state publisher, the joint state broadcaster, the rplidar driver, the controller manager and differential drive controller. It provides no launch configurations.
```bash
ros2 launch maze_bot maze.launch.py 
```

To monitor, control and launch the high level interfaces of the robot we can use the following command on the Laptop. This is will open up rviz, launch the slam and navigation stacks and allow us to tele-operate the robot with a controller.
```bash
ros2 launch maze_bot monitor.launch.py 
```

You can even use the monitor launch file in simulation by setting the `use_sim_time` launch argument to true. It also has `rviz`, `slam` and `nav` launch arguments, similar to the simulation launch file. This gives you great flexibility, for example you could use this to only open rviz2 and the controller tele-op with the real robot with the command bellow:
```bash
ros2 launch maze_bot monitor.launch.py use_sim_time:=false rviz:=True slam:=False nav:=False
```

By default use_sim_time is set to false, rviz, slam and nav are set to True. All of the launch configurations available are listed below:
```bash
Config          Options            Default 
use_sim_time:=  true/false         false
rviz:=          True/False         True
slam:=          True/False         True
nav:=           True/False         True
```


## Dependencies
Since the load is distributed between two machines, some packages are only used by one of them so its not necesarry to install everything on both machines. I will make it clear which pacakges are used by what.

### General Dependencies:

#### These packages
```bash
sudo apt install -y                         \
    joystick                                \
    jstest-gtk evtest                       \ 
    python3-argcomplete                     \
    software-properties-common              \
    python3-colcon-common-extensions        \
```
#### These ROS2 packages:
```bash
sudo apt install -y                         \
    ros-humble-xacro                        \
    ros-humble-twist-mux                    \
    ros-humble-navigation2                  \
    ros-humble-slam-toolbox                 \
    ros-humble-ros2-control                 \
    ros-humble-ros2-controllers             \
    ros-humble-gazebo-ros-pkgs              \
    ros-humble-gazebo-ros2-control          \
    ros-humble-joint-state-publisher-gui    \
```

### Rasberry Pi dependency only: 
#### This Package
```bash
sudo apt install -y                         \
    libraspberrypi-bin                      \
```
#### diffdrive_arduino  
Go into your workspace, clone the files and build 
```bash
cd ~/<path_to_your_workspace>/src
git clone https://github.com/joshnewans/diffdrive_arduino.git -b humble
cd ..
colcon build --symlink-install
```
#### rplidar_ros
Go into your workspace, clone the files and build
```bash
cd ~/<path_to_your_workspace>/src
git clone -b ros2 https://github.com/Slamtec/rplidar_ros.git
cd ..
colcon build --symlink-install
source ./install/setup.bash
```

## Install
To use this package please download all of the necesary dependencies first and then follow these steps
```bash
cd ~/<path_to_your_workspace>/src
git clone https://github.com/Alexander-Levy/maze_bot.git 
cd ..
colcon build --symlink-install
```

Launch the simulation to test the package
```bash
source ./install/setup.bash
ros2 launch maze_bot sim.launch.py 
```