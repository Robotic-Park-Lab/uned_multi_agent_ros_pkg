# uned_multi_agent_ros_pkg

> 📖 Para entender las ramas de este repo y la guía de contribución, consulta la rama [`doc`](https://github.com/Robotic-Park-Lab/uned_multi_agent_ros_pkg/tree/doc).

Repositorio para el desarrollo de funcionalidades multi-agente en Robotic Park Lab: ficheros de configuración y lanzamiento que combinan robots de distintos repositorios (Crazyflie 2.1, Khepera IV, LIMO, DJI Tello…), y los nodos de tarea/control que coordinan varios agentes a la vez. Se llamó `uned_swarm_ros_pkg` hasta 2026-08; se renombró porque el enfoque no es exclusivo de enjambres de UAVs.

#### Estructura
- **scripts**. Ficheros auxiliares que no forman parte de ningún paquete ROS.
- **[uned_swarm_config](uned_swarm_config/README_es.md)**. Paquete de ROS 2 (`ament_cmake`). Lanzamiento y configuración del entorno para experimentos multi-agente: `launch/generic.launch.py`, `launch/AC10_Sensor_Gazebo.launch.py`, `launch/AC10_Sensor_Webots.launch.py`.
- **[uned_swarm_driver](uned_swarm_driver/README_es.md)**. Paquete de ROS 2 (`ament_python`). Nodos driver del lado robot usados en experimentos multi-agente: `swarm_driver` (estimación del centroide sobre un conjunto de robots ya conducidos por otro driver), `tello_gazebo_driver` (control de formación de DJI Tello en Gazebo, vía el servicio `tello_msgs/TelloAction`), `turtlebot_driver` (control de posición de TurtleBot3).
- **[uned_swarm_task](uned_swarm_task/README_es.md)**. Paquete de ROS 2 (`ament_python`). Nodos de coordinación: `centralized_formation_controller`, `system_identification`, `open_loop_signal`, `swarm_reconfiguration`.

> Los paquetes internos siguen llamándose `uned_swarm_*` — el renombrado del repo no arrastró (todavía) el de los paquetes ROS por dentro, para no romper referencias existentes sin avisar.

## Instalación :book:
```
mkdir -p ~/multi_agent_ws/src
cd ~/multi_agent_ws/src
git clone -b humble-dev https://github.com/Robotic-Park-Lab/uned_multi_agent_ros_pkg.git
cd ..
colcon build --symlink-install --packages-select uned_swarm_config uned_swarm_driver uned_swarm_task
source install/setup.bash
```

## Uso 🔧
```
ros2 launch uned_swarm_config generic.launch.py config_file:=<experimento>.yaml
```
```
ros2 run uned_swarm_task centralized_formation_controller
```

Ver [uned_swarm_config/README_es.md](uned_swarm_config/README_es.md) para el esquema de `.yaml` de experiencia y el conjunto actual de ficheros de configuración de experimentos en `resources/`.

### Lanzamientos disponibles

| Fichero de lanzamiento | Descripción |
|---|---|
| `generic.launch.py` | Lanzamiento principal parametrizado: `config_file:=<experimento>.yaml` de `uned_swarm_config/resources/` (por defecto `default.yaml`, que no existe: pasa siempre `config_file`). Arranca Webots o Gazebo, un driver por robot, el nodo central si `Architecture.mode: centralized`, el supervisor y el monitor de CPU. |
| `AC10_Sensor_Webots.launch.py` | Antiguo experimento AC10 (Crazyflie + Khepera IV) escrito a mano, en Webots. **Roto**: carga `AC10_RoboticPark.yaml`/`.wbt`/`.rviz`, que no están en el repositorio, y el paquete eliminado `uned_crazyflie_webots`. |
| `AC10_Sensor_Gazebo.launch.py` | El mismo experimento AC10 en Gazebo. **Roto** por el mismo `AC10_RoboticPark.yaml` que falta. |

Experiencias para `generic.launch.py`:

| `config_file` | Descripción |
|---|---|
| `MultiRobot10_Webots.yaml` | 4 Crazyflie + 6 Khepera IV, virtuales en Webots: formación por distancias (`gradient`) con referencia al origen, comunicación por eventos, supervisor `MultiRobot_topics.yaml`. |
| `MultiRobot10_Gazebo.yaml` | La misma flota y formación en Gazebo (`Empty.world`). |
| `RIAI2023_Centralized_Webots.yaml` / `RIAI2023_Centralized.yaml` | 5 Crazyflie + 4 Khepera IV (RIAI 2023), virtual / real: formación calculada por el nodo central `centralized_formation_controller`. |
| `RIAI2023_Distributed_Webots.yaml` / `RIAI2023_Distributed.yaml` | La misma flota, virtual / real: formación distribuida, cada robot ejecuta su ley `gradient` en su driver de ROS 2. |
| `RIAI2023_Onboard_Webots.yaml` / `RIAI2023_Onboard.yaml` | La misma flota, virtual / real: formación distribuida con `task.Onboard: True` (en el Crazyflie real la ley se ejecuta en el firmware). |

`MultiRobot_topics.yaml` y `RIAI2023_topics.yaml` son guiones del supervisor, no experiencias. Las experiencias de todo el laboratorio están en [`RoboticPark/LAUNCHES_es.md`](https://github.com/Robotic-Park-Lab/RoboticPark/blob/humble-dev/LAUNCHES_es.md).

## Autores ✒️
* **[Francisco José Mañas Álvarez](https://github.com/FranciscoJManasAlvarez)** :envelope: fjmanas@dia.uned.es

## Publicaciones asociadas :paperclip:
