<center style="font-weight: bold; font-size: 25 ">ENTREGA 3: Memoria Bloque 2</center>

<center style="font-weight: bold; font-size: 18 ">Daniel Gozálvez Sánchez</center>

```toc
title: "## Índice"
style: number 
min_depth: 1
max_depth: 2
```

# Tareas Básicas

## Tarea 1: Control ON-OFF

```ad-statement
Interfaz UI en node-red para el control ON-OFF y monitorización del estado de todos los  
actuadores (relés de enchufes y luces) en un panel UI
```

Para realizar el sistema de monitorización y control de encendido y apagado de los actuadores utilizaremos este conjunto de nodos:

![[Pasted image 20230113110146.png]]

<center>Figura 1: Nodos usados en la interfaz de control ON-OFF para el actuador 'pasillo' </center>

El conjunto de nodos usados para el actuador "pasillo"  será la base para el resto de actuadores. Este conjunto de nodos contiene lo siguiente:

```ad-paragraph
- !!!col
	- 1
		![[Pasted image 20230113111546.png]]
	- 1
		Un receptor MQTT el cual esta conectado al Topic ==shellies/luz/pasillo/relay0==
```

Este receptor es capaz de leer los mensajes mandados a un "Topic" y enviar el mensaje recibido a otro nodo. En este caso, el mensaje se envía a una bombilla la cual se ha configurado para encenderse y apagarse con las señales recibidas del MQTT.  

```ad-paragraph
- !!!col
	- 1
		![[Pasted image 20230113112006.png]]
	- 1
		Un emisor MQTT el cual esta conectado al Topic ==shellies/luz/pasillo/relay0/command==
```

Este emisor enviará la señal que le enviemos al "Topic" seleccionado. En este caso la señal la enviarán dos botones, uno de encendido y otro de apagado, que enviaran una señal con el paquete `on` y `off` respectivamente.

Usando la misma estructura de nodos, implementamos el resto de actuadores:

![[Pasted image 20230113112456.png]]

<center>Figura 2: Nodos usados en la interfaz de control ON-OFF para todos los actuadores </center>

Ahora para modificar como quedarán en la interfaz se modificará la disposición de estos en el Dashboard de Node-Red:

![[Pasted image 20230113113415.png|center]]

<center>Figura 3:  Disposición del DashBoard para el control de encendido de los actuadores </center>


Se ha dividido cada nodo relacionado con el Dashboard en una subtabla, así se mostrará ordenado y separado por categorías. Cada subgrupo se ordena de la misma manera que "comedor".


Este sería el resultado:

![[Pasted image 20230113113239.png]]

<center>Figura 4: Dashboard con el control de encendido de los actuadores </center>


## Tarea 2: Monitorización de consumo

```ad-statement
Interfaz UI en node-red para monitorizar del consumo de la vivienda (medidor-consumo en canal 1)
```

Se nos pide leer un monitorizador del consumo, para ello recurriremos a este conjunto de nodos:

![[Pasted image 20230113114606.png]]

<center>Figura 5: Lectura del Monitorizador</center>

Se trata de dos receptores MQTT escuchando en los topics `shellies/medidor_consumo/emeter/1/power` y `shellies/medidor_consumo/emeter/1/voltage` respectivamente conectados a un medidor del Dashboard (con los valores máximos y mínimos modificados) que mostrará la información por pantalla.

Este sería el resultado:

![[Pasted image 20230113115032.png|center]]

<center>Figura 6: Medidores de consumo en el Dashboard</center>

## Tarea 3: Escenas

```ad-statement
Interfaz UI en node-red de escenas (llego a casa, no hay nadie, modo cine, generar actividad de presencia). Una escena consiste en conectar/desconectar varios dispositivos a la vez
```

Para añadir las escenas solo tendremos que añadir un botón que active varios emisores MQTT al mismo tiempo:

![[Pasted image 20230113115512.png|center]]

<center>Figura 7: Proyecto de Node-Red con las Escenas añadidas</center>

Lo que se ha realizado es un botón que activa otro botón seleccionando la opción de activar cuando se recibe una señal.

Las escenas añadidas son las siguientes:

- Apagar todo -> Apaga todos los actuadores.
- Llegar a Casa -> Enciende Pasillo y Comedor
- Cocinar -> Enciende Enchufe 1 y 2
- Modo Lectura -> Apaga todo y enciende Dormitorio

Este sería el resultado:

![[Pasted image 20230113115845.png|center]]

<center>Figura 8: Dashborad con las Escenas añadidas</center>

```ad-tip
title: Apunte

Para ver el funcionamiento de las Tareas 1-3 visualizar el video adjunto
```

## Tarea 4: Gestión Energética

```ad-statement
Interfaz UI en node-red de gestión energética (algoritmos como este: si consumo sobrepasa valor apago enchufes, etc..)
```

Para realizar la gestión energética se colocará un bloque función que compruebe si el voltaje es superior a 200 (en este caso):

```ad-code
``` javascript

var value = msg.payload;
if (value > 200){
	msg.payload = "off";
}
return msg;
```

Una vez hecha la comparación se enviaría una señal de apagado a todos los emisores MQTT.

Este sería el resultado:

![[Pasted image 20230113183628.png]]

<center>Figura 9: Proyecto de NodeRed con gestión energética</center>


## Tarea 5: Control Ambiental

```ad-statement
Desarrollar algoritmos de control ambiental utilizando los valores de la Temperatura y  
Humedad del sensor BLE
```

No se ha podido realizar un algoritmo de control ambiental debido a que no se ha podido realizar la conexión con el sensor de Temperatura y Humedad BLE.

Si se quisiera realizar un algoritmo de control ambiental se debería realizar un sistema parecido al de la Figura 9:

1. Deberíamos leer el valor de temperatura y humedad.
2. Crearíamos un bloque función que compare los valores obtenidos
3. Enviaríamos señales a los actuadores indicados mediante un emisor MQTT.

## Tarea 6: Análisis TFG

```ad-statement
Analiza este trabajo: DESPLIEGUE DE PROYECTOS PARA LA MONITORIZACIÓN  
EN SMARTCITIES USANDO NODERED por Mario Berbel Bueno
```

En el TFG de Mario Berbel Bueno se expone la idea de realizar un proyecto de monitorización enfocado el uso de Node-Red.

Para este proyecto, se ha separado la implementación en varias secciones:

- **RaspBerry Pi**: En el proyecto se utiliza una Raspberry Pi como ordenador principal, el cual contendrá la gran mayoría de recursos del proyecto.

- **Node-Red**: Se usará Node-Red como programa principal y como herramienta de muestreo de resultados, usando su función de DashBoard.

- **MongoDB**: Se usará MongoDB como base de datos local debido a la gran cantidad de datos que se va a almacenar, añadiendo que no se requiere de una base de datos relacional para este supuesto.

- **FiWare**: Se usará FiWare como base de datos remota.

- **SenseHat**: SenseHat es un dispositivo el cual alberga sensores los cuales se utilizaran para obtener lecturas de la situación actual en el entorno. Este dispositivo contiene sensores de temperatura, humedad, sensor de presión, giroscopio, acelerómetro y magnetómetro.

El objetivo principal del TFG es aprender a usar estos dispositivos y demostrar su facilidad de uso y reducido coste. Para demostrar esto, se plantea realizar un registro de datos obtenidos mediante los sensores para su posterior envío a una base de datos remota y la capacidad de ser mostrados mediante un entorno grafico, como la DashBoard de Node-Red.

# Tareas Avanzadas

## Tarea 2: Base de Datos

```ad-statement
Desplegar una base de datos para almacenar consumos y eventos producidos en la  
vivienda (https://flows.nodered.org/node/node-red-contrib-influxdb)
```

Para realizar la base de datos se han realizado dos contenedores con Docker (el proceso esta explicado en la entrega de Docker, aunque dejo el PDF).

Se usará MYSQL como base de datos. Para ello descargaremos el paquete `node-red-node-mysql` en Node-Red.

Colocaremos en nodo de la base de datos y lo configuramos:

![[Pasted image 20230114161001.png|center]]

<center>Figura 10: Configuración del nodo MYSQL</center>

Después haremos nodos encargados de enviar sentencias SQL a la base de datos, como la creación de la tabla, la lectura y la inserción de datos.

![[Pasted image 20230114160355.png]]

<center>Figura 11:  Nodos para el envió de datos al servidor SQL</center>

Este sería el Código:

```ad-code
Create Table
```javascript
msg.topic = "CREATE TABLE Test (output varchar(255), id MEDIUMINT NOT NULL AUTO_INCREMENT);"
return msg;
```

```ad-code
Read DB
```javascript
msg.topic = "SELECT * FROM Test;"
return msg;
```

```ad-code
Insert Form
```javascript
msg.topic = "INSERT INTO Test VALUES ('" + msg.payload + "';)"
return msg;
```

## Tarea 3: Analizar Nodo SVG

```ad-statement
Analizar el nodo `node-red-contrib-ui-svg`
```

En nodo SVG nos permite generar dibujos SVG dentro del Dashboard, además de eso, también nos permite interactuar de manera dinámica con estos nodos.

## Tarea 4: Analizar Nodo QR

```ad-statement
Analizar el nodo `node-red-contrib-qrcode-generator`
```

Este nodo nos permite crear códigos QR que lleven a direcciones web, WIFI, teléfonos, SMS, emails y ubicaciones.

Para usarlo primero colocaremos el nodo y le indicamos el valor que queremos representar en el código QR:

![[Pasted image 20230113194545.png|center]]

<center>Figura 12: Configuración del nodo de Códigos QR</center>

Después de colocar el link, ahora falta mostrarlo en el DashBoard, para ello colocaremos un nodo de "Template" y le colocaremos dentro del código `<img src={{msg.payload}}>` 

![[Pasted image 20230113194824.png|center]]

<center>Figura 13: Proyecto NodeRed para la visualización de un Código QR</center>

Este sería el resultado:

![[Pasted image 20230113195044.png]]

<center>Figura 14: DashBoard con Código QR (Link: WhatshapWeb)</center>


## Tarea 5: Analizar nodo Formulario

```ad-statement
Analizar este código:

![[Pasted image 20230114120155.png]]
```

En este código se muestra como al hacer elemento interactuables en el Dashboard todos envían una señal directa al modificarse (si la opción que realiza esa acción esta activada):

![[Pasted image 20230114121258.png|center]]

<center>Figura 15: Opción de envió de información al cambio o salida del elemento</center>

Lo que nos enseña este proyecto es que si se desea recoger la información de varios nodos en un momento especifico se complicaría el código que debe manejar esta operación.

No obstante existe un nodo en el paquete Dashboard que nos permite facilitar esto. Este nodo es el Formulario.

En el formulario nos deja introducir varios campos con presets como `String`, `email`, `password` , etc. Además nos permite decir si es obligatorio el campo.

Solamente se enviará toda la información una vez se presione el botón 'Sumbit'.

## Tarea 6: QR, Formulario y Base de Datos

```ad-statement
Relacionar un QR con la llamada a un formulario en local y registrar los datos en base  
de datos (se completa el ciclo de los tres puntos anteriores).
```

Para realizar esto primero vamos a usar la base de datos utilizada en la Tarea 2.

Para ligar el QR con el formulario vamos a crear dos apartados del Dashboard, uno con el QR y otro con el formulario.

Para el QR lo configuraremos igual que en la Tarea 4, pero esta vez lo asignaremos la URL `http://0.0.0.0:1880/ui/#!/0`:

![[Pasted image 20230114123158.png|center]]

<center>Figura 16: Creación del Código QR</center>


Una vez hecho el QR vamos a realizar el formulario:

![[Pasted image 20230114123511.png|center]]

<center>Figura 17: Configuración del Formulario</center>

Se crearan tres campos obligatorios. El formulario solo enviara la información cuando el usuario presione el botón 'Submit'.

Ahora lo juntamos con la base de datos:

![[Pasted image 20230114123438.png|center]]

<center>Figura 18: Nodos de la Tarea 6</center>

Para conectar la base de datos primero hemos creado dos funciones, una que crea una tabla y otra que lee la base de datos con estos códigos respectivamente:

```ad-code
``` javascript
msg.topic = "CREATE TABLE Test (name varchar(255), email varchar(255), data varchar(255));"
return msg;
```

```ad-code
``` javascript
msg.topic = "SELECT * FROM Test;"
return msg;
```

Por último la subida de datos se ha realizado el siguiente código:

```ad-code
```javascript
msg.topic = "INSERT INTO Test VALUES ('" + msg.payload.Name + "', '" + msg.payload.Email + "', '" + msg.payload.Data + "');" 
return msg;
```

Esta seria una prueba de uso:

![[Pasted image 20230114124227.png]]

<center>Figura 19: Dashboard con el QR</center>

![[Pasted image 20230114124343.png]]

<center>Figura 20: Dashboard con el Formulario</center>

![[Pasted image 20230114124433.png|center]]

<center>Figura 21: Salida por debug al realizar el Select</center>

