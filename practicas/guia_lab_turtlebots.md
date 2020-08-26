# Cómo trabajar con los robots turtlebot del laboratorio de robótica

Los turtlebot llevan a bordo un PC (Intel NUC) en el que está instalado ROS Indigo (excepto el número 5 que tiene Kinetic). Este ordenador tiene conexión wifi, aunque no tiene pantalla. Vamos a necesitar un PC adicional para poder interactuar con el robot.



## Conexión "física" con el robot

Lo primero es conectarte a la red inalámbrica local del laboratorio. Hay 3 redes inalámbricas (`labrobot-wifi-5-1`, `labrobot-wifi-5-2`, `labrobot-wifi-2-4`) pero son todas la misma red local (hay 2 redes en 5 G y 1 en 2.4G para dispositivos inalámbricos más antiguos).

> Los PCs del laboratorio no tienen wifi, si estás usándolos pídele al profesor un pendrive wifi de los armarios de material 
 
Arranca el robot con el interruptor de la base. Espera un minuto a que arranque su ordenador de a bordo. Ahora tienes que elegir cuál de los dos métodos siguientes quieres usar para conectar con el robot:

- **Cliente/servidor**: En este modo casi todo el código ROS se ejecuta en nuestro PC y en el robot solo se ejecutan los nodos ROS que controlan el motor y los sensores **Vuestro PC debe tener instalado ROS**.
- **Mediante VNC**: con la wifi podemos emplear los PCs del laboratorio (o tu portátil si lo prefieres) como terminales gráficos para el robot (es decir, básicamente como si fueran la pantalla/teclado/ratón del PC del robot). **No hace falta que vuestro PC tenga ROS** ni esté en linux ya que todo el código ROS se ejecuta en el propio robot.

## Conexión en modo cliente/servidor

> Esta forma ocupa mucho más ancho de banda pero permite usar la herramienta `rviz`. **Necesitarás tener instalado ROS en tu PC**

> Si estás usando una máquina virtual pon la red de la máquina virtual en modo *bridge*

Resumiendo, hay que definir dos variables de entorno, ROS_MASTER_URI y ROS_HOSTNAME, tanto en el turtlebot como en el PC. Veamos los detalles:

```
### En el turtlebot

Primero tenemos que conectar mediante `ssh` con el turtlebot:

```bash
#del turtlebot 1 al 4, el usuario es "turtlebot"
ssh turtlebot@<ip del robot>
#en el turtlebot 5 el usuario es distinto, "tb2"
ssh tb2@<ip del robot>
```

> Las ips de los robots son: (cada robot tiene el número en una pegatina en la parte superior)

>- Turtlebot 1. IP: 192.168.1.5  
>- Turtlebot 2. IP: 192.168.1.6  
>- Turtlebot 3. IP: 192.168.1.7  
>- Turtlebot 4. IP: 192.168.1.8 
>- Turtlebot 5. IP: 192.168.1.9 

Si la conexión funciona correctamente, a partir de este momento lo que escribas en la terminal en realidad se estará ejecutando en el robot:

```bash
export ROS_MASTER_URI=http://localhost:11311
export ROS_HOSTNAME=<ip del turtlebot>
```
Ejecutar los nodos de ROS que queramos arrancar en el robot, por ejemplo

```bash
#arranca los procesos mínimos para que el robot se pueda mover
roslaunch turtlebot_bringup minimal.launch
#arranca el laser (no siempre será necesario)
roslaunch turtlebot_bringup hokuyo_ust10lx.launch
```

### En el PC

```bash
export ROS_MASTER_URI=http://<ip del turtlebot>:11311
export ROS_HOSTNAME=<ip del PC>
``

## Visualizar los datos en el PC con RViz

```bash
rosrun rviz rviz
```

Ver el laser:

> Dará un error de fixed frame en Global Options,  (está puesto a map, pero no hay). Cambiarlo a otro, Por ejemplo a `base_link`

Añadir una visualización de tipo laserScan. Cambiar el topic a /scan

Ver la cámara 2D

En turtlebot arrancar la cámara astra: `roslaunch astra_launch astra.launch`
Añadir visualización de tipo Camera, cambiar topic a `/camera/rgb/image_raw`

Ver la nube de puntos de la cámara astra

Hay que tener arrancada la cámara en el turtlebot: `roslaunch astra_launch astra.launch`

Añadir visualización de tipo DepthCloud, cambiar topic a /camera/depth/image. La nube de puntos sale en la ventana principal


## Conexión con el robot mediante VNC

> Recuerda que TODO el código ROS se ejecutará en el robot, y que el PC solo va a hacer de terminal (pantalla/teclado/ratón). Este método no requiere que tengas instalado ROS en tu PC, y no ocupa demasiado ancho de banda. Como inconveniente, no podrás usar la herramienta `rviz`.
 
Una vez conectado el PC del laboratorio a la wifi, hay que conectar con un robot concreto. Lo haremos mediante VNC, que es un protocolo que permite conectar terminales gráficos a máquinas remotas. El cliente VNC es el equipo que hace de terminal y el servidor el robot.

En cada robot turtlebot ya debería estar funcionando un servidor VNC. Cualquier cliente VNC te debería permitir conectarte a los robots, en Ubuntu el visor por defecto se llama **Remmina VNC**.

Si estás usando Remmina, en el desplegable elige el protocolo VNC y teclea la IP del robot con el que quieras conectar y el password.

- Turtlebot 1. IP: 192.168.1.5 password: turtle_1 
- Turtlebot 2. IP: 192.168.1.6 password: turtle_2 
- Turtlebot 3. IP: 192.168.1.7 password: turtle_3 
- Turtlebot 4. IP: 192.168.1.8 password: turtle_4
- Turtlebot 5. IP: 192.168.1.9 password: turtle_5 

### Arrancar ROS en el robot

Para que los sensores y servicios del robot empiecen a publicar datos en ROS:

```bash
#arranque "mínimo" para que la base se mueva
roslaunch turtlebot_bringup minimal.launch
#laser
roslaunch turtlebot_bringup hokuyo_ust10lx.launch
#cámara RGBD. Nos da la distancia en 3D a la que se encuentra cada pixel de la imagen
roslaunch astra_launch astra.launch
```

> NO es necesario arrancar siempre todos los servicios y sensores, es mejor arrancar los mínimos imprescindibles. Por ejemplo para la práctica 1 no necesitas la cámara RGBD (`astra`), solo el laser, por lo que puedes obviar la última línea

Tras la ejecución de estos comandos el robot estará operativo, publicando información sobre su odometría, sensores de contacto, laser, etc. Para comprobarlo prueba a consultar los topics disponibles, a ver si aparece una lista. Comprueba que aparezca `/scan`, (el laser) que es el que necesitas para la práctica 1

```bash
rostopic list
```
> Si te fijas en el turtlebot simulado verás que no tiene laser y sin embargo también publica un *topic* `/scan`. Esto es porque en la simulación se usa la cámara RGBD como sensor de rango (solo se toma una fila horizontal de pixels). Sin embargo esto no debería afectar a la práctica 1 ya que tu código debería funcionar razonablemente independientemente de la resolución exacta del sensor de rango.

también puedes probar a teleoperar el robot con el teclado para comprobar que se puede mover la base

```bash
roslaunch turtlebot_teleop keyboard_teleop.launch
```

## Copiar archivos entre el PC y el robot

Una forma de copiar archivos es con la herramienta en línea de comandos `scp`.

El robot tiene un usuario linux que se llama `turtlebot` y cuya contraseña es `ros`. Es el que usaremos en `scp`. CUIDADO: el usuario del turtlebot número 5 es `tb2`, no `turtlebot`

> Para windows puedes copiar archivos gráficamente con una aplicación llamada [WinSCP](https://winscp.net/eng/docs/lang:es)

Copiar desde el PC al robot:

```bash
#Esto se teclea DESDE UNA TERMINAL DEL LINUX del PC, NO en el VNC
scp mi_archivo.zip turtlebot@IP_del_robot:~
```

te pedirá la contraseña para el usuario (que en nuestro caso es `ros`) y lo copiará al directorio `home` de ese usuario.

Copiar desde el robot al PC:

```bash
#Esto se teclea DESDE UNA TERMINAL DEL LINUX del PC, NO en el VNC
#FIJATE en el '.' del final, esto hace que se copie en la carpeta actual
scp turtlebot@IP_del_robot:~/mi_archivo.zip .
```

