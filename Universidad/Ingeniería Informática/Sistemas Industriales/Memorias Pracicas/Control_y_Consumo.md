<center style="font-weight: bold; font-size: 25 ">Control de Aparatos y Visualización del Consumo</center>


```ad-question
title: Enunciado

Se nos entrega un programa de NodeRed compuesto por 4 secciones que realiza una serie de tareas. El objetivo de este programa en el control de dos enchufes y la visualización del consumo eléctrico generado por estos enchufes.
```

```ad-info
title:Objetivos

- Cargar el Código otorgado
- Entender el Código otorgado
```




- !!!col
	- 1
		El código de NodeRed se guarda en un JSON. Para cargarlo le damos a la opción de **_Import_** o presionar **_ctrl + i_**
		
	- 1
		![[Pasted image 20221031153502.png]]

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
