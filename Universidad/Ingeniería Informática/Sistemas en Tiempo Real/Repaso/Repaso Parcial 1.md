#flashcards/Parcial_1

==Presiona Ctrl + num_0 para revisar las cartas==

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

**Cita** y **Describe** las **características principales de un Sistema en Tiempo Real**.
```
?
```ad-info
title: Respuesta

La característica mas importante en un STR es la **Concurrencia** ya que esta la concurrencia es inherente en los sistemas STR.

Otra característica muy importante es la **Dependencia en el tiempo**, ya que en un STR es necesario que las tareas se terminen antes de que se agote su plazo '*deadline*'.

Por último necesitan ser **Fiables** (Tener una alta probabilidad de proporcionar el servicio requerido) y **Seguros** (Tener una alta probabilidad de recuperarse en caso de error).
```
<!--SR:!2022-10-12,4,274-->

```ad-question
title: Pregunta

Nombra los **Tipos de Sistemas en Tiempo Real**.
```
?
```ad-info
title: Respuesta

La **clasificación** de los STR se hacen **sobre dos Factores**, por su **Criticidad** y por su **Instante de Activación**.

- Por su **Criticidad**:
	- **Hard Real Time**
		- Todas las acciones son críticas.
	- **Soft Real Time**:
		- Se puede perder plazos
		- La utilidad del tiempo decrece con el tiempo.
	- **Firm Real Time**:
		- Se puede perder plazos
		- Una respuesta tardía no tiene valor. 

- Según el **Instante de Activación de las Tareas**:
	- Los **instantes de activación** pueden ser dirigidos por:
		- Eventos (Inicio en instantes predeterminados)
		- Tiempo (Inicio cuando se produce un suceso de cambio de estado)
	- **Esquemas de activación**:
		- **Periódica** : Se ejecuta con un periodo (en ciclos)
		- **Aperiódica**: Se ejecuta de manera irregular, responde a estímulos.
		- **Esporádica**: Debe tener una separación entre activaciones.
```
<!--SR:!2022-10-11,3,254-->

```ad-question
title: Pregunta

Cita las **Arquitecturas Software** que se pueden dar en un **Sistema en Tiempo Real**.
```
?
```ad-info
title: Respuesta

**Ejecutivo Cíclico**: Un único programa de ejecución secuencial con un bucle de control.    
- Las lecturas se hacen con la misma frecuencia.
- Ineficaz ante restricciones temporales.
- Mientras se realiza una tarea, las anteriores no están activas.
- Si algún elemento falla, todo el programa fallará.

**Sistema Operativo en Tiempo Real**: Programas separados o procedimientos distintos en varios programas secuenciales.
- A veces no queda claro que son actividades concurrentes o procedimientos.

**Lenguaje de Programación en Tiempo Real**: Un solo programa que tenga concurrencia  
- Tipos:
    - **Ensamblador**: Eficiente pero Costoso.
    - **Secuencial**: Necesitan un S.O.
    - **Concurrente**: Incluido en el lenguaje.
```
<!--SR:!2022-10-12,3,256-->

```ad-question
title: Pregunta

Cita los **Requerimientos** de un **Sistema en Tiempo Real**.
```
?
```ad-info
title: Respuesta

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
```
<!--SR:!2022-10-12,4,275-->

```ad-question
title: Pregunta

Cita las **Diferencias** entre las librerias **Ada.Real_Time** y **Ada.Calendar**.
```
?
```ad-info
title: Respuesta

Ada.Real_Time **ofrece una mayor precisión** frente a Ada.Calendar, además, este ofrece los resultados en un **valor único** y no en una fecha.
```
<!--SR:!2022-10-12,4,276-->

```ad-question
title: Pregunta

Nombra los **Tipos de Retardos** en un **Sistema de Tiempo Real**.
```
?
```ad-info
title: Respuesta

**Relativo** => Ejecución se suspende durante un intervalo de tiempo relativo al instante actual
**Absoluto** => Ejecución se suspende hasta que llegue a un instante determinado de tiempo absoluto
```
<!--SR:!2022-10-12,4,276-->

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
<!--SR:!2022-10-12,4,274-->

```ad-question
title: Pregunta

Se nos pide hacer una tarea Anonima que realize la suma continua de un numero (num + 1) y que este cree otra tarea que imprima este número por pantalla.

Después de calcular cada numero la tarea esperará un segundo.

Escribe el codigo necesario paar realizar este sistema.
```
?
```ad-info
title: Respuesta

El código necesario sería el siguiente:
```
``` ADA
-- pkg_task.ads

with Ada.Text_IO; use Ada.Text_IO;
with Ada.Integer_Text_IO; USE Ada.Integer_Text_IO;  

package pkg_task is

	task sumNum;
	task type printNum(num:Integer);
	type ptr_printNum is access printNum;
	 
end pkg_task;

-- pkg_task.adb

package body pkg_task is
	
	task body sumNum is
	
		num:Integer := 0;
		tarea : ptr_printNum;

	begin
		loop
			num := num + 1;
			tarea := new printNum(num);
			delay(1.0);
		end loop;
	end sumNum;

	task body printNum is
	
	begin
	
		Put(num);
		Put_Line("");
	
	end printNum;

end pkg_task;

-- main.adb

with pkg_task;

procedure main is
begin
	null:
end main;
```
<!--SR:!2022-10-12,3,258-->





