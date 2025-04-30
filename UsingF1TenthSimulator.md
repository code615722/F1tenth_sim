# Using F1Tenth ROS 2 Simulator
The F1Tenth Gym ROS simulator displays a rviz2 visualization of the car in an environment. The simulator repo can be found [here](https://github.com/f1tenth/f1tenth_gym_ros?tab=readme-ov-file). While that repo provides a keyboard teleop node that can manually drive the car around, it does not have autonomous driving examples. This document explains how to run code written at the University of Waterloo to autonomously drive the car that is provided [here](https://github.com/CL2-UWaterloo/f1tenth_ws).

## Rocker
Rocker is used to facilitate using Docker containers with GUI forwarding. In order to follow the instructions below, ensure that rocker is either installed system-wide or within a virtual environment that is activated. Rocker can be installed [here](https://github.com/osrf/rocker).

## Simulation Maps
The maps needed for simulation lie in ``/sim_ws/src/particle_filter/maps``. However, the ``particle_filter`` directory is forked from another repository. In the directory for the f1tenth_ws repository run the following commands to ensure the maps can be located and used:
```
git submodule update --init --recursive
```

## Notes on f1tenth_ws
The [Getting Started in Simulation](https://github.com/CL2-UWaterloo/f1tenth_ws?tab=readme-ov-file#getting-started-in-simulation) section on the f1tenth_ws repository has information for launching the Docker container necessary to run simulations.

## Running Autonomous Driving Algorithms
The instructions below provide information on how to run the Pure Pursuit (one agent) and the Stanley Avoidance (one or two agents) algorithms in simulation.

The first step is to provide the correct path to the map that will be used for the algorithm to be run. Depending on which algorithm you are running, see the corresponding section below on how to do this.

### Launching rviz
After changing the map navigate back to ``/sim_ws`` and run the following commands:
```
source /opt/ros/foxy/setup.bash
source install/local_setup.bash
ros2 launch f1tenth_gym_ros gym_bridge_launch.py
```

### Prepare to run driving algorithms
Open a new terminal, change to ``/sim_ws/src`` and run the following commands:
```
source /opt/ros/foxy/setup.bash
colcon build
. install/setup.bash
```

Finally, see the section for the algorithm that you want to run for the launch command.

### Pure Pursuit Simulation
#### Changing the map
Within the Docker container navigate to ``/sim_ws/install/f1tenth_gym_ros/share/f1tenth_gym_ros/config`` and execute the following command:
```
nano sim.yaml
```
With the sim.yaml file open make the following changes:
- Make ``map_path: '/sim_ws/src/particle_filter/maps/e7_floor5'``
- Make ``map_img_ext: '.pgm'``

#### Launching pure pursuit
After building the workspace and sourcing the underlay and overlay you can run the algorithm with the following command:
```
ros2 launch pure_pursuit sim_pure_pursuit_launch.py
```

### Stanley Avoidance Simulation
Make the following changes in the sim.yaml file:
- Make ``map_path: '/sim_ws/src/particle_filter/maps/Silverstone_map'``
- Make ``map_img_ext: '.png'``

After building the workspace and sourcing the underlay and overlay run the following command:
```
ros2 launch stanley_avoidance sim_stanley_avoidance_launch.py
```

#### Running Stanley Avoidance with multiple agents
If wanting to run Stanley Avoidance with multiple agents then make the following change in sim.yaml:
- Make ``num_agent: 2``  

You also have to make sure the opp starts within the bounds of the map which can be done by setting the following within sim.yaml:
- Make ``sx1: 1.0``
- Make ``sy1: 1.0``

Changes must also be made within RViz after launching the simulation. Click "Add" in the bottom left and under the "By display type" tab add a new RobotModel. It should appear in the left sidebar with the other Displays. Expand the dropdown for the RobotModel and set "Description Topic" to ``/opp_robot_description``.

Perform similar steps to add a new LaserScan if you want to see the LiDAR for the opp. Expand the dropdown for the LaserScan and set "Topic" to ``/opp_scan``. The "Size (m)" can be changed to 0.1 to match the ego.

Instead of using ``sim_stanley_avoidance_launch.py`` use ``sim_multi_agent_stanley_avoidance_launch.py``
