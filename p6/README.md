# P6-Marker Based Visual Locc

02/07/26

David Pons Canet

## Objetivo

El objetivo de este ejercicio es estimar la posición y la orientación (pose) de un robot en un espacio bidimensional mediante la detección y el análisis de marcadores visuales, concretamente AprilTags.


## Resumen

Obtener las poses de los tags, y construir una matriz de transformacion de tag a world
de cada uno de los tags.
Detectar lo tags, y con solvePNP obtener los vectores de traslacion y rotacion.
Cojer la matriz del tag correspondiente y calcular la poe con los vectores obtenidos
anteriormente.

Si no vemos ningun tag, nos movemos referente al incremento de odom.

## Matrices Transformacion Tags

Para obtener las matrices de cada tag, primero tenemos que crear una matriz 4x4, 
y ponemos la rotacion obtenida en el archivo yaml, y lo multiplicamos por TAG_ROT, 
ya que los ejes de la camara no coinciden con los ejes del robot. de esta forma los 
alineamos. Despues añadimos la traslacion y lo guardamos en diccionario de las 
transformaciones de los tags.

## Prediccion Pose

Para predecir la pose, primero detectamos los tags vistos en cada momento, y con solvePNP nos quedamos con el tag mas cercano.
Una vez lo tenemos, cojemos la matriz de transformacion de los tags correspondiente,
y le aplicamos las transformaciones necesarias con los vectores de rotacion y translacion obtenidos con solvePNP, y calculamos la posicion estimada.

## Video


[▶ Ver vídeo](Screencast from 2026-07-01 18-42-20.mp4)