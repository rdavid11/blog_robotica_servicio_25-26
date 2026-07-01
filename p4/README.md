# P4-amazon_warehouse

02/07/26

David Pons Canet


## Objetivo

Desarrollar un sistema de navegación y planificación de trayectorias para un robot logístico, permitiéndole transportar estanterías de forma autónoma desde un punto de recogida hasta una zona de entrega, respetando la cinemática del vehículo y evitando colisiones.


## Resumen

Calcular la transformación afín para relacionar las coordenadas cartesianas del mundo con los píxeles del mapa bidimensional.

Planificar rutas óptimas y libres de obstáculos utilizando la librería OMPL (Open Motion Planning Library) y el algoritmo RRT*.

Controlar la navegación del robot mediante un algoritmo basado en seguimiento de puntos (Lookahead) y controladores PID para ajustar las velocidades.

Manipular estanterías y actualizar el mapa de ocupación en tiempo real dependiendo de si el robot está cargado o descargado. 


## Transformación de Coordenadas

Para poder navegar basándome en el mapa proporcionado, primero resuelvo la relación entre las coordenadas del mundo (WORLD_COORDS) y las del mapa en píxeles (MAP_COORDS). Para ello, optimizo los parámetros de una transformación afín utilizando los métodos differential_evolution y least_squares de la librería scipy.optimize. Esto me permite usar las funciones auxiliares world_to_map y map_to_world de manera precisa para traducir las ubicaciones durante la planificación y la ejecución.


## Planificación de Trayectorias (OMPL)

Durante el estado APPROACHING, el robot persigue y ejecuta la ruta calculada. Para la cinemática de tipo Ackerman, el algoritmo escanea la trayectoria y busca un punto objetivo (lookahead_pt) a una distancia de visión determinada hacia adelante. Analizando la dirección del segmento de la ruta, el sistema decide automáticamente la marcha y si el robot debe avanzar o ir hacia atrás (is_reverse).

El error de orientación hacia ese punto objetivo se introduce en un controlador PID, que devuelve la velocidad angular necesaria para maniobrar el vehículo. Además, la velocidad lineal se adapta de forma dinámica en función del error angular y la distancia a la meta, y los volantazos se amortiguan gradualmente conforme el robot se acerca al final de su ruta para garantizar que entre recto.


## Navegación y Control

Durante el estado APPROACHING, el robot persigue y ejecuta la ruta calculada. Para la cinemática de tipo Ackerman, el algoritmo escanea la trayectoria y busca un punto objetivo (lookahead_pt) a una distancia de visión determinada hacia adelante. Analizando la dirección del segmento de la ruta, el sistema decide automáticamente la marcha y si el robot debe avanzar o ir hacia atrás (is_reverse).

El error de orientación hacia ese punto objetivo se introduce en un controlador PIDController (angularPID), que devuelve la velocidad angular necesaria para maniobrar el vehículo. Además, la velocidad lineal se adapta de forma dinámica en función del error angular y la distancia a la meta, y los volantazos se amortiguan gradualmente conforme el robot se acerca al final de su ruta para garantizar que entre recto.


## Videos

[Ver video](holonimic.mp4) 
[Ver video](ackerman.mp4) 