## Práctica evaluable 1: moverse evitando obstáculos

## Objetivo 

**Desarrollad un programa en ROS que haga que el robot se mueva por el entorno evitando los obstáculos**.  No tiene por qué tener un destino determinado, simplemente "vagabundear" por el entorno. Aunque en la asignatura veremos algoritmos de evitación de obstáculos bastante sofisticados, no se pide nada de eso ya que el objetivo es simplemente que os familiaricéis con la programación en ROS.

Como idea sencilla podéis mirar las lecturas que apuntan más o menos a la derecha y las que apuntan más o menos a la izquierda (en lugar de tomar una sola podéis coger la media de varias que estén en un ángulo similar). Si las lecturas de la izquierda son menores que las de la derecha hay que girar hacia la derecha, y viceversa. Podéis añadir mejoras, como por ejemplo:

 - Que la velocidad de giro sea mayor si la diferencia entre izquierda y derecha es mayor
 - Que la velocidad lineal sea proporcional a la distancia al obstáculo más cercano
 - ...cualquier otra idea que se os ocurra

**IMPORTANTE**: el código debería funcionar para cualquier sensor de rango 2D independientemente del número de lecturas y del ángulo de visión del sensor. Únicamente debéis suponer que el ángulo de visión tiene su punto medio en el frente (con lo que la lectura del medio del array será lo que tiene el robot justo enfrente). **Haced el código lo bastante  genérico para que aunque cambie el número de lecturas y el ángulo de visión del sensor, el programa no dé error**, aunque es posible que si el sensor cambia demasiado tengáis que reajustar parámetros en el algoritmo.

## Normas de entrega

Esta práctica se debe desarrollar **individualmente**. 

> Podéis compartir con vuestros compañeros las ideas de cómo funciona el algoritmo (y de hecho si lo discutís y os dais ideas entre vosotros será más enriquecedor) pero el código Python o C++ debería ser exclusivamente vuestro. Sería recomendable que si habéis colaborado en el desarrollo del algoritmo os referenciéis unos a otros en la documentación entregada ("la idea del algoritmo ha surgido en colaboración con XXX e YYY...")

La práctica se entregará a través de moodle. El plazo de entrega concluye el **martes 13 de octubre a las 23:59**

Tenéis que entregar:

- **Código fuente** del programa con comentarios de cómo funciona
- **Documentación de las pruebas realizadas**: en qué circunstancias funciona el algoritmo, en cuáles ha fallado, por qué creéis que lo ha hecho y cómo creéis que se podría solucionar. Podéis incluir capturas de pantalla, adjuntar videos, los ficheros de los mundos...
    + Como mínimo probad un mundo en el simulador stage (usando el `ejemplo.world` que vimos en la introducción a ROS), y otro en el simulador gazebo. Ten en cuenta que el sensor simulado en stage (un láser) tiene mucho más campo de visión que el de Gazebo (una cámara 3D): 270 grados para el láser vs. 60 para la cámara, y eso influirá en el comportamiento del algoritmo, más que el que sea un entorno 2D o 3D.
  
  > Por las restricciones de presencialidad de este año, en la nota de esta práctica no se evaluará el algoritmo probándolo con el robot real, pero sí que lo podréis probar en una sesión aparte (el 14 o el 21 de octubre)

**IMPORTANTE**: en evitación de obstáculos (como en todo lo demás) **no hay algoritmos perfectos  ni que funcionen siempre** igual de bien en todos los casos. No debéis intentar "esconder" los casos en los que vuestro código no funciona, sino documentarlos, intentar encontrarle una explicación y (idealmente) proponer mejoras que lo solucionarían. Para poder aplicar un algoritmo es importante conocer sus limitaciones.

## Baremo de evaluación

- **Hasta un 6**: código entregado y documentado (con comentarios al fuente) y una explicación breve (de 1/2 a 1 página) de la idea básica de vuestro algoritmo
- **Hasta un 7**: todo lo anterior más 2 pruebas documentadas: una en stage y otra en gazebo 
- **Hasta un 8**: todo lo anterior más pruebas documentadas en al menos otro entorno de stage y otro de gazebo, que sean diferentes a los anteriores (p.ej. pocos obstáculos vs muchos). Podéis buscar entornos por internet o crearlos vosotros.
- **Hasta un 10**: todo lo anterior más buscar otro algoritmo de evitación de obstáculos ya implementado en ROS (o en otra plataforma), explicar a grandes rasgos la teoría de cómo funciona (hasta 1 página), probarlo y compararlo con el vuestro (1 página más mínimo). Si no encontráis ninguno que consigáis hacer funcionar en ROS, probadlo en un entorno lo más similar posible a los que uséis en stage o gazebo.