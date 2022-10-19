<center style="font-weight: bold; font-size: 25 ">Comunicación y Sincronización de Tareas</center>

[[Práctica_4| Ir a la práctica(Sesión 5)]]

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
	

[^1]: Tiene que estar dentro de una unidad de compilación, es decir, necesita estar en un _pakage_.