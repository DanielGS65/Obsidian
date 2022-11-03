<center style="font-weight: bold; font-size: 25 ">Memoria Bloque 1</center>

<center style="font-weight: bold; font-size: 18 ">Daniel Gozálvez Sánchez</center>

```toc
title: "## Índice"
style: number 
min_depth: 2
max_depth: 2
```

## Capturar Datos de OpenWeather


```ad-question
title:Enunciado

Para la primera sesión se nos pide utilizar la herramienta **_Docker_** para crear un entorno en el que utilizar **==_NodeRed_==**. Mediante NodeRed se nos pide realizar una conexión con el servicio **_==OpenWeather==_** para recibir datos de del tiempo registrados en ciertas zonas del planeta.
```

```ad-info 
title:Objetivos

- Crear un Docker con NodeRed.
- Registrarse en OpenWeather.
- Usar OpenWeather en NodeRed
```

Primero se ha instalado Docker en la máquina virtual de Ubuntu.

Después de esto se ha realizado la descarga y puesta de NodeRed colocando en el terminal la siguiente instrucción:

```
docker run -it -p 1880:1880 -v node_red_data:/data --name mynodered nodered/node-red
```

Esto nos creara una imagen de Docker llamada mynodered.

Para encender o apagar esta maquina debemos poner respectivamente:

```
// Encender
docker start mynodered

// Apagar
docker stop mynodered
```

Cuando se encienda NodeRed estará situado en la dirección `http://localhost:1880` :

![[Pasted image 20221028165302.png]]

Con **NodeRed** ya activo ahora se nos pide instalar varios paquetes para habilitar algunos paquetes que nos dejaran conectarnos a la API de OpenWeather.

Una vez hecho la instalación de los paquetes realizaremos el siguiente programa:

![[Pasted image 20221028170222.png]]

Este programa tiene los siguientes componentes:

- **TimeStamp** 
	- Un actuador el cual enviara una señal de activación al cumplirse las condiciones asignadas, en nuestro caso, sería al pulsar el botón azul situado a la izquierda de este.
-  **OpenWeatherMap Node**
	- Nodo el cual realiza una petición HTTP para obtener los datos de la API de OpenWeather.
- **Debug OutPut**
	-  Canal de salida que nos monstrará en la consola de Debug los resultados obtenidos


```ad-warning
title: Configuración OpenWeatherMap Node

Antes de que funcione, tenemos que configurar el nodo de OpenWeather:
- !!!col
	- 1
		![[Pasted image 20221028170428.png|center]]
	- 1
		Debemos asignar la **==API Key==**  la cual la encontraremos en la web de OpenWeather una vez nos registremos.

Después debemos poner la ubicación de la cual queremos la información, en nuestro caso de Alicante/España.


```

Una vez hecho esto, lanzamos en proyecto y nos devolverá los siguiente por el terminal de debug:


```ad-check
title: Resultado

- !!!col
	- 1
		![[Pasted image 20221028170731.png|center|200]]
	- 2
		Como podemos observar nos devuelve un conjunto de datos relacionados con la tiempo en  Alicante, como la temperatura, humedad, presión y velocidad del aire, todo esto enviado por el nodo DebugOtput.
```


## Monitorización y DashBoard

```ad-question
title:Enunciado

Se busca utilizar **NodeRed** para ==realizar una aplicación Web de visualización de la información== de forma que nos proporcione de una manera visual toda la información obtenida en el programa.
```

```ad-info
title:Objetivos

- Utilizar el paquete Dashboard de NodeRed
- Crear un Dashboard en NodeRed
```

Primero necesitaremos instalar los paquetes necesarios de NodeRed para permitir la utilización del Dashboard.

Una vez instalados vamos a crear el programa:

![[Pasted image 20221029154356.png|center]]

Vamos a separar el programa en dos secciones, la **_Sección Funcional_** y la **_Sección Visual_**:

```ad-note
title: Sección Funcional

Vamos a explicar que función realiza cada nodo:
![[Pasted image 20221029154646.png|center]]

- **_TimeStamp_**
	- !!!col
		- 1
			![[Pasted image 20221029155058.png|center]]
		- 1 
			Se ha configurado el TimeStamp de tal manera que este mandará una señal después 0.1 segundos de iniciar el programa y después de esa señal enviará una cada 5 segundos

- **_Random_H/Random_T_**
	- Generadores de números aleatorios que nos proporcionarán los valores a representar (H-> Humedad / T-> Temperatura) cada uno de estos tiene asignado un rango de valores.
	```ad-warning
	title: Error en la generación de números aleatorios
	El paquete de generación de números aleatorios en NodeRed no funciona de manera correcta, devolviendo valores fuera del rango.
	```
- **_Change_H/Change_T_**
	- !!!col
		- 1
			![[Pasted image 20221029160606.png|center]]
			-------------------------------------------
			![[Pasted image 20221029160631.png|center]]
		- 1
			Estos nodos nos permiten transformar ciertas variables a otras, transformado su ámbito para poder utilizarlas. En este caso estamos transformado las variables locales que se han creado a partir de los números aleatorios y las estamos colocando en el **msg.payload**, haciendo que estas puedas ser utilizadas por otros nodos fuera del ámbito.

- **_Debug_H/Debug_T_**
	- Estos nodos imprimirán los datos obtenido de los anteriores nodos.


### **_==Resultados==_**
- !!!col
	- 1
		![[Pasted image 20221029161804.png|center]]
	- 1
		Como podemos ver cada nodo Debug nos muestra los valores aleatorios obtenidos.
```

```ad-tip
title:Sección Visual

Vamos a explicar la función que realiza cada Nodo:

![[Pasted image 20221029162158.png|center]]

- **_TimeStamp_**
	- !!!col
		- 1
			![[Pasted image 20221029155058.png|center]]
		- 1 
			Al igual que el anterior, se ha configurado el TimeStamp de tal manera que este mandará una señal después 0.1 segundos de iniciar el programa y después de esa señal enviará una cada 5 segundos

- **_Button_**
	- Elemento de DashBoard que nos servirá para activar de manera manual los nodos de la interaz gráfica desde la misma.

- **_get_H/get_T_**
	- !!!col
		- 1
			![[Pasted image 20221029163223.png|center]]
			-------------------------------------------
			![[Pasted image 20221029163307.png|center]]
		- 1
			Estos nodos tienen como función obtener las variables de humedad y temperatura del _msg.payload_ pasando así la variable a su ámbito y pasándola a los siguientes nodos.

- **_gauge_H/gauge_T_**
	- Estos nodos son dos objetos gráficos mostraran la información en el dashboard.
	- Gauge_T es de tipo Manecilla.
	- Gaguge_H es de tipo Liquido.

- **_Humedad/Temperatura_** 
	- Dos nodos que son dos campos de texto para el DashBoard.

### **_==Resultados==_**
- Para acceder al resultado nos debemos ir a la siguiente dirección
`http://localhost:1880/ui`

![[Pasted image 20221029164114.png|center]]
```


### Ampliación

Para la ampliación se ha usado el programa del ejercicio anterior y se ha mostrado mediante Dashboard los resultados:

![[Pasted image 20221029164535.png|center]]

Vamos a explicar los nodos añadidos:

- **_Button_**
	- Se ha añadido un botón para solicitar la información a la API desde el DashBoard. No se ha automatizado el timestamp para no solicitar demasiadas peticiones a la API.
- **_Function_**
	- Tenemos tres funciones: getId, getTemperature, getHumidity. Cada una de estas extrae el dato del JSON que nos devuelve la API de OpenWeather.
``` java
// getId
var json = msg.payload;
var id = json["id"];
msg.payload = " " + id;
return msg;

//getTemperature
var json = msg.payload;
var temp = json["tempc"];
msg.payload = " " + temp;
return msg;

//getHumidity
var json = msg.payload;
var hum = json["humidity"];
msg.payload = " " + hum;
return msg;
```

- **_Country_ID_**
	- Muestra la ID del lugar donde se obtienen los datos mediante un texto dentro del DashBoard.
- **_Temperature / Humidity_** 
	- Dos Manecillas que nos muestran los valores de humedad y temperatura.


 ### **_==Resultados==_**
![[Pasted image 20221029165330.png|center]]



## Control de Aparatos y Visualización del Consumo

```ad-question
title: Enunciado

Se nos entrega un programa de NodeRed compuesto por 4 secciones que realiza una serie de tareas. El objetivo de este programa en el control de dos enchufes y la visualización del consumo eléctrico generado por estos enchufes.
```

```ad-info
title:Objetivos

- Cargar el Código otorgado
- Entender el Código otorgado
```

```ad-example
title: Carga del Código

 - !!!col
	- 1
		El código de NodeRed se guarda en un JSON. Para cargarlo le damos a la opción de **_Import_** o presionar **_ctrl + i_**
		
	- 1
		![[Pasted image 20221031153502.png]]

```


Vamos a explicar cada una de las secciones:

```ad-tip
title:Control Enchufes


![[Pasted image 20221031153716.png|center]]

En esta sección se establece parte del Dashboard

Primero, en la parte superior se establecen dos **Switches** (**enchufe 1 / enchufe 2**) que mandan una señal a los nodos **Cambiar Enchufe** que cambiara un **true** a un **false** o viceversa. Después se enviará un http Request al Aparato (en este caso el enchufe) y después se activaría o se  desactivaría  una luz para indicar si esta apagado o encendido.


En  la sección inferior se manda una señal de manera periódica al enchufe para comprobar si esta encendido o apagado y enviar su estado a los **Switch** del Dashboard para clonar su estado.

```

```ad-tip
title:Programación Horaria

![[Pasted image 20221031154552.png|center]]

En esta sección se realiza una desactivación periódica de los enchufes.

Los **TimeStamp** se ejecutan de manera periódica, pero esta vez solo se activan a las 0:00 de cada día.

Cada vez que se activa el **TimeStamp** se manda una **http Request** para desactivar los enchufes.
```

```ad-tip
title: Programación Condicional

![[Pasted image 20221031160028.png]]

Esta sección también controla el encendido o apagado de los enchufes con otra condición.

Se tiene una http Request que llama a la **API de OpenWeather** para recopilar diversos datos. El que nos interesa son los datos de las nubes, ya que el nodo **Function 7** enciende uno de los enchufes si el valor de las nubes es mayor a 50 y lo apaga si es mayor a 50.
```

```ad-tip
title: Medición de Consumo

![[Pasted image 20221031161045.png|center]]

En esta sección se muestra la otra sección del **DashBoard** orientada al consumo.

Se manda una petición **http Request** a uno de los enchufes y se pregunta por su consumo actual, este **se envía a una manecilla** para mostrarlo. Al mismo tiempo, se envía a un programa que **calcula el consumo en Kilovatitos/hora** y lo muestra en texto.
```

```ad-success
title: Resultado

![[Pasted image 20221031161739.png]]
```



## Mensajería con MQTT

```ad-question
title: Enunciado

Se va a usar el sistema asincrono de mensajeria MQTT para mandar mensajes a un Topic.
```

```ad-info
title: Objetivos

- Usar el protocolo MQTT
- Capturar datos por MQTT de un sensor
```

Para capturar datos de MQTT se usarán los nodos de MQTT:

```ad-tip
title: Mensaje por MQTT

- !!!col
	- 1
		Este programa es un sencillo, la parte superior envía una señal con el mensaje '**Hola**' por MQTT al Topic '**Saludos**'
		La parte inferior lee el Topic '**Saludos**' y muestra el mensaje por debug.
	- 1
		![[Pasted image 20221031164344.png|center]]

```


Después se intento la realización de un programa de lectura de un sensor mediante NodeRed y MQTT, no obstante no se pudo conseguir del todo.

Al final nos conectamos a un compañero de clase haciendo su ordenador de broker, colocando en el mqtt la ip de su ordenador:

```ad-tip
title: Leer sensor por MQTT 

- !!!col
	- 1
		![[Pasted image 20221031171459.png]]
	- 1
		![[Pasted image 20221031171525.png]]
```

## Monitorización mediante Ubidots

```ad-question
title:Enunciado

Se busca utilizar la aplicación web Ubidots para realizar un DashBoard en la cual nos muestre los datos proporcionados por un programa externo.
```

```ad-info
title:Objetivos

- Utilizar la web Ubidots
- Realizar un programa generador de datos.
- Vincular el Progranma con Ubidots
```

Primero nos **registraremos en la web de Ubidots**.

Una vez Registrados necesitamos conectar algún dispositivo para que Ubidots obtenga datos.

Para ello realizaremos un programa externo en Python que envíe los datos a Ubidots.

El programa que realizaremos será el programa de prueba que nos proporciona Ubidots.

Primero deberemos instalar el paquete request:

`pip install requests`

El programa sería el siguiente:
``` python
import time
import requests
import math
import random

TOKEN = "BBFF-o63O7Ap7f07GwsPU6Jm4JWDUIF2vPI"  # Put your TOKEN here
DEVICE_LABEL = "machine"  # Put your device label here
VARIABLE_LABEL_1 = "temperature"  # Put your first variable label here
VARIABLE_LABEL_2 = "humidity"  # Put your second variable label here
VARIABLE_LABEL_3 = "position"  # Put your second variable label here

def build_payload(variable_1, variable_2, variable_3):
    # Creates two random values for sending data
    value_1 = random.randint(-10, 50)
    value_2 = random.randint(0, 85)

    # Creates a random gps coordinates
    lat = random.randrange(34, 36, 1) + \
        random.randrange(1, 1000, 1) / 1000.0
    lng = random.randrange(-83, -87, -1) + \
        random.randrange(1, 1000, 1) / 1000.0
    payload = {variable_1: value_1,
               variable_2: value_2,
               variable_3: {"value": 1, "context": {"lat": lat, "lng": lng}}}
    return payload
  
def post_request(payload):
    # Creates the headers for the HTTP requests
    url = "http://industrial.api.ubidots.com"
    url = "{}/api/v1.6/devices/{}".format(url, DEVICE_LABEL)
    headers = {"X-Auth-Token": TOKEN, "Content-Type": "application/json"}

    # Makes the HTTP requests
    status = 400
    attempts = 0

    while status >= 400 and attempts <= 5:
        req = requests.post(url=url, headers=headers, json=payload)
        status = req.status_code
        attempts += 1
        time.sleep(1)
  
    # Processes results
    print(req.status_code, req.json())
    if status >= 400:
        print("[ERROR] Could not send data after 5 attempts, please check \
            your token credentials and internet connection")
        return False
        
    print("[INFO] request made properly, your device is updated")
    return True

def main():
    payload = build_payload(
        VARIABLE_LABEL_1, VARIABLE_LABEL_2, VARIABLE_LABEL_3)
    print("[INFO] Attemping to send data")
    post_request(payload)
    print("[INFO] finished")

if __name__ == '__main__':
    while (True):
        main()
        time.sleep(1)
```

Este Código nos envía una Latitud y Longitud aleatoria para ubicarnos en el mapa, después nos genera una temperatura y una humedad aleatoria que mandará cada pocos segundos.

Esto es lo que nos aparecería en Ubidots:

![[Pasted image 20221031113043.png]]


Si aumentamos alguna de las variables podremos ver un gráfico de la variación de la variable.

![[Pasted image 20221031113722.png]]

### Ampliación

Para la ampliación se va a sustituir los datos aleatorios por datos obtenidos desde la API de OpenWeather.

Para ello solo necesitamos modificar el código de Python añadiendo una **_http request_** a la API:

``` python
import time
import requests
import math
import random

TOKEN = "BBFF-o63O7Ap7f07GwsPU6Jm4JWDUIF2vPI"  # Put your TOKEN here
DEVICE_LABEL = "machine"  # Put your device label here
VARIABLE_LABEL_1 = "temperature"  # Put your first variable label here
VARIABLE_LABEL_2 = "humidity"  # Put your second variable label here
VARIABLE_LABEL_3 = "position" # Put your second variable label here

API_TOKEN = '13ebf6cfb3ffd4e34d7d55fb04ae21ab'
API_URI = 'api.openweathermap.org/data/2.5/weather'

def kelvin_to_celsius(kelvin: float):
    return kelvin - 273.15

def get_city_temperature(city: str):
    try:
        response = requests.get('https://'+API_URI+'?q='+city+'&appid='+API_TOKEN)
        data = response.json()
    except Exception as e:
        return 0
    return data

def openweatherService():
    celsius = get_city_temperature('Alicante')

def build_payload(variable_1, variable_2, variable_3):
    data = get_city_temperature("Alicante")
    value_1 = kelvin_to_celsius(data['main']['temp'])
    value_2 = data['main']['humidity']

    lat = 38.3451700
    lng = -0.4814900
    payload = {variable_1: value_1,
               variable_2: value_2,
               variable_3: {"value": 1, "context": {"lat": lat, "lng": lng}}}

    return payload

def post_request(payload):
    # Creates the headers for the HTTP requests
    url = "http://industrial.api.ubidots.com"
    url = "{}/api/v1.6/devices/{}".format(url, DEVICE_LABEL)
    headers = {"X-Auth-Token": TOKEN, "Content-Type": "application/json"}

    # Makes the HTTP requests
    status = 400
    attempts = 0
    while status >= 400 and attempts <= 5:
        req = requests.post(url=url, headers=headers, json=payload)
        status = req.status_code
        attempts += 1
        time.sleep(20)

    # Processes results
    print(req.status_code, req.json())
    if status >= 400:
        print("[ERROR] Could not send data after 5 attempts, please check \
            your token credentials and internet connection")
        return False

    print("[INFO] request made properly, your device is updated")
    return True
    
def main():
    payload = build_payload(
        VARIABLE_LABEL_1, VARIABLE_LABEL_2,VARIABLE_LABEL_3)

    print("[INFO] Attemping to send data")
    post_request(payload)
    print("[INFO] finished")

if __name__ == '__main__':
    while (True):
        main()
        time.sleep(1)
```

```ad-warning
title: Atención

Se ha colocado una espera de 20 segundos entre llamada y llamada para no saturar la API.
```

Haciendo esta modificación al código nos permitirá enviar los datos de temperatura y humedad de Alicante a Ubidots:
![[Pasted image 20221031151211.png|center]]

Los **valores de Humedad** han sido los siguientes:

![[Pasted image 20221031151458.png|center]]

