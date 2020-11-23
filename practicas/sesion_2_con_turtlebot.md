# Robots Móviles 2020/21. 
# Sesión 2 con los Turtlebot: Mapeado y localización

En esta sesión de trabajo presencial con los Turtlebots vamos a probar los algoritmos de mapeado y localización. Como sabéis solo hay 5 turtlebots por lo que la práctica la tendréis que hacer en grupos.

Los objetivos son:

- Como hemos dicho, **probar el mapeado y localización** con el laser y, si es posible, con la cámara RGBD del robot
- Aprender otra forma de conectarse con el robot, el **modo cliente/servidor**, donde parte del código corre en el robot y otra parte en vuestro ordenador. 

## Conexión con el robot

> IMPORTANTE: en este modo de conexión que usaremos hoy necesitáis ROS Kinetic en vuestro PC. En el apéndice se describe cómo usar el modo VNC como alternativa, que no necesita ROS en el PC, aunque no podréis ver cómo se va construyendo el mapa sobre la marcha. 

## Mapeado

En el turtlebot siemp


## Localización


## Entrega

La práctica se realiza y por tanto se **entrega por grupos**. La memoria la entregará un único miembro del grupo en moodle. En la memoria es donde figurarán los nombres de todos los componentes.

La **memoria** debe incluir:

    + 1-2 páginas describiendo los resultados: si son buenos o malos en general, qué diferencias habéis notado con la simulación (si las hay), 
        + mapeado: poner los mapas, qué partes del mapa han salido mejor/peor, qué cosas ha detectado mejor/peor el sensor... (y por qué puede ser, si se os ocurre), 
        +  localización: qué tal ha funcionado, si se pierde el robot en algún momento....
    + Los archivos con los mapas generados durante la sesión (los .pgm y los .yaml)
    + Para la localización, un video de rviz que muestre al robot navegando por el entorno y localizándose (podéis filmarlo con una cámara para que se vea rviz y también dónde está el robot en el mundo real). Al ser el límite de la entrega de 20Mb tendréis que guardar el video en Google Drive, o subirlo a Youtube o similar y entregar el enlace.


## Apéndice: uso de Rosbag y VNC para generar el mapa *offline*

    