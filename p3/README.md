# P3-Autoparking

07/11/25

David Pons Canet

## Objetivo

El objetivo de este ejercicio es implementar la lógica de un algoritmo de navegación para un vehículo autónomo. El vehículo debe encontrar una plaza de aparcamiento y aparcar correctamente.

## Resumen

Alineación inicial del vehículo mediante la comparación de las distancias devueltas por los extremos del láser lateral.

Uso de un controlador PID para mantener una separación lateral constante y segura respecto a los vehículos ya aparcados durante la fase de exploración.

Detección de plazas libres calculando la distancia recorrida (odometría) mientras el láser derecho no detecta obstáculos cercanos.

Ejecución de la maniobra de aparcamiento en línea mediante una submáquina de estados que intercala giros, marcha atrás y marcha adelante, apoyándose en la lectura de los láseres frontal y trasero para evitar colisiones y centrar el vehículo.

## Búsqueda de hueco y Posicionamiento

El comportamiento del coche se divide inicialmente en tres grandes estados o modos de operación:

### SEARCH: 
El vehículo avanza leyendo continuamente el láser lateral derecho. Si detecta un coche a su derecha, utiliza el PIDController para ajustar su dirección (Yaw) y mantenerse a una distancia lateral paralela de exactamente 1.25 metros (LATERAL_PARK_DIST). Si el láser derecho deja de detectar un obstáculo (lectura mayor a 5 metros), el sistema guarda la coordenada inicial y comienza a calcular el espacio vacío recorrido (empty_space).

### GET_IN_POSE:
Si el espacio vacío medido supera los 8 metros (SPACE_TO_PARK), el coche considera que la plaza es válida. Entra en este modo para avanzar unos 3.5 metros adicionales en línea recta (usando el PID para no desviarse de su target_yaw), colocándose así en la posición de partida óptima por delante del hueco para iniciar la maniobra de retroceso.

## Máquina de estados de Aparcamiento (PARK)

Una vez colocado en la posición de inicio, el algoritmo cambia al modo PARK. Este ejecuta la maniobra en línea dividida en 6 sub-estados secuenciales, basando sus transiciones en la variación del ángulo (yaw), la distancia recorrida y las lecturas de los láseres frontal y trasero:

### Fases 1 y 2:
El vehículo inicia dando marcha atrás y girando fuertemente a la derecha hasta rotar 30 grados. A continuación, realiza un pequeño ajuste hacia adelante de 15 grados para suavizar y corregir el ángulo de inserción.

### Fase 3:
El coche retrocede en línea recta y en diagonal durante exactamente 1.3 metros para introducirse de lleno en la plaza.

### Fase 4:
Continúa la marcha atrás, pero ahora girando el volante fuertemente a la izquierda para meter el morro en la plaza. Este estado incluye un límite de seguridad vital: el vehículo se detiene y cambia de estado en el momento en el que el láser trasero detecta un obstáculo a menos de 0.75 metros.

### Fases 5 y 6:
Para enderezar completamente el coche, avanza girando a la izquierda hasta que el láser frontal indica que está a 1 metro del vehículo delantero. Como paso final, da marcha atrás ajustando levemente el giro hasta que la diferencia entre el espacio libre delantero y el trasero es menor o igual a 25 centímetros. En ese momento, apaga los motores y da el aparcamiento por finalizado.


## Video

[ver video](autoparking.mp4)