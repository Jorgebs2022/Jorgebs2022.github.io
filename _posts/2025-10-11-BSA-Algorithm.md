---
layout: post
title: "BSA Algorithm"
date: 2025-10-11 14:50:00 +0200
categories: robotica-movil
---

## Introduction

En esta practica el objetivo es conseguir que el robot trace un plan de movimiento por toda la casa y limpie lo maximo posible. Para ello es esencial trazar un plan, obtener los puntos criticos y puntos de retorno por los que el robot tiene que volver para seguir la planificacion.

En esta practica tenemos un mapa PNG de la casa el cual tenemos que transformar en un mapa de rejilla ya que el robot se debe mover por esas celdillas

## Plan Algorithm Overview

The navigation and planning system relied on several key components:

1. **transformation matrix**:
    - En el mapa trabajamos con píxeles (imagen 2D), pero el robot se mueve en coordenadas de mundo (metros). Para poder pasar de un sistema al otro, necesitamos un “puente”. Ese puente lo construimos a partir de puntos de calibración(pares de puntos conocidos tanto en mundo como en la imagen).

    - Con 3 puntos no colineales ya podriamos ajustar una transformación afín exacta, pero al usar más puntos haces un ajuste por mínimos cuadrados que promedia el error, por ello he optado por optener 10 coordenadas con sus respectivos pixeles para calibrar la relación entre el plano del mapa (píxeles) y el plano del mundo (metros)

    - La transformación que usas es afín 2D. Modela traslación, rotación, escala y cizalla entre mundo y píxeles:
            u​=ax+by+tx
            v​=cx+dy+ty​

    donde (x,y) están en mundo (m) y (u,v) en píxeles. Los 6 parámetros  (a,b,c,d,tx,ty) son los que ajustas con los puntos de calibración.

    - Para cada punto añado dos ecuaciones (una para u y otra para v). Con N puntos tengo 2N ecuaciones y 6 incógnitas. Resuelvo por mínimos cuadrados (lstsq), que encuentra el valor que minimiza el error global entre los u,v predichos y los observados.

    - Esta matriz es de suma importancia para para la creacion de las funciones world_to_pixel(x,y,T) y pixel_to_world(u,v,T)

2. **Obstacle inflation**:
    - Con el objetivo de que el robot no se choque contra los obstaculos a la hora de navegar es necesario que se realice una inflacion de los obstaculos con la finalidad de asegurar que el robor trace la ruta sin colisiones.

3. **Creating the Grid Map**:
    - El mapa de rejilla es uno de los elementos mas imprtantes de la practica y lo he hecho de la siguiente manera, Creo una matriz numpy que solo va a contener en cada coordenada los valores 0(Negro) o 127(Blnanco), las celdillas tienen un tamaño de 28x28, Al principio opte por hacer un contador de pixeles blancos y pixdeles negros, recorriendo cada pixel de la celdilla, tras recorrerla si habia mas pixeles negros que blancos la celdilla seria negra y biceversa. Finalmente para aumentar la seguridad del robot y evitar las colisiones opte por simplemente si el en los pixeles de la celdilla que queria rellenar se encontraba por lo menos un pixel negro la celdilla entera seria negra

4. **Movement Priority**:
   - A la hora de hacer la planificacion el robot debe llevar una prioridad de movimientos para que este sea lo mas eficiente posible, en este caso he optado por realizar los movimientis derecha, arriba, izquierda, abajo en este orden.

5. **Critical Point and Return Point**:
   - Al trazar el plan llega un momento donde el robot no puede girar en ninguna direccion ya que o se encuentra rodeado de paredes o se encuentra rodeado de zonas que ya ha visitado(el plan no puede repetirse por lo que nos guardamos las coordenadas por donde pasa la planificacion paraque no pase dos veces por el mismo sitio), al llegar a este punto critico el robot se queda sin opciones de movimiento, pero para solucionar este problema se encuentra el punto de retorno. En mi caso elpunto de retorno lo he obtenido de la siguiente manera. Cada vez que el plan avanza ua rejilla me guardo sus vecinos en un stack, al llegar a un punto critic recorro la lista de vecinos hasta encontrar el ultimo vecino guardado que se encuentre en una celdilla no visitada; en este momento lanzo una busueda por anchura para encontrar la ruta mas corta que existe entre el punto critico y el punto de retorno. Esta es la unica excepcion que hace posible que el plan pueda volver a pasar por celdillas ya visiyadas; Una vez ha llegado al punto de retorno el plan continua co normalidad con la prioridad de movimientos hasta llegar de nuevo a un punto critico.



6. **End Planning**:
   - Ademas de el mapa de rejilla que se ha creado para que la planificacion se mueva por celdillas tambie he creado una matriz numpy de booleanos que indican si la celdilla ha sido visitada o no, al principio la matriz entera esta a False y a medida que la planificacion pasa por una de las celdillas del mapa de rejilla esta pone a True la posicion de la matriz de visitados que corresponde con la posicion de la celdilla. Cuando toda la matriz esta en True la planificacion termina.

7. **Robot movement**
    - Para el mpvimiento del robot primero obtenemos la orientacion del punto al que queremos llegar con respecto a robot y calculamos el error en un rango de -pi a pi, para el control es simple, se basa en avanzar y girar, cuando el error del angulo calculado supera un humbral el robot se detiene (unicamente se detiene la velocidad lineal) y gira hasta que se reduce el error, entontes procede a avanzar hacia el objetivo. El plan devuelve una lista de coordenadas que es la ruta que tiene que seguir el robot para completar toda la casa.



## Difficulties

Several challenges arose during the development and implementation of this practice:

### Creating the transformation matrix
Devido a los conceptos teoricos requeridos para hacer la matriz de transormacion tanto en el hambito de las matematicas como en el de la libreria de numpy se me ha hecho muy dificil sacar correctamente la matriz de transformacion
### Reaching the return point
Se me complico mucho hacer la vuelta dle plan al punto de retorno seleccionado ya que inicialmente en mi plan tras llegar al punto critico se teletransportaba al punto de retorno y posteriormente seguia con l plan, pero esre metodo era ineficiente por lo que tuve que crear la funcion de busqueda por anchura para trazar un camino de vuelta del punto critico al punto de retorno.

### Robot mocement algorithm
Inicialmente tuve la intencion de hacer el movimiento del robot meiante un VFF, tras darme cuenta de que la practica no incluia obstaculos dinamicos me di cuenta de que lo ms optimo era hacer que simplemente el robot se dirijiese a la coordenada indicada ya que si el grid map, la inflacion de los obstaculos estaban correctamente definidos entonces el robot segiria su ruta sin chocarse 

## Robot limitations
Una de las limitaciones mas grandes del robot es su velocidad, esto es debido a que si se aumenta considerablemente su velocidad esta afecta al movimiento dle robot
## Conclusion

This practice provided valuable experience in implementing a navigation system based on planning. Understanding BFS ,grid matrix, onstacle inflation and planning , managing coordinate systems, and balancing navigation performance were key lessons learned. While there are areas for improvement, the vehicle successfully navigates to its target creaning almos all the house.

## Full video one trip

[Ver el video en YouTube](https://youtu.be/ZvjFWqf-dd8)

## Full video two trips

[Ver el video en YouTube](https://youtu.be/APqx-dhWYkQ)
