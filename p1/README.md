# P1-vacuum_cleaner_loc

11/10/25

David Pons Canet


## Objetivo

Desarollar un algoritmo de cobertura BSA que consiga que la aspiradora cubra el espacio libre de una casa.


## Resumen

Registrar el mapa para relacionar las coordenadas del mundo con las coordenadas en pixeles del mapa y con la rejilla de navegación.

Crear la rejilla de navegacion y planificar la ruta del robot aspirador siguiendo el algoritmo de cobertura BSA.

Ejecurar la ruta planificada con un control por posición.


## Rejistro del mapa

Para realizar el registro del mapa, he movido el robot a 12 posiciones distintas por el mundo, y me he guardado el valor de las coordenadas mundo de cada punto, guardandome tambien el aproximadamente las coordenadas pixel que representaria en el mapa.

Una vez he conseguido los 12 pares de coordenadas, he resuelto la matriz de transformacion y optimizado sus resultados con los metodos "least_squares" y "differential_evolution" de la libreria "scipy.optimize". Esto me devuelve los valores de la matriz de transformaxion con la cual podremos hacer la conversion entre tipos de coordenadas.


## Crear rejilla de navegación

Para crear la rejilla de navegación, primero de todo he tenido que erosionar el mapa (erosionar espacio libre, dilatar obstaculos), la mitad del radio del robot, para que al navegar no choque con los bordes o las esquinas.

Una vez erosionado el mapa, asignandole un valor a "CELL_SIZE", que debe ser algo mas pequeño que "ROBOR_SIZE", podemos obtener el numero de filas y columas de la nav_grid y la creamos.

Despues, recorremos todas las celdas, mirando si hay algun pixel negro(obstaculo), dentro de cada celda, de ser asi, marcamos la celda como obstaculo.

He hecho con np.dtype, que cada celda del nav grid, contenga un "struct", en el cual de cada celda podremos saber: fila y columna del pixel del nav_grid que representa el centro de la celda, el estado de la celda (obstaculo, obstaculo virtual, putnos de retorno y critico), y el color del que lo queremos pintar para poder enseñarlo y debugear correctamente.  


## Planificación BSA

Para obtener la rota a seguir, primero he obtenido mi posicion actuial y la he convertido en la celda que le corresponde. A partir de ahi, creo una lista y voy añadiendo las celdas que me va dando el algoritmo de cobertura BSA. 

Una vez alcanzado un punto crítico, calculo por manhattan cual es el punto de retorno mas cercano, y despues hago una busqueda por anchura para obtener la ruta hasta ese punto de retorno, y se la sumo a la ruta que llevabamos calculada hasta ese momento.


## Ejecución ruta planificada

Para poder ejecutar la ruta planificada, podremos hacerlo por posicion, ya que tenemos el robot bien locaclizado. Comparando la posición actual y la posicion deseada, obtendremos el error, con el cual podemos sacar nuestras velocidades linear y angular para cumplir nuestro objetivo y navegar por toda la ruta planificada. 

He considerado que si estoy mas cerca de 10 cm de la celda objetivo, he llegado a esa celda y paso a la siguiente de la lista.

Para el ajuste de "v" y "w", lo he probado con muchos valores distintos kp, kd y ki de un pid, pero la forma en la que me parecia que lo hacia mejor, era sin que el pid haga nada (kp=1, kd=0, ki=0). Aun que de esta forma quiza va un poco lento, es la manera en la que he conseguido que el robot vaya mas recto por el centro de las celdillas, sin hacer nada de zig-zag.
