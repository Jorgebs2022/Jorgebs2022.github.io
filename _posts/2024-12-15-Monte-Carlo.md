---
layout: post
title: "Monte Carlo"
date: 2024-12-15 10:25:43 +0200
categories: robotica-movil
---

## Introduccion:

En esta práctica, el objetivo fue implementar un sistema de localización basado en el algoritmo de Monte Carlo (MCL). Este método utiliza partículas para representar la posición y orientación del robot en un mapa y permite actualizar estas estimaciones iterativamente basándose en mediciones reales de sensores y el movimiento del robot.

El enfoque principal fue inicializar las partículas de forma controlada, calcular probabilidades basadas en los datos del láser teórico y real, propagar las partículas con ruido en cada iteración, y re-muestrearlas utilizando la ruleta de Monte Carlo.

## Descripcion del algoritmo:

### Inicialización de partículas:

    - Las partículas se generan en torno al punto central del mapa, con ruido controlado en la posición y orientación inicial.
    - Solo se generan partículas en espacios libres del mapa (celdas no ocupadas por obstáculos). Esto asegura que las partículas sean válidas y representen ubicaciones posibles del robot.

### Obtención de datos del láser:

    - Se calculan las distancias del láser en dos formas:
        - Láser real: Datos obtenidos directamente de los sensores del robot.
        - Láser teórico: Distancias simuladas mediante un algoritmo de ray tracing desde cada partícula.
    - Las similitudes entre estos dos conjuntos de datos son la base para calcular la probabilidad de cada partícula.

### Propagación de partículas:

    - En cada iteración, las partículas se mueven simulando el desplazamiento del robot. Este movimiento incluye:
        - Ruido en x,yx,y y orientación para modelar las incertidumbres del movimiento.
    -En este caso implemente la odometría real del robot para mejorar la precisión de la propagación.

### Cálculo de pesos:

    Se comparan las lecturas del láser real y teórico para cada partícula. Las partículas con mediciones más similares al láser real reciben un peso mayor.
    Los pesos se normalizan para que su suma sea igual a 1, creando una distribución de probabilidad.

### Re-muestreo de partículas:

    Se utiliza la ruleta de Monte Carlo para seleccionar partículas en función de sus pesos. Esto significa que partículas con mayor peso tienen más probabilidad de ser seleccionadas, mientras que las de menor peso se eliminan gradualmente.

### Iteración del bucle:

    Estos pasos se repiten continuamente para refinar la estimación de la posición y orientación del robot en el mapa.

## Dificultades

Durante la implementación, surgieron varios desafíos:

### Validación de partículas iniciales

El principal reto al inicializar las partículas fue garantizar que todas cayeran en celdas libres del mapa. Esto requirió convertir coordenadas globales a locales y viceversa, y validar cada partícula para evitar ubicarlas sobre obstáculos.

### Simulación del láser

La implementación del láser teórico mediante ray tracing fue compleja debido a mi inicial falta de entendimiento, afortunadamente a medida que fui avanzando en la practica me fui dando cuenta del verdadero funcionamiento y utilidad del laser teoroco.

### Propagación y ruido

Modelar el ruido del movimiento del robot para que fuera realista pero no excesivo fue crítico para evitar que las partículas se dispersaran demasiado lejos del robot real.

### Re-muestreo por ruleta

La implementación del re-muestreo por ruleta con los pesos normalizados presentó dificultades iniciales, ya que errores menores en la normalización causaban desbalances en la selección de partículas.

## Versión Final

En la versión final, el sistema funcionó correctamente gracias a las siguientes mejoras:

### Inicialización robusta:
    Las partículas se generan en torno al centro del mapa con ruido limitado, asegurando una representación inicial precisa.

### Simulación precisa del láser:
    La comparación entre el láser real y el láser teórico fue optimizada utilizando el cálculo de errores absolutos y funciones probabilísticas gaussianas.

### Ruido ajustado:
    La propagación de partículas incluye ruido gaussiano limitado en las posiciones x,yx,y y la orientación para simular el movimiento del robot.

### Re-muestreo eficiente:
    La ruleta de Monte Carlo ajusta la distribución de partículas en cada iteración, favoreciendo partículas con mayores probabilidades y descartando las menos relevantes.

## Robot Limitaciones

### Consumo computacional:
    La simulación del láser virtual para cada partícula es costosa. Reducir el número de haces (parámetro LASER_NUM_BEAMS) mejora el rendimiento, pero afecta la precisión.

### Ruido excesivo:
    En entornos con muchos obstáculos o ruido de sensores, la estimación puede volverse inestable si las partículas no convergen rápidamente.

## Conclusion:

Esta práctica permitió implementar y comprender el algoritmo de Monte Carlo para la localización de robots. A través de pasos iterativos de propagación, cálculo de pesos y re-muestreo, el sistema es capaz de estimar con precisión la posición del robot en un mapa conocido.

Los aspectos más destacados de la práctica incluyen:

    - La inicialización controlada de partículas en espacios libres.
    - La integración de datos del láser real y teórico.
    - El uso eficiente de la ruleta de Monte Carlo para ajustar las partículas.

A pesar de las limitaciones, el sistema es robusto y funcional, proporcionando una base sólida para futuras mejoras

