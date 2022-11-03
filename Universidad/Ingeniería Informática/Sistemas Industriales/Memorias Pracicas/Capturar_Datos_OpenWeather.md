<center style="font-weight: bold; font-size: 25 ">Capturar Datos de OpenWeather</center>


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
