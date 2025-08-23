# Jackal Simulation Workspace

Este proyecto contiene los archivos necesarios para simular el robot Jackal en ROS 2 y Gazebo.

## Requisitos mínimos

- **Sistema operativo:** Ubuntu 20.04 o superior
- **ROS 2:** Humble
- **Gazebo:** Instalado y configurado para ROS 2
- **Python:** 3.8 o superior
- **Dependencias ROS 2:**  
  - `robot_state_publisher`
  - `twist_mux`
  - `gazebo_ros`
  - `rviz2`
  - `xacro`
  - `ament_index_python`
  - `slam_toolbox`
  - `nav2_bringup` (para navegación)

> **Nota:** Asegúrate de tener todos los paquetes y dependencias instalados antes de ejecutar la simulación.

## Estructura relevante del proyecto

- `src/jackal/launch/launch_sim.launch.py`: Archivo principal de lanzamiento para la simulación.
- `src/jackal/launch/rsp.launch.py`: Publica el estado del robot usando el modelo URDF.
- `src/jackal/config/view_bot_rviz.rviz`: Configuración de RViz para visualizar el robot.
- `src/jackal/config/twist_mux.yaml`: Configuración para el nodo `twist_mux`.
- `src/jackal/worlds/warehouse2.world`: Mundo personalizado para Gazebo.
- `src/jackal/config/mapper_params_online_async.yaml`: Parámetros para SLAM Toolbox.

## Cómo ejecutar la simulación

### 1. Construye el workspace

```sh
colcon build
source install/setup.bash
```

### 2. Lanza la simulación con un mundo personalizado

```sh
ros2 launch jackal launch_sim.launch.py world:=$(pwd)/src/jackal/worlds/warehouse2.world
```

- Esto lanzará Gazebo con el mundo `warehouse2.world` y spawneará el robot Jackal en la posición inicial.
- También abrirá RViz2 con la configuración predeterminada.

### 3. Ejecuta SLAM Toolbox para mapear el entorno

En una nueva terminal (no olvides `source install/setup.bash`):

```sh
ros2 launch slam_toolbox online_async_launch.py slam_params_file:=./src/jackal/config/mapper_params_online_async.yaml use_sim_time:=true
```

- Esto iniciará SLAM Toolbox en modo asíncrono usando los parámetros definidos.
- En RViz, agrega una visualización de tipo **Map** y selecciona el topic `/map` para ver el mapa generado en tiempo real.

### 4. Ejecuta la navegación con Nav2

En otra terminal:

```sh
ros2 launch jackal navigation_launch.py use_sim_time:=true
```

- Esto lanzará el stack de navegación (Nav2) para el robot.
- En RViz, agrega una visualización de tipo **Map** y selecciona el topic `/global_costmap` para ver el costmap global.
- Puedes ocultar otros elementos y dejar visible solo el costmap si lo deseas.

### 5. Prueba la navegación

- Usa las herramientas de Nav2 en RViz para definir la posición inicial del robot (**2D Pose Estimate**) y los puntos de destino (**Nav2 Goal**).
- El robot planificará y navegará automáticamente hacia los puntos definidos, utilizando el mapa generado por SLAM.
