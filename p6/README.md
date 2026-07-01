# P6-Marker Based Visual Locc

02/07/26

David Pons Canet

## Objetivo

El objetivo de este ejercicio es estimar la posición y la orientación (pose) de un robot en un espacio bidimensional mediante la detección y el análisis de tags visuales, concretamente AprilTags.


## Resumen

Cargar y procesar las posiciones absolutas de los marcadores desde un archivo de configuración para generar las matrices de transformación espacial.

Detectar los AprilTags en el flujo de video y resolver la perspectiva de la cámara respecto al marcador utilizando OpenCV (solvePnP) para obtener la pose relativa.

Transformar estas coordenadas relativas al sistema de coordenadas global del mundo, calculando con precisión las coordenadas cartesianas (X, Y) y la orientación (Yaw).

Integrar la odometría del propio robot como sistema de respaldo para mantener la estimación de la posición cuando no hay tags a la vista.

Ejecutar un algoritmo de navegación puramente reactivo (Bump & Go) apoyado en los datos del sensor láser para mantener al robot en movimiento continuo..


## Lectura del mapa y Transformaciones

Al inicio del programa, se extrae la información espacial de los tags desde el archivo de configuración apriltags_poses.yaml. Conociendo la posición (X, Y, Z) y la orientación (Yaw) de cada tag, construyo una matriz de transformación homogénea de 4x4 para cada una.

Estas matrices (WORLD_TAG_TRANSFORMS) incluyen los ajustes de rotación necesarios para alinear los ejes del tag con los del mundo, permitiendo una conversión directa entre el marco de referencia de cualquier AprilTag y las coordenadas globales del mapa.


## Localización Visual con solvePnP

Para la estimación de la pose, el flujo de trabajo es el siguiente:

Detección: Utilizo la librería pyapriltags sobre la imagen en escala de grises para identificar las esquinas en 2D de las balizas visibles (familia tag36h11).

Estimación relativa: Si hay detecciones, se procesan usando la función cv2.solvePnP en modo SOLVEPNP_IPPE_SQUARE. Introduciendo las esquinas 2D, las coordenadas 3D físicas de la marca, y los parámetros intrínsecos de la cámara, obtenemos los vectores de rotación (rvec) y traslación (tvec). Para minimizar errores, si hay múltiples balizas, el algoritmo selecciona siempre la más cercana a la cámara.

Conversión Global: Se invierten las matrices resultantes para calcular la posición de la cámara relativa a la baliza. Finalmente, se multiplica este resultado por la matriz global de transformación de dicha etiqueta (calculada en el primer paso) para obtener la posición rob_x y rob_y del robot en el mundo real. El ángulo yaw se extrae proyectando el eje Z de la cámara sobre el plano global.


## Fusión con Odometría

Dado que la cámara del robot tiene un campo de visión limitado y no siempre tendrá un AprilTag enfrente, he implementado un sistema de continuidad apoyado en la odometría.

El sistema guarda la posición del robot (last_odom) en cada iteración. Si en un ciclo concreto la cámara no detecta ninguna baliza visual, se calcula el diferencial de movimiento (dx, dy, dyaw) proporcionado por la odometría pura del robot. Estos incrementos se suman a la última estimación visual válida (last_pose), permitiendo que el robot mantenga la noción de dónde está basándose en su propio desplazamiento relativo hasta que vuelva a ver otra etiqueta.


## Video


[Ver video](autoloc_vision.mp4) 