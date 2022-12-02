<center style="font-weight: bold; font-size: 25 ">Comunicación y Sincronización de Tareas</center>

[[Universidad/Ingeniería Informática/Sistemas en Tiempo Real/Prácticas/Práctica_4|Ir a la práctica(Sesión 5)]]
[[Universidad/Ingeniería Informática/Sistemas en Tiempo Real/Prácticas/Práctica_5|Ir a la práctica(Sesión 6)]]

```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
varied_style: true
```

## Comunicación y Sincronización

```ad-info
title: Conceptos

- Comunicación
	- Transferencia de información de un proceso a otro
- Sincronización
	- Cumplir las restricciones de orden en el que se ejecutan las acciones de distintos procesos
```

La comunicación se puede conseguir mediante:

- **Memoria Compartida** : Datos a los que pueden acceder más de un proceso.
- **Paso de Mensajes** : Intercambio explícito de datos entre dos procesos.


## Memoria Compartida

```ad-warning
title: Problemas

- El uso sin restricciones de variables compartidas presenta problemas de actualización.
- Dos procesos que actualizan una variable compartida X mediante la instrucción: X := X+1.
- Uso de instrucciones no atómicas. 
```

Hay varios mecanismos para sincronizar, en ADA usaremos los **Objetos Protegidos**.

## Objetos Protegidos en Ada

```ad-important
title: Funcionalidades

- Controlan el **acceso a datos compartidos** entre múltiples procesos
- Garantizan **exclusión mutua**
- Proporcionan **sincronización condicional** mediante expresiones booleanas (barrera en un entry
```

```ad-note
title: Declaración

Se pueden declarar
- Tipos de objeto protegido
- Instancias únicas

Hay que **declarar la _especificación_ y el _Body_** en ambos casos.
```

Estos son **unidades de programa**, no es una unidad de compilación [^1].

```ad-check
title: Metodos de Acceso

- Función Protegida
- Procedimiento Protegido
- Entrada (entry) Protegido
```

Las **_funciones protegidas_** proporcionan **acceso concurrente de lectura**
- Una o más tareas están ejecutando funciones protegidas
- Las tareas que requieran “escribir” (modificar los datos) deben esperar

Los **_procedimientos_ y _entrys protegidos_** proporcionan **acceso mutuamente exclusivo de lectura+escritura**
- Una tarea está ejecutando un procedimiento o un entry protegido
- Las tareas que requieran “escribir” y/o “leer” (consultar datos) deben esperar

### Sincronización Condicional

Un **_entry_ protegido** presenta una interfaz semejante a la de un procedimiento.

Tienen una **barrera condicional** (expresión booleana)
- La barrera no puede hacer referencia a los parámetros del _entry_
- Si la **barrera es falsa** la **tarea se suspende** en una cola de espera
	- Encolarse en un _entry_ requiere bloqueo de lectura/escritura
- Cuando la **barrera es cierta** la **tarea puede ejecutar** el cuerpo del _entry_

### Evaluación de las barreras

- Cuando una tarea llama a un _entry_.
- Cuando una tarea termina la ejecución de un procedimiento o de un _entry_.
```ad-question
title: ¿Por qué no se reevalúan cuando termina una función protegida?

Debido a que los parámetros no pueden modificarse dentro de las funciones protegidas.

Por el contrario, en los _entrys_ esto sí es posible.
```

### Acceso de Escritura

En ADA hay dos tipos de Colas:

- Tareas encoladas en barreras
- Tareas encoladas esperando acceso al objeto protegido

```ad-info
title: Información

Cuando una tarea termina la ejecución de un procedimiento o de un entry
```

En los _entry_ tenemos el atributo '_count_' que  nos indica el número de tareas en cola.

### Excepciones y objetos protegidos

- Cualquier **excepción lanzada y no manejada** mientras se **ejecuta una acción protegida**, se **propaga a la tarea invocante**.
- Una **excepción lanzada durante la evaluación de una barrera**, se convierte en un **Program_Error lanzado a todas las tareas encoladas** en el _entry_.

### Llamada condicional a un _entry_

- _Select_ con Alternativa _Else_
	- Se llama a un _entry_, si la barrera es falsa salta a la sentencia _else_. No obstante, si el objeto protegido esta ocupado sí esperará a que se libere.
- _Select_ or _delay_
	- Si la barrera es falsa se esperará el tiempo asignado por el delay. Si aun asi no puede entrar se ejecutara las sentencias debajo del _delay_.
- _Select_ then _abort_
	- Si la barrera es falsa se ejecutará la sección _then abort_ mientras se espera. Si termina la ejecución del _abort_ y no se entra se omitiría, si entra antes del _abort_ este para su ejecución.
	

## Paso de Mensajes

Los **procesos se pueden comunicar y sincronizar mediante mensajes**.

### Modelo de Sincronización

- **Sincronización implícita**
	- El proceso receptor no puede obtener un mensaje antes de que dicho mensaje haya sido enviado
- **Sincronización en función de la semántica de la operación enviar** (_send_) : 
	- Asíncrona
		- El emisor continúa su ejecución independientemente de si se ha recibido o no el mensaje
		- Se requiere un búfer para almacenar los mensajes
			- Capacidad potencialmente ilimitada
			- Si es limitado, puede bloquearse el emisor
	- Síncrona
		- El emisor continúa sólo cuando se ha recibido el mensaje
		- No es necesario un búfer
		- En ADA usamos la **Invocación remota**
			- El emisor continúa sólo cuando se ha devuelto una respuesta desde el receptor
			- Se conoce como cita extendida (**_extended rendezvous_**)

### Identificación de procesos

- **Identificación directa o indirecta**
	- **Directa**
		- El emisor identifica explícitamente el receptor
		- Tiene la ventaja de su simplicidad
	- **Indirecta**
		- Se utiliza una entidad intermediaria (Canal, buzón de correo, tubería, etc)
		- Facilita la descomposición del software
- **Identificación simétrica o asimétrica**
	- **Simétrica**
		- El emisor y el receptor se pueden identificar entre sí.
	- **Asimétrica**
		- El receptor no identifica un origen específico, sino que acepta mensajes de cualquier emisor (proceso o buzón)

## Cita Extendida en ADA

```ad-info
title: Caracteristicas de la Cita Extendida

- Modelo de sincronización : Cita extendida (extended rendezvous)  
	- La tarea que llega primero espera a la otra  
	- Las tareas esperan a la respuesta  
- Intercambio de información mediante parámetros  
- Se proporciona exclusión mutua  
	- Una tarea no puede mantener varias citas a la vez. Después de responder a una cita, se puede establecer la siguiente.  
- La cita está basada en el modelo cliente/servidor  
	- El servidor declara un conjunto de servicios que se ofrecen a otras tareas (sus clientes)
```

### Entry

Para que una tarea pueda recibir un mensaje, debe tener definido un _entry_ (entrada).

Cada _entry_ indica el nombre del servicio, los parámetros necesarios para la petición y los resultados que devolverá.

La tarea que realiza la llamada "conoce" a la tarea a la que "llama"

```ad-tip
title: Información

El _entry_ también contiene un atributo _Count_.
```

```ad-info
title:Espera selectiva en el cliente

**La sentencia Select...llamada_entry permite al cliente:**  
- No esperar si el servidor no está disponible inmediatamente para establecer la cita (**else**)  
- Dejar de esperar si el servidor no ha llegado a la cita en un tiempo especificado (**or delay**)  
- Abortar la ejecución de acciones que se han estado realizando durante la espera (**then abort**)
```

### Accept

Para que una tarea pueda servir un mensaje, debe tener definido un _accept_  

La sentencia _accept_ permite al servidor atender una petición de servicio (llamada a un _entry_)  

La tarea que sirve la petición no "conoce" a la tarea que manda el mensaje

```ad-warning
title: Restricciones del _Accept_

- Puede colocarse únicamente en el cuerpo de una tarea  
- Se pueden anidar, aunque no para el mismo entry  
- Debería haber al menos un accept por cada entry
```

```ad-bug
title: Manejo de Excepciones en un Accept

Si no se maneja dentro del accept la excepción se lanza en la tarea invocadora y en la receptora de tipo **TASKING_ERROR**

La excepción **TASKING_ERROR** se puede generar cuando:  
• Se realiza una llamada a un entry de una tarea que no está activa  
• Se está encolado en un entry de una tarea que termina
```

```ad-info
title: Espera Selectiva en Servidor

La sentencia **_Select...accept_** permite:

- Esperar a más de una cita a la vez (**or accept**)  
- Retirar la oferta de comunicación si ninguna cita está disponible inmediatamente (**else**)  
- Detener la espera si ninguna cita ha llegado en un tiempo especificado (**or delay**)  
- Terminar si no hay clientes que puedan llamar a sus entradas (**or terminate**) se suele hacer cuando la tarea es innecesaria

```

[^1]: Tiene que estar dentro de una unidad de compilación, es decir, necesita estar en un _pakage_.