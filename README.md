# LABORATORIO: ANÁLISIS Y OPERACIÓN DEL MANIPULADOR MOTOMAN MH6

### 1. CUADRO COMPARATIVO ENTRE LOS ROBOTS MOTOMAN MH6 Y ABB IRB140:

A continuación se muestra el cuadro comparativo entre el robot Motoman MH6 y el ABB IRB140.

![](https://github.com/ayromerod/Lab_Motoman_MH6/blob/main/Figuras/CuadroComparativo.PNG?raw=true)

El Motoman MH6 es más adecuado para aplicaciones que requieren mayor alcance y velocidad, mientras que el ABB IRB 140 destaca por su precisión y es más utilizado en tareas de mecanizado y laboratorio.

### 2. DESCRIPCIÓN DE LAS CONFIGURACIONES HOME 1 Y HOME 2 DEL ROBOT MOTOMAN MH6:

A continuación se muestra la posición del manipulador en HOME 1:

![](https://github.com/ayromerod/Lab_Motoman_MH6/blob/main/Figuras/Home1.jpg?raw=true)

Los ángulos correspondientes a esta posición de HOME 1 se muestran encerrados en un recuadro rojo en la siguiente figura:

![](https://github.com/ayromerod/Lab_Motoman_MH6/blob/main/Figuras/Home1_angulos.jpg?raw=true)

A continuación se muestra la posición del manipulador en HOME 2:

![](https://github.com/ayromerod/Lab_Motoman_MH6/blob/main/Figuras/Home2.jpg?raw=true)

Los ángulos correspondientes a esta posición de HOME 2 se muestran encerrados en un recuadro rojo en la siguiente figura:

![](https://github.com/ayromerod/Lab_Motoman_MH6/blob/main/Figuras/Home2_angulos.jpg?raw=true)

Como se puede observar en las anteriores 4 figuras, la posición HOME 1 y HOME 2 son bastante diferentes. En un ambito industrial, habitat natural de los robots manipuladores, las posiciones de HOME se determinan en función de las condiciones de trabajo y de las necesidades especificas que se requieran en cada lugar y situación. Por ejemplo, la posición HOME 1 mostrada podría ser útil cuando el robot no se está usando y se desea que ocupe la menor cantidad de espacio posible sin estorbar ni obstaculizar nada ni a nadie. Sin embargo, si se desea realizar alguna reparación al servo motor de la base, esta posición de HOME 1 no es la más adecuada y en lugar de eso la posición de HOME 2 recién mostrada parece ser una mejor alternativa en ese caso. Así mismo, habrá otra posición HOME 3 que sea ideal cuando se requiera cambiar de herramienta. En conclusión, ninguna posición HOME es mejor que la otra, y dependiendo de las necesidades, cada una será más adecuada que la otra. 

### 3. PROCEDIMIENTO PARA REALIZAR MOVIMIENTOS MANUALES EN EL ROBOT MOTOMAN MH6:

### 4. VELOCIDADES MANUALES EN EL ROBOT MOTOMAN MH6:

Existen 4 movimientos de velocidad manual, [INCH] (Inching) - [SLW] (Slow Speed) - [MED] (Medium Speed) - [FST] (High Speed); nombrados respectivamente desde el movimiento más lento, hasta el movimiento más rápido.

![](https://github.com/ayromerod/Lab_Motoman_MH6/blob/main/Figuras/DX100MH6%20UN%20v2.pptx.pdf-image-085.jpg?raw=true)

La velocidad manual se puede cambiar con los botones [FAST] y [SLOW], [FAST] incrementando la velocidad, y [SLOW] disminuyendola, así como se enseña en el siguiente diagrama:

![](https://github.com/ayromerod/Lab_Motoman_MH6/blob/main/Figuras/DX100MH6%20UN%20v2.pptx.pdf-image-086.jpg?raw=true)

Se puede saber en qué nivel de velocidad se encuentra el robot, mirando la parte superior de la interfaz.

![](https://github.com/ayromerod/Lab_Motoman_MH6/blob/main/Figuras/DX100MH6%20UN%20v2.pptx.pdf-image-038.jpg?raw=true)

### 5. DESCRIPCIÓN DE LAS PRINCIPALES FUNCIONALIDADES DE RoboDK:

RoboDK es un software que permite la simulación y la programación offline de robots industriales. Permite generar trayectorias y enviar comandos a robots físicos sin  la necesidad de programarlos manualmente en su controlador. Para el caso del robot Motoman de Yaskawa la comunicación se realiza mediante el controlador propio del robot a través de archivos de programas (JBI) que tienen las instrucciones de acción para el robot.

Para el laboratorio se utilizó la API de RoboDK en Python, esta API no es exclusiva de Python y se puede usar en varios lenguajes de programación como C++, C#, Java y Matlab. Utiliza el módulo Robolink que funciona como un intermediario entre el código y el entorno de simulación
```
from robodk.robolink import *

RDK = Robolink()
```

Para enviar comandos al robot en tiempo real se añaden los respectivos comandos del API luego de haber instanciado el robot, el frame y la herramienta

```
robot = RDK.Item("Motoman", ITEM_TYPE_ROBOT)
frame = RDK.Item("Base", ITEM_TYPE_FRAME)
tool = RDK.Item("Gripper", ITEM_TYPE_TOOL)

robot.MoveJ()
robot.MoveL()
robot.MoveC()
```
Luego RoboDK utiliza MotoCom o MotoPlus para enviar los comandos en tiempo real al robot mediante comunicación ethernet. O bien se pueden enviar los programas al controlador mediante USB o FTP en cuyo caso el código se ejecuta desde el Teach Pendant.

### 6. COMPARACIÓN ENTRE RoboDK Y RobotStudio:

### 7. CÓDIGO DESARROLLADO EN ROBODK PARA EJECUTAR UNA TRAYECTORIA POLAR CON EL ROBOT MOTOMAN MH6:

A continuación se observa el código phyton implementado para ejecutar la rutina del Motoman MH6:

```python

from robodk.robolink import *    # API para comunicarte con RoboDK
from robodk.robomath import *    # Funciones matemáticas
import math

#------------------------------------------------
# 1) Conexión a RoboDK e inicialización
#------------------------------------------------
RDK = Robolink()

# Elegir un robot (si hay varios, aparece un popup)
robot = RDK.ItemUserPick("Selecciona un robot", ITEM_TYPE_ROBOT)
if not robot.Valid():
    raise Exception("No se ha seleccionado un robot válido.")

#------------------------------------------------
# 2) Cargar el Frame (ya existente) donde quieres dibujar
#    Ajusta el nombre si tu Frame se llama diferente
#------------------------------------------------
frame_name = "Frame_from_Target1"
frame = RDK.Item(frame_name, ITEM_TYPE_FRAME)
if not frame.Valid():
    raise Exception(f'No se encontró el Frame "{frame_name}" en la estación.')

# Asignamos este frame al robot
robot.setPoseFrame(frame)
# Usamos la herramienta activa
robot.setPoseTool(robot.PoseTool())

# Ajustes de velocidad y blending
robot.setSpeed(100)   # mm/s - Ajusta según necesites
robot.setRounding(5)  # blending (radio de curvatura)

#------------------------------------------------
# 3) Parámetros de la figura (rosa polar)
#------------------------------------------------
num_points = 720       # Cuántos puntos muestreamos (mayor = más suave)
A = 150                # Amplitud (300 mm = radio máximo)
B = 1.8
k = 5                  # Parámetro de la rosa (pétalos). Si es impar, habrá k pétalos; si es par, 2k
z_surface = 0          # Z=0 en el plano del frame
z_safe = 50            # Altura segura para aproximarse y salir

#------------------------------------------------
# 4) Movimiento al centro en altura segura
#------------------------------------------------
# El centro de la rosa (r=0) corresponde a x=0, y=0
robot.MoveJ(transl(0, 0, z_surface + z_safe))

# Bajamos a la "superficie" (Z=0)
robot.MoveL(transl(0, 0, z_surface))

#------------------------------------------------
# 5) Dibujar la rosa polar
#    r = A * sin(k*theta)
#    x = r*cos(theta), y = r*sin(theta)
#------------------------------------------------
# Recorremos theta de 0 a 2*pi (una vuelta completa)
full_turn = 2*math.pi

for i in range(num_points+1):
    # Fracción entre 0 y 1
    t = i / num_points
    # Ángulo actual
    theta = full_turn * t

    # Calculamos r
    r = A * math.sin(k * theta) + B * theta

    # Convertimos a coordenadas Cartesianas X, Y
    x = r * math.cos(theta)
    y = r * math.sin(theta)

    # Movemos linealmente (MoveL) en el plano del Frame
    robot.MoveL(transl(x, y, z_surface))

# Al terminar, subimos de nuevo para no chocar
robot.MoveL(transl(x, y, z_surface + z_safe))

print(f"¡Figura (rosa polar) completada en el frame '{frame_name}'!")
```
### 8. VIDEO DE SIMULACIÓN EN ROBODK MOSTRANDO LA TRAYECTORIA POLAR CON EL ROBOT MOTOMAN MH6:

Dando click en la siguiente imagen lo redirigirá al video de la simulación donde el robot ejecuta la trayectoria polar.

[![Ver video](https://github.com/ayromerod/Lab_Motoman_MH6/blob/main/Figuras/CapturaSimulacion.PNG?raw=true)](https://drive.google.com/file/d/14JbyRtTl9OlgKsnysJ1sjEwKrNEW9lJ9/view?usp=sharing)

### 9. IMPLEMENTACIÓN EN EL MANIPULADOR MOTOMAN MH6 DE FORMA FÍSICA:

Dando click en la siguiente imagen lo redirigirá al video de la implementación física donde el robot ejecuta la trayectoria polar en la vida real.

[![Ver video](https://github.com/ayromerod/Lab_Motoman_MH6/blob/main/Figuras/CapturaEjecucionReal.PNG?raw=true)](https://drive.google.com/file/d/1c34hLpIiWrlbkiJAAfrrLgcEIB8qxs0G/view?usp=sharing)
