# Robots Móviles 2020/21. 
# Sesión 2 con los Turtlebot: Mapeado y localización

En esta sesión de trabajo presencial con los Turtlebots vamos a probar los algoritmos de mapeado y localización. Como sabéis solo hay 5 turtlebots por lo que la práctica la tendréis que hacer en grupos.

Los objetivos son:

- **Usar Rviz** para poder ver la información de los sensores del robot real
- **Probar el mapeado y localización** con el laser real
- Aprender otra forma de conectarse con el robot, el **modo cliente/servidor**, donde parte del código corre en el robot y otra parte en vuestro ordenador. 

## Conexión con el robot

> **IMPORTANTE**: en este modo de conexión que usaremos hoy **necesitáis ROS Kinetic en el PC. Lo mejor es que uséis los equipos de los laboratorios. Usad los ordenadores 1,4,8,11 y 15, que tienen conexión wifi**. 

En el modo cliente/servidor, el cliente es nuestro PC y el servidor el robot. Hay algunos nodos de ROS que deben correr en el servidor (por ejemplo el `turtlebot_bringup.launch`) y otros en nuestro PC (por ejemplo `RViz`). En otros es indiferente.

Además de que necesitas ROS en tu equipo, lo más importante del modo cliente/servidor es que **en cada terminal que abras, tanto en tu ordenador como en el robot, debes fijar unas variables de entorno para poder trabajar**, lo que es un poco incómodo 😩, pero inevitable. Tenemos un *script* que lo hace por nosotros pero hay que acordarse de llamarlo en cada terminal.

1. Descarga el *script* [setvars.sh](setvars.sh) que fijará las variables de entorno necesarias para la comunicación cliente/servidor.
2. **Conecta el PC del laboratorio con la red wifi** del laboratorio, recuerda que el nombre comienza por "labrobot". Usa a ser posible las que llevan un 5 en el nombre, son las de 5Ghz y deberían tener un mayor ancho de banda.
3. Abre una terminal de linux y en ella **conecta con el robot** como hacíamos hasta ahora:
    - `ssh turtlebot@ip_del_robot` (en el robot 5 es `ssh tb2@ip_del_robot`), la contraseña es `ros`.
    - Recuerda que el turtlebot 1 es la `192.168.1.5` y así sucesivamente hasta el 5 que es la `192.168.1.9`.
    - En la terminal del robot teclea `source setvars.sh` para fijar las variables de entorno. **Siempre tendrás que hacer esto en cada nueva terminal que abras** (el `setvars.sh` del robot no es el que te has bajado en el paso 1, es uno ya copiado en el robot y adaptado a él).
    - Si necesitas más terminales conectadas al turtlebot (y las necesitarás) tendrás que repetir todo lo del paso 3 de nuevo
    - En esta terminal arranca el robot y el laser: 
    
      ```bash
      #El & se pone para que puedas arrancar ambos desde la misma terminal
      roslaunch turtlebot_bringup minimal.launch & 
      roslaunch turtlebot_bringup hokuyo_ust10lx.launch
      ```

4. Abre otra terminal de linux para **trabajar en tu PC**, y en ella escribe `source setvars.sh numero_del_robot` para fijar las variables de entorno, **por ejemplo `source setvars.sh 1`** para el robot número 1.
    - Si necesitas más terminales para trabajar en tu PC tendrás que ejecutar en ellas el `source setvars.sh numero_del_robot` antes de escribir comandos de ROS
5. Para comprobar que todo está OK, en la terminal abierta en el PC haz un `rostopic list`. Deberían aparecer los *topics* del ROS que está corriendo en el robot, entre ellos los de `/mobile_base`, el `/scan`, etc.

## Pruebas con RViz. Visualización de los sensores

En modo cliente/servidor sí que se puede usar RViz, a diferencia de VNC (lo que usábamos hasta ahora para conectar con los robots).

> Recuerda que **SIEMPRE, en todas las terminales, debes hacer antes el `source setvars.sh numero_de_robot` si la terminal es local al PC (no es un `ssh`) y `source setvars.sh` si está conectada al robot (es un `ssh`)**. A partir de ahora no lo pondremos más, pero recuerda que hay que hacerlo igualmente.


En una **terminal en tu PC** ejecuta RViz con `rosrun rviz rviz`. 

> Aparecerá un error de `fixed frame` en Global Options,  (el origen de coordenadas está puesto a `map`, pero de momento no hay un mapa). **Cambiar el `fixed frame` a `odom`**. Así usará como origen el punto (0,0,0) de la odometría.

### Visualizar el laser

Añadir una visualización para el laser (botón `Add` abajo a la izquierda > en el listado `By Display Type` seleccionar `LaserScan`). Una vez añadida aparecerá en el panel de la izquierda, cambiar el `topic` a `/scan`. Debería aparecer una línea con las distancias detectadas por el laser.

Puedes probar a mover el robot para ver cómo cambian las lecturas. **En una terminal en el PC** lanza el `roslaunch turtlebot_teleop keyboard_teleop`. Recuerda que esta ventana tiene que tener el foco del teclado para que funcione.

> Captura una pantalla en la que se vean las lecturas del laser (o haz una foto a la pantalla) y luego con algún programa gráfico señala qué es lo que estaba "percibiendo" el robot en cada zona (poniendo un texto en cada zona que diga por ejemplo "pared", "mesa", "persona",...). Adjúntalo a la documentación de la práctica.

## Mapeado con teleoperación

Quita el RViz, ya que vamos a ponerlo en marcha con una configuración nueva.

En **terminales en el PC**, escribe:

```bash
#rviz configurado automáticamente para ver el mapa que se va a construir 
#inicialmente dará un montón de errores, muchos desaparecerán
#al empezar a construir el mapa
roslaunch turtlebot_rviz_launchers view_navigation.launch
#esto si no lo tenías ya de antes
roslaunch turtlebot_teleop keyboard_teleop.launch
```

> En nuestros Turtlebot como "parche" para que encuentre la configuración, antes de mapear **en la terminal del robot siempre hay que hacer `export TURTLEBOT_3D_SENSOR=astra`**

En una **terminal del robot**, para crear el mapa escribe:

```bash
export TURTLEBOT_3D_SENSOR=astra
roslaunch turtlebot_navigation gmapping_demo.launch
```

En RViz se debería ver el mapa conforme lo va construyendo el robot. En el panel de la izquierda de RViz desactiva el `Local Map` para que se vea mejor el mapa. Este local map se usa para navegación, y marca las zonas por las que no hay que pasar, pero no deja ver bien el mapa que se está construyendo. 

Cuando lo tengas suficientemente completo, en una *terminal del robot* guárdalo con 

```bash
rosrun map_server map_saver -f nombre_que_quieras_dar_al_mapa
```

Recuerda que se crean dos ficheros, uno con extensión `.pgm`, que es el gráfico con el mapa en sí y otro con extensión `.yaml` que son los metadatos (tamaño en metros del total, tamaño en metros de cada pixel,...).

si quieres tener también una copia del mapa en el PC puedes hacer lo mismo en una terminal del PC, pero al menos deberías guardarlo en el robot, ya que luego hará falta para la localización.

> Si quieres copiar algún archivo del robot al PC también puedes teclear **en una terminal del PC** (y en este caso no te hace falta previamente el `source setvars`)
> 
> ```bash
#FIJATE en el '.' del final, esto hace que se copie en la carpeta actual
scp turtlebot@IP_del_robot:~/mi_archivo_el_que_sea .
```

## Localización

Una vez guardado el mapa, interrumpe el gmapping_demo.launch que tenías funcionando para construir el mapa. Deja RViz funcionando para poder ver la localización.

En una **terminal conectada con el robot** haz: 

```bash
roslaunch turtlebot_navigation amcl_demo.launch map_file:=fichero_mapa.yaml
```

donde: 

- sustituye "fichero_mapa" por el nombre de tu mapa
- tienes que poner la **trayectoria completa** desde la raíz del disco para que ROS lo localice. El directorio HOME del robot es `/home/turtlebot`

Para que el algoritmo de localización funcione **tienes que dar una estimación de la posición inicial en RViz**. 

1. Clica en el botón de la barra superior `2D pose estimate` 
2. Clica en el mapa, **en el punto en el que esté el robot en la realidad**. Una vez clicado, arrastra el ratón para indicar la dirección en la que mira el robot

Puedes ir **moviendo al robot con el "teleop"** y comprobar si en RViz se corresponde la posición con la que tiene en el mundo real.

> Haz un video con el móvil en el que se vea la posición del robot en RViz y también la posición del robot en el mundo real. Adjúntalo a la documentación de la práctica.

## Navegación autónoma a un punto

Para que el robot **navegue automáticamente a un punto** le puedes fijar un destino en RViz con el botón de la barra superior llamado `2D Nav Goal`. IMPORTANTE: antes de esto, interrumpe el proceso de "teleop" ya que interferiría con la navegación.

Fíjale un punto y comprueba si calcula una trayectoria razonable y se mueve correctamente hasta su destino (también puedes adjuntar un video del camino seguido aunque no es estrictamente necesario si has hecho el video anterior). Puedes probar a ponerte en medio de la trayectoria para ver si te detecta como obstáculo imprevisto y te evita en su camino.

## Entrega

La práctica se realiza y por tanto se **entrega por grupos**. La memoria la entregará un único miembro del grupo en moodle. En la memoria es donde figurarán los nombres de todos los componentes.

Cada turno tendrá una semana para hacer la práctica:

- L@s que realicéis la práctica el día 2 la podéis entregar hasta el día 8 a las 23:59.
- L@s que realicéis la práctica el día 9 la podéis entregar hasta el día 15 a las 23:59.

La **memoria** debe incluir:

+ 1-2 páginas describiendo los resultados: si son buenos o malos en general, qué diferencias habéis notado con la simulación (si las hay),
    + imágenes con la visualización del laser y de la cámara RGB/nube de puntos.
    + mapeado: poned una imagen del/los mapa/s, qué partes del mapa han salido mejor/peor, qué partes del laboratorio/mobiliario ha detectado mejor/peor el sensor... (y por qué puede ser, si se os ocurre), 
    +  localización: qué tal ha funcionado, si se pierde el robot en algún momento....
    +  navegación autónoma: qué tal ha funcionado, ¿llega el robot al destino? evita los obstáculos imprevistos?
+ Los archivos con el mapa generado durante la sesión  (.pgm y .yaml) (si hacéis más de una prueba, incluid todos los mapas generados).
+ Para la localización, un video de rviz que muestre al robot navegando por el entorno y actualizando su posición (podéis filmarlo con un móvil para que se vea rviz y también dónde está el robot en el mundo real). 

Al ser el límite de la entrega de 50Mb, si no os cabe todo tendréis que guardar el/los video/s en Google Drive, o subirlo/s a Youtube o similar y luego poner los enlaces en la documentación.



    