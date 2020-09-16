# Instalación de ROS

> *TL;DR:* lo más recomendable es instalarse la **máquina virtual de VMWare con ROS Kinetic** que tienes disponible en el moodle de la asignatura. Es la versión que hay instalada en los laboratorios y además el código que desarrolles en esa versión debería funcionar también con los robots reales. Si ves que no funciona o las simulaciones corren muy lentamente, puedes probar las otras opciones

## "Ramas" de ROS

Actualmente hay dos "grandes ramas" de ROS. Lo que podríamos llamar ROS1 (o ROS "a secas") y ROS2.  

### ¿Por qué hay un ROS1 y un ROS2?

ROS nació para un contexto de uso muy específico (de hecho un único robot llamado PR2), y aunque ha tenido un gran éxito y actualmente se usa en muchos robots, contextos y tareas diferentes, se podría cambiar para adaptarse mejor a muchos casos de uso no contemplados al principio, como por ejemplo plataformas embebidas, multirobots, redes con conectividad pobre.... Si quieres saber más puedes ver la explicación de los desarrolladores de ROS en ¿[Why ROS2](https://design.ros2.org/articles/why_ros2.html)?.

### ¿Qué ROS usaremos nosotros?

**ROS1**, o simplemente ROS. De momento hay muchos más paquetes de terceros disponibles para ROS1, y además los robots que tenemos en el laboratorio tienen instalado ROS1

## ¿Qué versión de ROS1 me instalo en mi ordenador y cómo ?

### Versiones de ROS1

No hay una única versión de ROS1 (en adelante, simplemente "ROS"), sino que se va actualizando cada cierto tiempo

**ROS solo es compatible con Linux, y además está preparado específicamente para Ubuntu**. Cada versión de ROS está preparada para una versión de Ubuntu concreta. Por ejemplo Kinetic requiere de Ubuntu 16.04, Melodic del 18.04 y la última, Noetic del 20.04.

### ¿Entonces qué versión de ROS debería instalar?

Yo os recomiendo **Kinetic** porque es el mismo  de los laboratorios y tiene mejor integración con los robots reales. Si te viene mejor instalarte otra en tu ordenador también te valdrá, pero tendrás que tener en cuenta que:

- En la asignatura no podemos dar soporte a todas las versiones de ROS, así que si algo no funciona haré lo posible por ayudarte pero tendrás que ingeniártelas por tu cuenta
- Tendrás que adaptar las instrucciones que se dén en las prácticas a cómo se hagan las cosas en tu versión de ROS (aunque no debería ser muy complicado)
- Tu código va a ser más difícil que se pueda ejecutar en el robot real. Para que esto no sea un problema, se intentará que las prácticas con el robot real se puedan preparar y probar integramente en el tiempo de prácticas presenciales.

## Opciones de instalación

### 1. **Máquina virtual VMware con Kinetic** disponible en el moodle de la asignatura

- **Ventajas**: sencilla de instalar, es el mismo entorno que en los ordenadores del laboratorio, tiene soporte para los Turtlebot2 (los robots que tenemos en el laboratorio)
- **Desventajas**: necesita de un ordenador que no sea antiguo y tenga suficiente RAM para funcionar, sobre todo la simulación

### 2. Instalar **Ubuntu nativo** en el ordenador **+ la versión de ROS correspondiente**

- **Ventajas**: mejor rendimiento
- **Desventajas**: salvo ROS Kinetic las otras versiones de ROS tienen un soporte pobre para los robots del laboratorio, los Turtlebot
- [Instrucciones para Ubuntu 16.04 + ROS Kinetic](http://wiki.ros.org/kinetic/Installation/Ubuntu)
- [Instrucciones para Ubuntu 18.04 + ROS Melodic](http://wiki.ros.org/melodic/Installation/Ubuntu)
- [Instrucciones para Ubuntu 20.04 + ROS Noetic](http://wiki.ros.org/noetic/Installation/Ubuntu). Noetic es un cambio más grande que Melodic, en gran medida porque usa Python 3 en lugar de 2, así que prácticamente nada del código que hagas para Noetic te va a funcionar en versiones inferiores

### 3. ROS "en la nube"

Hay varios servicios "en la nube" donde puedes ejecutar ROS sin instalar nada en tu máquina:

- [ROS Development Studio](http://rds.theconstructsim.com/), de TheConstructSim: es el único que conozco que es **gratuito**, aunque no permite tener el código privado, por lo que no te serviría para hacer todas las prácticas (tendrías que crear y borrar el proyecto cada vez si no quieres que se quede público), pero sí para trabajar algún día aislado. También tienen planes de pago que permiten código privado y mejores máquinas virtuales con más rendimiento.
- En Azure o AWS puedes ejecutar máquinas virtuales con ROS preinstalado, aunque son servicios de pago. No obstante estos sitios tienen cuentas con crédito gratuito para estudiantes