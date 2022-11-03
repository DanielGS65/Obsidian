<center style="font-weight: bold; font-size: 25 ">Monitorización mediante Ubidots</center>

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

