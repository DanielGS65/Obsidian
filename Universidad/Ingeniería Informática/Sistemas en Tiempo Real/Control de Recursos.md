<center style="font-weight: bold; font-size: 25 ">Control de Recursos</center>

```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
varied_style: true
```

## Sincronización en el control de recursos

Se requiere sincronización entre procesos que **comparten acceso a recursos limitados**.

La asignación de recursos entre procesos competitivos _**puede afectar a la fiabilidad**_ del sistema:
- Fallo de un proceso con un recurso asignado  
- Monopolizar un recurso  
- Interbloqueo

El control de recursos se puede llevar a cabo mediante
- Recursos Protegidos
- Un Servidor


## La funcionalidad de reencolado en Ada

```ad-info
title: Definición

El reencolado consiste en **mover una tarea X** que estaba e**n la cola de un entry A a la cola de otro entry B**, ==cuando se ejecuta el entry A==.
```

```ad-warning
title: El reencolado no es una simple llamada

- Cuando el cuerpo del _entry_ A ejecuta un _requeue_, el _entry_ actual se completa
- La _tarea_ X continua suspendida hasta que el cuerpo del _entry_ B se ejecute
```

### Paso de parámetros en el _requeue_

No se proporcionan parámetros actuales en la llamada al _entry_ en la sentencia _requeue_.

El entry nombrado en la sentencia requeue:
- O ==**no tiene parámetros**==.
- O si los tiene, debe ser el ==**mismo número y tipo de parámetros**== que el _entry_ desde el que se hace el reencolado

### Uso de la sentencia _requeue_

Se permiten reencolados entre _entries_ de tareas y de objetos protegidos

El **reencolado puede ser para**:
- El **mismo _entry_**.
- Otro **_entry_ en la misma unidad**.
- Un **_entry_ de otra unidad**.

### Llamadas temporizadas

Se puede añadir una clausula a los requeue para que estos respeten el temporizador que tenía asignada la tarea solicitante.

Esta sentencia es el _**requeue then abort**__

Si la tarea solicitante tiene una sentencia la cual tenga un temporizador [^1] , si el _entry_ que lo atiende tiene un requeue puede decidir si sigue respetando el temporizador o no.

- _**requeue then abort**_ respeta el temporizador.
- _**requeue**_ no respeta el temporizador.

```ad-check
title: Realizar Pregunta requeue with abort
```

