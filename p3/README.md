# P3-Autoparking

07/11/25

David Pons Canet

## Objetivo

El objetivo de este ejercicio es implementar la lógica de un algoritmo de navegación para un vehículo autónomo. El vehículo debe encontrar una plaza de aparcamiento y aparcar correctamente.

## Resumen

Lo he implementado con una maquinda de estados, con los estados "SEARCH"(buscar hueco para poder aparcar), "GET_IN_POSE"(colocarse para aparcar) y "PARK", que tiene otra maquina de estados con los tres modos de aparcamiento (entrar, enderezar, y avanzar un poco).

### SEARCH

Si al empezar ya detecta hueco, avanza hasta que detecta espacio suficiente para aparcar. Una vez detecta que tiene espacio suficiente, pasa a "GET_IN_POSE".

Si al empezar detecta coches, avanza pero buscando la distancia "LATERAL_PARK_DIST" con el coche detectado. Cuando ya esta a la distancia deseada, continuo recto buscando el hueco para aparcar.

### GET_IN_POSE

En este estado, avanzamos un poco para estar en la posicion correcta para poder aparcar el coche sin problemas

### PARK

Este estado es para hacer las maniobras necesarias para aparcar el coche correctamente. Los tres "park_mode", son las tres maniobras necesarias.
