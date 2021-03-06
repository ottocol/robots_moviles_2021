

# Práctica 3. Programación de tareas en robots móviles
**Robots Móviles. Universidad de Alicante. Noviembre 2020**


> Esta práctica se realizará por grupos de 2-3 personas, aunque también podéis hacerla de modo individual si lo preferís

En esta práctica vamos a programar un robot móvil para que realice una tarea "compuesta" que implique navegar por el entorno realizando una serie de subtareas. La tarea puede ser la que queráis, por ejemplo:

- Patrullar por un edificio pasando por diversos puntos y detectando posibles "intrusos"
- Navegar por una habitación detectando objetos tirados por el suelo y recogiéndolos si el robot tiene un brazo capaz de ello, o simplemente guardándose las coordenadas donde están
- Navegar por un entorno en que el robot va detectando señales (por ejemplo flechas) que le van indicando a dónde ir
- Intentar localizar una pelota en el suelo y empujarla para marcar gol en una portería
- Cualquier otra tarea que se os ocurra...

> NOTA: según sea la tarea que diseñéis es posible que no la podáis probar más que en simulación. Se valorarán las pruebas en los Turtlebot reales (y además son más divertidas que las simulaciones :)) pero también podéis usar otros modelos de robots distintos y simplemente simularlos en ROS.

La tarea puede ser muy diversa pero en general vais a necesitar varios tipos de elementos para implementarla:

- Subtareas de **movimiento**:
    + Algunas subtareas deben mandar comandos de movimiento al robot (por ejemplo: "navega en línea recta", "navega al azar evitando obstáculos". Ya hicisteis algo parecido en la práctica 1.
    + Si tienes un mapa del entorno, algunas subtareas pueden requerir ***navegar* a puntos concretos del mapa** (por ejemplo una tarea de vigilancia). Para eso podéis usar el *stack* de navegación de ROS que se explica en la sección siguiente.   
- Otras subtareas serán de **detección de condiciones** (por ejemplo, "detectar si estoy en un pasillo", o "buscar en la imagen de la cámara una pelota de color rojo"). Para estas tendréis que hacer uso de los sensores del robot.
- Necesitaréis **coordinar las subtareas**: por ejemplo habrá subtareas que se deberán realizar en una secuencia ("primero ve al *waypoint* 1 y luego al 2"), otras serán condicionales ("navega aleatoriamente hasta que te encuentres una pelota"), otras tareas serán en paralelo... En robótica para coordinar este tipo de subtareas se pueden usar varios mecanismos, como las máquinas de estados finitos y los *behavior trees*. También podéis coordinarlas simplemente con los condicionales, bucles, etc. de vuestro código.


> Aunque no es obligatorio, se valorará el uso de alguna librería de máquinas de estados finitos, como [SMACH](http://wiki.ros.org/smach) o [FlexBE](http://wiki.ros.org/flexbe) para coordinar las subtareas

## El *stack* de navegación de ROS

El *stack* de navegación de ROS es un conjunto de nodos, de distintos paquetes, que sirven para gestionar las tareas de navegación en robots móviles. Básicamente el *stack* de navegación resuelve una serie de problemas:

- Localización
- Navegación a un punto del mapa siguiendo la ruta más corta y sin chocar con el entorno 
- Evitación de obstáculos que no están en el mapa 

El *stack* necesita localizarse y por tanto necesita un mapa, por lo que **si queréis usar estas funcionalidades tendréis que disponer del mapa** en formato YAML para el entorno que estéis probando.

### Iniciar el *stack* de navegación con el Turtlebot simulado

Como se puede apreciar en la figura anterior el *stack* está formado por muchos nodos y ponerlos todos en marcha simultáneamente puede resultar complicado. Afortunadamente en los paquetes de turtlebot que hay instalados tenemos un par de *demos* que nos pueden servir:

> IMPORTANTE: **en los ordenadores del laboratorio no funcionan correctamente los paquetes `turtlebot_gazebo` y `turtlebot_stage`**. Podéis bajaros el *workspace* `catkin_ws_fixed` corregido que está en moodle. Para usarlo en lugar de las versiones que vienen por defecto en el laboratorio, descomprimidlo y haced en la terminal:
```bash
cd catkin_ws_fixed
catkin_make
source devel/setup.bash`
```

Para probar con el simulador Gazebo (en 3D) y el mundo por defecto (`playground.world`)

```bash
#simulador
roslaunch turtlebot_gazebo turtlebot_world.launch
#stack de navegación
roslaunch turtlebot_gazebo amcl_demo.launch
#rviz para visualizar el resultado y poder fijar destinos
roslaunch turtlebot_rviz_launchers view_navigation.launch
```

Una vez puesto todo en marcha, puedes probar en `rviz` a fijar un destino para el robot. Clica en el botón que aparece en la barra superior llamado `2D Nav Goal`. Luego clica en el punto del mapa al que quieres que se mueva el robot y opcionalmente arrastra para indicar la orientación final del robot. Si hay un camino factible el robot debería seguirlo y moverse hasta el destino.

Esto también lo puedes hacer por código, bien sea en python o en C++, como verás en el apartado siguiente.

> NOTA: ya veremos en clase de teoría con más detalle cómo funciona el *stack* de navegación. De momento podéis observar en los nodos de rviz (panel izquierdo) que hay un `Local Planning` (evitar obstáculos basándose en lo que detectan los sensores) y un `Global Planning` (planificar la mejor trayectoria según el mapa, sin chocar con los obstáculos reflejados en él)

Si quieres usar un mundo propio tendrás que hacerlo de este modo:

```bash
#simulador
roslaunch turtlebot_gazebo turtlebot_world.launch world_file:=<trayectoria al fichero con el mundo>
#stack de navegación
roslaunch turtlebot_gazebo amcl_demo.launch map_file:=<trayectoria al .yaml del mapa>
#rviz se lanza igual
roslaunch turtlebot_rviz_launchers view_navigation.launch
```

También podéis probar con el simulador *stage*, que es mucho más ligero que gazebo y más fácil de probar.

```bash
#Esto lanza el simulador, el stack de navegación y rviz todo en uno
roslaunch turtlebot_stage turtlebot_in_stage.launch
```
### Iniciar el *stack* de navegación con el Turtlebot real

```bash
roslaunch turtlebot_navigation amcl_demo.launch map_file:=<trayectoria al .yaml del mapa>
```

### Moverse a un punto del mapa en Python

En este ejemplo podéis ver cómo decirle al robot que navegue hasta un determinado punto del mapa. La ruta la calculará automáticamente el *stack* de navegación (siempre suponiendo que tenemos un mapa del entorno). El código ejecuta una **acción**, que es como se representan en ROS las tareas que tardan un tiempo en ejecutarse. En nuestro caso la acción es de tipo `MoveBaseAction`, las que se usan para mover al robot.

> Para probar el siguiente ejemplo no os hace falta ningún workspace de ROS. Simplemente copiad el código a un archivo `test_movebase.py` y ejecutadlo con `python test_movebase.py <x_destino> <y_destino`.

> En RViz, para saber las coordenadas de un punto, pulsad sobre el botón con un "+" de la barra de herramientas y en el cuadro de diálogo elegir `Publish Point`. Aparecerá una nueva herramienta con la que si pasáis el ratón por el mapa en RViz os irá diciendo las coordenadas en metros.

```python
#!/usr/bin/python
# -*- coding: utf-8 -*-
import rospy
import actionlib
from move_base_msgs.msg import MoveBaseAction, MoveBaseGoal
import sys

#Uso de la acción move_base en ROS para moverse a un punto determinado
#En ROS una acción es como una petición de un "cliente" a un "servidor"
#En este caso este código es el cliente y el servidor es ROS
#(en concreto el nodo de ROS 'move_base')
class ClienteMoveBase:
    def __init__(self):
        self.client =  actionlib.SimpleActionClient('move_base',MoveBaseAction)
        #esperamos hasta que el nodo 'move_base' esté activo`
        self.client.wait_for_server()

    def moveTo(self, x, y):
        #un MoveBaseGoal es un punto objetivo al que nos queremos mover
        goal = MoveBaseGoal()
        #sistema de referencia que estamos usando
        goal.target_pose.header.frame_id = "map"
        goal.target_pose.pose.position.x = x   
        goal.target_pose.pose.position.y = y
        #La orientación es un quaternion. Tenemos que fijar alguno de sus componentes
        goal.target_pose.pose.orientation.w = 1.0

        #enviamos el goal 
        self.client.send_goal(goal)
        #esperamos que el robot llegue al punto
        result = self.client.wait_for_result()
        if not result:
            rospy.logerr("No se ha podido ejecutar la acción!!")
        else:
            return self.client.get_result()

if __name__ == "__main__":
    if len(sys.argv) <= 2:
        print "Uso: " + sys.argv[0] + " x_objetivo y_objetivo"
        exit()      
    rospy.init_node('prueba_clientemovebase')
    cliente = ClienteMoveBase()
    result = cliente.moveTo(float(sys.argv[1]), float(sys.argv[2]))
    print(result)
    if result:
        rospy.loginfo("Goal conseguido!")
```

### Cancelar la acción actual

El código anterior usa `wait_for_result` para ejecutar la acción, lo que nos bloquea hasta que la acción se complete. Si mientras se está ejecutando la acción quieres poder hacer otras cosas habría que meterse en un bucle consultando el estado de la acción hasta que ésta se complete.

```python
state = self.client.get_state()
#ACTIVE es que está en ejecución, PENDING que todavía no ha empezado
while state==GoalStatus.ACTIVE or state==GoalStatus.PENDING:
    rospy.Rate(10)   #esto nos da la oportunidad de escuchar mensajes de ROS
    state = self.client.get_state()
```

Tienes un ejemplo completo en [este archivo](movebase_stop.py). Mientras se ejecuta la acción, el código está escuchando mensajes en el topic "/comando", y la acción se cancelará si recibe el mensaje "STOP" en este topic. Si mientras el robot se está moviendo a su destino escribes en una terminal :

```bash
rostopic pub comando std_msgs/String STOP
```
El robot debería cancelar la acción en curso. 

## Entrega de la práctica

### Material a entregar

La documentación de la práctica es una parte muy importante en la puntuación final, ya que es el sitio en que se debe ver reflejado todo el trabajo que habéis hecho.

Debéis no solo documentar la implementación sino también **todos** los experimentos realizados en simulación y con el robot real (¡aun los que no funcionen!. En estos podéis analizar qué es lo que no ha funcionado y cómo lo pretendéis resolver).  

Se debe entregar documentación (en cualquier formato: PDF, HTML, etc.) con los siguientes puntos:

1. Indice del contenido de la práctica 
2. Contenido de la práctica: 
    - Explicación de la tarea que se quiere abordar
    - Explicación de cada una de las subtareas que la componen
    - Si usáis máquinas de estados finitos
       - Esquema de la máquina de estados y descripción general de su comportamiento
       - Descripción de cada uno de los nodos, detallando en su caso los algoritmos/métodos que haya usado cada uno de ellos: por ejemplo si un nodo busca objetos de color azul decir cómo lo habéis hecho.
    - Si no usáis máquinas de estados, explicad cómo se combinan todas las funciones/clases/módulos de vuestro código para realizar la tarea
    - Experimentos realizados en simulación y si los habéis hecho con el robot real
3. Discusión de los resultados (qué ha funcionado, qué no, por qué creéis que han fallado algunas cosas, cómo se podrían resolver, posibles ampliaciones de la práctica,...)

Además de la documentación anterior también debéis entregar todo el **código fuente** que hayáis realizado. En el código debéis incluir comentarios indicando qué hace cada clase y cada método o función.

Si tomáis código de otros **debéis referenciar en la memoria la fuente original**.

### Baremo 

- Para una nota **como máximo de 6**, podéis "limitaros" a probar alguno de los proyectos que ya están implementados en libros o tutoriales de internet. Normalmente estos proyectos ya tienen todo el código hecho y os podéis limitar a bajároslo y probarlo (¡lo que no quiere decir que sea trivial!) **Excepcionalmente, si el proyecto es de especial complejidad** porque requiere por ejemplo de hardware adicional, experimentación extensiva, programación adicional etc. **podéis obtener más nota**. Consultad con el profesor el proyecto en concreto. Podéis encontrar proyectos de este tipo por ejemplo en
    + El libro de ["Programming Robots with ROS"](https://www.oreilly.com/library/view/programming-robots-with/9781449325480/?ar) que ya usamos en la práctica anterior describe algunos proyectos en los capítulos 11, 12 y 14.
    + El libro ["ROS robotics Projects"](https://learning.oreilly.com/library/view/ros-robotics-projects/9781783554713/) trata sobre distintos proyectos realizados con ROS, cada capítulo es un proyecto distinto. Algunos de ellos requieren hardware adicional o son especialmente complejos, por lo que os podrían servir para obtener más nota.
- Para una nota **como máximo de 8**, vuestra tarea debería ser original, propuesta por vosotros y no tomada de un libro/tutorial. Si tomáis la idea de algún sitio, al menos la implementación debería ser vuestra (podéis tomar código de otros por ejemplo para detectar objetos, mover el brazo, ... pero la coordinación y secuenciación de las tareas deberíais hacerla con vuestro código).
- Para una nota **superior a 8**, vuestra tarea debería ser original como en el caso anterior y además cumplir alguna de las siguientes condiciones: 
    - **(hasta 1 punto)** Usar alguna librería de máquinas de estados finitos como FlexBE o SMACH.
    - **(hasta 1 punto)** Que alguna subtarea haga uso de visión: colores, formas, reconocimiento de objetos. Podéis usar cualquier paquete ROS/librería de terceros que encontréis. La nota dependerá de la dificultad de uso y también de la experimentación realizada. 
    - **(hasta 2 puntos)** Que hayáis probado la práctica en los turtlebot reales. Aunque no podáis hacer que funcione toda la práctica se valorará que funcione al menos alguna subtarea 
    - Podéis proponer cualquier otra ampliación que se os ocurra, consultadla antes con el profesor para ver cuánto se podría valorar en el baremo  

### Plazo y procedimiento de entrega

- La práctica se podrá entregar **hasta las 23:59 horas del viernes 15 de enero de 2021**.
- La entrega se realizará a través del moodle

