<center style="font-weight: bold; font-size: 25 ">Gestión del Tiempo en STR</center>


[Ir a la Práctica](Universidad/Ingeniería%20Informática/Sistemas%20en%20Tiempo%20Real/Prácticas/Práctica_3.md)

```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
varied_style: true
```

## Requerimientos temporales en STR

__Interacción con el Tiempo__
- Medir el paso del tiempo.
- Retrasar ejecución de tareas.
- Programación con esperas (_Timeouts_).
- Ejecutar acciones en determinados instantes.

__Representación de requerimientos temporales__
- Periodos de activación
- Plazos de ejecución (_deadlines_)

__Satisfacción de requerimientos temporales__
- _Scheduling_

### Facilidades de control de tiempo real

- Especificar los tiempos en las que las acciones tienen que ejecutarse
- Especificar los tiempos en las que las acciones tienen que completarse

## Acceso a Relojes

De manera regular se debería acceder al reloj del sistema.

### Acceso en ADA

- __ADA.Calendar__
	- Instante de tiempo absoluto -> type Time
	- Intervalo de tiempo -> type Duration
	- Clock -> Fecha y Hora

- __ADA.Real_Time__
	- Instante de tiempo absoluto -> type Time
	- Intervalo de tiempo -> type Time_Span
	- Clock -> Valor que representa el tiempo transcurrido desde el instante inicial al prefijado.

## Retardos

Los retardos sirven para suspender la ejecución de una tarea durante un tiempo determinado

__Tipos de retardos:__
- Relativo => Ejecución se suspende durante un intervalo de tiempo relativo al instante actual
- Absoluto => Ejecución se suspende hasta que llegue a un instante determinado de tiempo absoluto

### Retardos Relativos en ADA

La instrucción `delay(T<duration>)` suspende la ejecución de la tarea durante el intervalo de tiempo que indica `T`.

```ad-warning
title: Pregunta de examen
Como implementar correctamente una tarea periódica
```


Puede pasar un tiempo desde que el fin del delay hasta la ejecución de instrucciones, esto puede ser debido a: 
- La presencia de tareas de mayor prioridad.
- Por el Sistema operativo. 
- Por diferencias entre clock y el delay.

Estos retrasos no suelen ser valores muy altos, pero hay que tenerlos en cuenta. 

### Retardo Absoluto

La sentencia `delay until (T<duration>)` suspende la tarea hasta que se alcance el instante absoluto, también sufre de desviación local.

## Temporizadores

La función de los temporizadores es limitar el tiempo durante el cual una tarea esta lista para esperar una comunicación.

- Se utiliza para notificaciones asíncronas.
