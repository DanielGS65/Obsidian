<center style="font-weight: bold; font-size: 25 ">Planificación de Recursos</center>

```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
varied_style: true
```

## Interacción entre tareas

```ad-info
title: Dependencia entre tareas

Generalmente en un STR existe dependencia entre tareas  
- Tipos de dependencias:  
	- Restricciones de precedencia  
		- Sincronización y comunicación  
	- Restricciones de exclusión mútua para proteger recursos compartidos

```

```ad-example
title: Bloqueo entre tareas

- Los procesos interactúan mediante:  
	- Datos compartidos  
	- Paso de mensajes  
- En ambos casos puede ocurrir que una tarea tenga que esperar un suceso de otra menos prioritaria  
	- Esta situación se denomina bloqueo
```

## Inversión de prioridades

```ad-tip
title: El problema de la inversión de prioridad
- Un proceso está bloqueado cuando está esperando debido a otro de menor prioridad  
- A este hecho se le denomina inversión de prioridad  
- La inversión de prioridad no se puede eliminar completamente, pero es posible limitar su duración
```

Para limitar su duración se usarán los siguientes protocolos:

## Protocolo de herencia de prioridades

```ad-info
title: Herencia de prioridades

Una forma de reducir la duración de los bloqueos es variar dinámicamente la prioridad de las tareas  
- Si la tarea τ2 está bloqueando a la tarea τ1 , entonces τ2 se ejecutará temporalmente con la prioridad de τ1  
En cada tarea se distingue entre:  
- una prioridad fija por defecto P i , p.ej. asignada según RM 
-  y una prioridad activa p i asignada dinámicamente (pi > Pi)  
La herencia de prioridad es transitiva  
- Si la tarea τ3 bloquea a la tarea τ2 , y τ2 bloquea a la tarea τ1 , entonces τ3 hereda la prioridad de τ1 a través de τ2
```

```ad-tip
title: Duración máxima de bloqueo

El protocolo de herencia de prioridad limita el tiempo máximo de bloqueo de cada tarea  
- Una tarea puede bloquearse por recursos a los que no accede  
- Una tarea puede bloquearse aunque no acceda a ningún recurso  
- La tarea de menor prioridad no sufre bloqueo
```

```ad-warning
title: Inconvenientes
Se pueden producir:
- Bloqueos Encadenados
- Interbloqueos
```

## Protocolos de techo de prioridad

```ad-success
title:Ventajas

Previene la formación de:
- Bloqueos encadenados  
- Interbloqueo  
Un proceso de alta prioridad puede ser bloqueado como máximo en una sola ocasión en cada activación
```

```ad-info
title: OCPP (Original Ceiling Priority Protocol)
- Cada tarea tiene una prioridad estática  
	- Asignada por defecto  
- Cada recurso tiene un techo de prioridad asignado  
	- Máxima prioridad de las tareas que lo pueden usar  
- Cada tarea tiene una prioridad dinámica  
	- Máximo entre su propia prioridad estática y cualquiera que herede debido a que bloquea a tareas más prioritarias

```ad-tip
title: Funcionamiento
Una tarea puede bloquear un recurso solamente si su prioridad dinámica es mayor que el techo de cualquier recurso actualmente bloqueado por otras tareas
```


```ad-info
title: ICPP (Immediate Ceiling Priority Protocol)

- Cada tarea tiene una prioridad estática  
	- Asignada por defecto  
- Cada recurso tiene un techo de prioridad asignado  
	- Máxima prioridad de las tareas que lo pueden usar  
- Cada tarea tiene una prioridad dinámica  
	 - Máximo entre su prioridad estática y los valores “techo” de  cualquier recurso que en ese momento tenga bloqueado
```

