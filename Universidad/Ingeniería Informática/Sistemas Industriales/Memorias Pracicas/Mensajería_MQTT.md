<center style="font-weight: bold; font-size: 25 ">Mensajería con MQTT</center>


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

- !!!col
	- 1
		Este programa es un sencillo, la parte superior envía una señal con el mensaje '**Hola**' por MQTT al Topic '**Saludos**'
		La parte inferior lee el Topic '**Saludos**' y muestra el mensaje por debug.
	- 1
		![[Pasted image 20221031164344.png|center]]

Después se intento la realización de un programa de lectura de un sensor mediante NodeRed y MQTT, no obstante no se pudo conseguir del todo.

Al final nos conectamos a un compañero de clase haciendo su ordenador de broker, colocando en el mqtt la ip de su ordenador:

- !!!col
	- 1
		![[Pasted image 20221031171459.png]]
	- 1
		![[Pasted image 20221031171525.png]]