<center style="font-weight: bold; font-size: 25 ">PLANIFICACIÓN DE TAREAS APERIÓDICAS</center>

```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
varied_style: true
```


## Restricciones temporales de las tareas

- Un STR puede requerir tanto tareas periódicas como aperiódicas
- Las tareas periódicas generalmente tienen restricciones de tiempo hard
- Las tareas aperiódicas son dirigidas por eventos (event-driven) y pueden tener restricciones de tiempo:
	- Hard  
	- Firm  
	- Soft  
	- No tiempo real

**Hay que garantizar:**

- Cumplir los plazos de todas las tareas aperiódicas hard en las condiciones del peor caso
- Maximizar el ratio de tareas aperiódicas firm.
- Optimizar tiempos de respuesta medios para tareas aperiódicas soft o sin restricciones de tiempo real

## Scheduling de tareas aperiódicas


```ad-tip
title: Tareas aperiódicas hard

**Se asumen como tareas esporádicas:**

- Suponemos que en el peor de los casos la tarea es pseudoperiódica (con periodo T)
	- El parámetro T representa la separación mínima entre dos activaciones consecutivas.
- El plazo de respuesta suele ser $D < T$
- Los análisis de planificabilidad para FPS siguen siendo válidos
```

```ad-tip
title: Tareas áperiodicas firm

**Cuando no se puede garantizar a priori la tasa de llegada máxima**  
- Se requiere un test de aceptación  
	- Verificar si la tarea puede ejecutarse dentro de su plazo, sin incumplir el deadline del resto de tareas  
- Si no pasa el test, la tarea es rechazada
```

```ad-tip
title: Tareas aperiódicas soft  

**Optimizar el tiempo de respuesta medio**  
- Planificadores:  
	- Background scheduling  
	- Servidores de prioridad fija  
		- Servidor por consulta (Polling Server)  
		- Servidor diferido (Deferrable Server)  
		- Servidor esporádico (Sporadic Server)  
	- Algoritmo de extracción de holgura (Slack Stealing)
```

## Background scheduling

Las tareas aperiódicas ==se ejecutan como **procesos con la prioridad más baja**.==

Es un método muy sencillo, pero los **tiempos de respuesta para las peticiones aperiódicas pueden ser muy altos**

## Servidores aperiódicos

```ad-tip
title: Servidor por consulta (Polling Server)

Para mejorar los tiempos de respuesta medios de tareas aperiódicas, se usa un servidor  
- Servidor = tarea periódica que atiende peticiones aperiódicas  
	- $(Ts , Cs )$  
	- $Cs$ = capacidad del servidor  
- El servidor es planificado con el mismo algoritmo usado para las tareas periódicas  
- Una vez activo, sirve las peticiones aperiódicas dentro del límite de su capacidad
- En cada activación del servidor :  
	- Se restablece la capacidad del servidor  
	 - Cuando es seleccionado por el planificador comprueba si hay peticiones aperiódicas pendientes  
		- Si las hay, las puede ejecutar si le queda capacidad  
		- Si no las hay, se suspende (pierde su capacidad) hasta su próxima activación


```ad-example
title: Analisis de planificabilidad

- La planificabilidad de las tareas periódicas puede garantizarse evaluando la interferencia que provoca el servidor
	- En el peor de los casos dicha interferencia es igual que la que produciría una tarea periódica con periodo Ts y tiempo de computación Cs
- Se utiliza el ==test de factores de utilización==
```

```ad-tip
title: Servidor diferido (Deferrable Server)
- En cada activación del servidor :  
	- Se restablece la capacidad del servidor  
	- Cuando es seleccionado por el planificador comprueba si hay peticiones aperiódicas pendientes  
		- Si las hay, las puede ejecutar si le queda capacidad  
		- Si no las hay, conserva su capacidad hasta su próxima activación (a menos que la necesite antes)
```

```ad-tip
title: Servidor Esporadico

La capacidad del servidor :  
- Se conserva cuando no hay peticiones aperiódicas pendientes  
- Se repone siguiendo la siguiente regla:  
	- Instante de reposición (Replenishment time) RT:  
	- $t_a$ = instante en el que el servidor pasa a estar activo  
- Servidor activo = cuando Priorida de ejecución $>= Ps$ y le queda capacidad  
- $T_s$ = Periodo del servidor  
- Cantidad a reponer (Replenishment amount) RA:  
	- $t_i$ = instante en el que el servidor pasa a estar inactivo (idle)  
- Servidor inactivo = cuando Prioridadejecución < Ps o no le queda capacidad
```

## Algoritmo de extracción de holgura

Las tareas periódicas pueden tener un tiempo de holgura (slack time)  
- Se puede retrasar la ejecución de una tarea periódica hasta el instante que siga permitiendo cumplir con su deadline  
- El tiempo de holgura es empleado para ejecutar tareas aperiódicas  
- No se trata de un servidor periódico:  
	- Es una tarea que se activa cuando llega una petición aperiódica  
	- “Roba” tiempo de las tareas periódicas (sin causar que pierdan su plazo) para dedicárselo a las peticiones aperiódicas

