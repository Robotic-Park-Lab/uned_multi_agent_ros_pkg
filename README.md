# uned_multi_agent_ros_pkg

> 📖 To understand this repo's branches and its contribution guide, see the [`doc`](https://github.com/Robotic-Park-Lab/uned_multi_agent_ros_pkg/tree/doc) branch.

Repository for multi-agent functionality development in Robotic Park Lab: configuration and launch files that combine robots from different repositories (Crazyflie 2.1, Khepera IV, LIMO, DJI Tello...), and the task/control nodes that coordinate several agents at once. It was called `uned_swarm_ros_pkg` until 2026-08; it was renamed because the focus isn't exclusive to UAV swarms.

#### Structure
- **scripts**. Auxiliary files that are not part of any ROS package.
- **[uned_swarm_config](uned_swarm_config/README.md)**. ROS 2 package (`ament_cmake`). Launch and environment configuration for multi-agent experiments: `launch/generic.launch.py`, `launch/AC10_Sensor_Gazebo.launch.py`, `launch/AC10_Sensor_Webots.launch.py`.
- **[uned_swarm_driver](uned_swarm_driver/README.md)**. ROS 2 package (`ament_python`). Robot-side driver nodes used in multi-agent experiments: `swarm_driver` (centroid estimation over a set of robots already driven by something else), `tello_gazebo_driver` (DJI Tello formation control in Gazebo, via the `tello_msgs/TelloAction` service), `turtlebot_driver` (TurtleBot3 position control).
- **[uned_swarm_task](uned_swarm_task/README.md)**. ROS 2 package (`ament_python`). Coordination nodes: `centralized_formation_controller`, `system_identification`, `open_loop_signal`, `swarm_reconfiguration`.

> Internal packages are still named `uned_swarm_*` — the repo rename hasn't (yet) carried over to the ROS packages inside it, to avoid silently breaking existing references.

## Installation :book:
```
mkdir -p ~/multi_agent_ws/src
cd ~/multi_agent_ws/src
git clone -b humble-dev https://github.com/Robotic-Park-Lab/uned_multi_agent_ros_pkg.git
cd ..
colcon build --symlink-install --packages-select uned_swarm_config uned_swarm_driver uned_swarm_task
source install/setup.bash
```

## Usage 🔧
```
ros2 launch uned_swarm_config generic.launch.py config_file:=<experiment>.yaml
```
```
ros2 run uned_swarm_task centralized_formation_controller
```

See [uned_swarm_config/README.md](uned_swarm_config/README.md) for the experience `.yaml` schema and the current set of experiment config files in `resources/`.

### Available launches

| Launch file | Description |
|---|---|
| `generic.launch.py` | Main parametrized launch: `config_file:=<experiment>.yaml` from `uned_swarm_config/resources/` (default `default.yaml`, which does not exist — always pass `config_file`). Starts Webots or Gazebo, one driver per robot, the central node when `Architecture.mode: centralized`, the supervisor and the CPU monitor. |
| `AC10_Sensor_Webots.launch.py` | Old hand-written AC10 experiment (Crazyflie + Khepera IV) in Webots. **Broken**: it loads `AC10_RoboticPark.yaml`/`.wbt`/`.rviz`, which are not in the repo, and the removed `uned_crazyflie_webots` package. |
| `AC10_Sensor_Gazebo.launch.py` | Same AC10 experiment in Gazebo. **Broken** for the same missing `AC10_RoboticPark.yaml`. |

Experiences for `generic.launch.py`:

| `config_file` | Description |
|---|---|
| `MultiRobot10_Webots.yaml` | 4 Crazyflie + 6 Khepera IV, virtual in Webots: distance-based formation (`gradient`) with an origin reference, event-based communication, supervisor `MultiRobot_topics.yaml`. |
| `MultiRobot10_Gazebo.yaml` | Same fleet and formation in Gazebo (`Empty.world`). |
| `RIAI2023_Centralized_Webots.yaml` / `RIAI2023_Centralized.yaml` | 5 Crazyflie + 4 Khepera IV (RIAI 2023), virtual / real: formation computed by the central node `centralized_formation_controller`. |
| `RIAI2023_Distributed_Webots.yaml` / `RIAI2023_Distributed.yaml` | Same fleet, virtual / real: distributed formation, each robot runs its own `gradient` law in its ROS 2 driver. |
| `RIAI2023_Onboard_Webots.yaml` / `RIAI2023_Onboard.yaml` | Same fleet, virtual / real: distributed formation with `task.Onboard: True` (on the real Crazyflie the law runs in the firmware). |

`MultiRobot_topics.yaml` and `RIAI2023_topics.yaml` are supervisor scripts, not experiences. Lab-wide experiences are listed in [`RoboticPark/LAUNCHES.md`](https://github.com/Robotic-Park-Lab/RoboticPark/blob/humble-dev/LAUNCHES.md).

## Authors ✒️
* **[Francisco José Mañas Álvarez](https://github.com/FranciscoJManasAlvarez)** :envelope: fjmanas@dia.uned.es

## Related publications :paperclip:
