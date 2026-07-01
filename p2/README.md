# P2-rescue_people

02/07/26

David Pons Canet


## Objetivo

Desarrollar un algoritmo para un dron de rescate que explore una zona delimitada en el mar, detecte a los supervivientes mediante visión artificial y regrese a la base de forma segura.

## Resumen

Convertir las coordenadas GPS del barco y la zona de emergencia a coordenadas cartesianas UTM para poder usarlas en el sistema de navegación local.

Planificar la ruta de vuelo del dron generando un patrón de barrido que cubra toda el área de exploración definida.

Procesar las imágenes de la cámara ventral para aislar a los supervivientes, filtrando el color del mar y aplicando detección de rostros en múltiples ángulos.

Ejecutar la misión mediante una máquina de estados que controla la aproximación inicial, la exploración por waypoints y el retorno por batería baja.


## Planificación de ruta y Coordenadas

Para establecer los puntos de navegación, primero he convertido las coordenadas originales (grados, minutos y segundos) a formato decimal y posteriormente las he transformado a coordenadas cartesianas utilizando la librería utm. Esto me permite calcular distancias en metros (x, y) respecto a mi zona de inicio.

Sabiendo que el área a explorar es de 1000 m², he calculado la longitud del lado del cuadrado y delimitado los bordes de la zona (x_min, x_max, y_min, y_max). Con estos límites, genero dinámicamente una lista de "waypoints" para realizar un patrón de barrido (zig-zag). El dron recorre la zona de un extremo al otro en el eje X, y avanza en el eje Y utilizando un paso (step) de 2 metros por cada pasada.


## Máquina de estados y Ejecución

Para la navegación y ejecución de la misión he utilizado una máquina de estados con tres modos:

APPROACHING: Una vez el dron despega a 5 metros de altura, se le envía el comando para volar directamente al primer waypoint de nuestra lista. Calculo la distancia euclídea y, cuando el error es menor a 15 cm, considero que ha llegado a la zona y paso al modo de barrido.

SWEEPING: El dron navega secuencialmente por la lista de waypoints calculada mientras el algoritmo de detección de rostros se ejecuta continuamente en las imágenes de la cámara. Al igual que en la aproximación, paso al siguiente punto de la lista cuando la distancia al objetivo es menor de 15 cm.

RETURNING: He implementado un monitoreo simulado de batería. Si el nivel de batería desciende por debajo del 20%, el dron cambia automáticamente a este estado y se dirige a las coordenadas donde realizó el despegue para asegurar su retorno.


## Video

[Ver video](dron_rescue.mp4) 