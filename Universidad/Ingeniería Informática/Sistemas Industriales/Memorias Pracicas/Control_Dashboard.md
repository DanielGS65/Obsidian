<center style="font-weight: bold; font-size: 25 ">Monitorización y Dashboards</center>


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