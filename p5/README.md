# 🗺️ Robot Autonomous Mapping & Obstacle Avoidance

Este proyecto implementa un sistema de **mapeo probabilístico**,
**detección de obstáculos** y **navegación reactiva** para un robot
móvil equipado con un sensor LIDAR de 360°. El robot explora el entorno
actualizando un mapa de ocupación en tiempo real y evitando colisiones
mediante un comportamiento reactivo simple basado en avance y giro.

## 📌 Características principales

-   Ray tracing para actualizar celdas libres y ocupadas.\
-   Uso completo del LIDAR (360 lecturas por ciclo).\
-   Mapa probabilístico usando odds Bayesianos.\
-   Navegación reactiva: el robot avanza y gira según el espacio
    disponible.\
-   Visualización del mapa en WebGUI.\
-   Bucle principal continuo con actualización y movimiento.

## 🧠 Funcionamiento general

El robot procesa cada ciclo de la siguiente forma:

1.  Lee los datos del LIDAR.\
2.  Traza los rayos y actualiza probabilidades del mapa
    (libre/ocupado).\
3.  Comprueba si hay un obstáculo frontal a menos de `SAFE_DIST`.\
4.  Toma una decisión:
    -   Si no hay obstáculo → avanza recto.\
    -   Si hay obstáculo → gira hacia el lado con mayor espacio libre.\
5.  Envía el mapa procesado a WebGUI para visualización.

## 📁 Estructura del mapa

El sistema usa dos matrices:

-   `probabilities_map`: valores entre 0 y 1 representando probabilidad
    de ocupación.\
-   `occupancy_map`: mapa visual donde:
    -   255 = libre\
    -   128 = desconocido\
    -   0 = ocupado

Dimensiones del mapa:

-   Ancho: 1500\
-   Alto: 970

## 🔧 Principales funciones

### update_map()

Toma los datos del LIDAR y actualiza el mapa probabilístico llamando a
`ray_trace()` para cada rayo.

### ray_trace(x0, y0, x1, y1, obs)

Simula el recorrido del rayo entre el robot y la posición detectada.\
Actualiza cada celda como: - Libre (si el rayo pasa por ella).\
- Ocupada (si es el punto final y se detecta obstáculo).

### is_obstacle()

Comprueba si existe un obstáculo frente al robot midiendo las distancias
de los rayos frontales.\
Devuelve también hacia qué lado hay más espacio para girar.

### show_map()

Convierte el mapa probabilístico en un mapa visual (blanco/negro/gris) y
lo envía a WebGUI.

## 🚀 Modos de navegación

### FORWARD

El robot avanza recto con: - Velocidad lineal: `HAL.setV(1)`\
- Velocidad angular: `HAL.setW(0)`

Cambia a modo TURN si detecta un obstáculo frontal.

### TURN

El robot gira sobre sí mismo hacia: - Izquierda → `HAL.setW(0.2)`\
- Derecha → `HAL.setW(-0.2)`

Cuando el frente está libre, vuelve a FORWARD.
