#flashcards/Parcial_1

==Presiona Ctrl+0 para revisar las cartas==

%%Teoría%%
```ad-question
title:Pregunta

**Cita** las características que tiene el lenguaje de programación **ADA**. 
```
?
```ad-info
title:Respuesta

ADA tiene las siguientes características:
- Diseñado para sistemas de tiempo real:
	- Es un lenguaje con procesamiento concurrente.
	- Tiene un sistema de manejo del tiempo.
	- Capacidad de generar Interrupciones.
- Su sintaxis es descendente de Pascal:
	- Es un lenguaje fuertemente tipado.
	- Tiene estructuras en bloque.
- Además, al estar pensado para grandes sistemas y sistemas cambiantes:
	- Contiene Paquetes y unidades genéricas
	- Contiene interfaces normalizadas con otros lenguajes como C y Fortran. 
```
<!--SR:!2022-10-11,3,250-->

```ad-question
title:Pregunta

Indica las diferencias entre las **funciones** y los **procedimientos** en ADA
```
?
```ad-info
title:Respuesta

Un procedimiento puede tener parámetros de distintos tipos de formato (entrada '_in_', salida '_out_', por referencia '_in out_'), este puede devolver valores mediante el uso de un '_return_'.
Por el contrario las funciones, aunque si admiten parametros, no admiten que se les ponga formato, por lo que siempre estaran formateados como '_in_', además siempre deben tener una sentencia '_return_'.
```
<!--SR:!2022-10-12,4,270-->


```ad-question
title: Pregunta

¿Qué es una unidad genérica en ADA?
```
?
```ad-info
title:Respuesta

Una unidad genérica es una unidad la cual puede ser reutilizada en el código cuando **son usadas cambiando el tipo del dato de entrada**
```
<!--SR:!2022-10-12,4,270-->

```ad-question
title: Pregunta

Indica las diferencias entre las **Tareas Anónimas** y las **Tareas No Anónimas**, indicando también los **tipos distintos de estas tareas** (Si los tienen).
```
?
```ad-info
title: Respuesta

Las **Tareas Anónimas** son tareas las cuales **son ejecutadas de manera automática** si esta está incluida en el programa, este tipo de tareas **no se pueden declarar en el programa mediante variables**. Estas **se ejecutan después de la declaración del Main**, el main no se ejecutará hasta que todas las tareas anónimas hayan terminado su declaración.

Las **Tareas No Anónimas** son tareas que **necesitan ser llamadas en el código**, este tipo de tareas son **similares a las tareas anónimas**, pero estas **admiten discriminantes** (un discriminante es como un parámetro con restricciones, estos solo admiten tipos básicos y punteros).
Las tareas no Anónimas tienen dos tipos que cambian el momento de invocación.
- **Estáticas**: Se **declaran en la zona de declaración de un procedimiento**.
- **Dinámicas**: Se **declara un puntero de la tarea y se invoca dentro del código de un procedimiento**.
```
<!--SR:!2022-10-11,3,250-->

```ad-question
title: Pregunta

**Cita** y **Describe** las **caracteristicas principales de un Sistema en Tiempo Real**.
```
?
```ad-info
title: Respuesta

La caracteristica mas importante en un STR es la **Concurrecia**
```

%%Práctica%%
```ad-question
title: Pregunta

Dado el Siguiente Código y sabiendo que se pide que cada tarea se ejecute cada 5 segundos, ¿el código mostrado sería correcto?

```
```ADA
task body tarea is
begin
	delay(5.0);
	function;
end tarea;
```
?
```ad-info
title: Respuesta

No, no sería correcto debido a que la instrucción delay esperará 5 segundos además de tener que esperar la ejecución de la función a la que llama. Debería usarse la instrucción _delay until_.
```




