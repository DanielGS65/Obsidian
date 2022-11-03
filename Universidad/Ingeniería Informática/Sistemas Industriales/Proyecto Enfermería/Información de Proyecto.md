<center style="font-weight: bold; font-size: 25 ">Proyecto Enfermería</center>

```toc
title: "## Índice"
style: number 
min_depth: 2
max_depth: 3
varied_style: true
```

## Descripción de Proyecto

En esta sección se realizará una breve descripción del proyecto a realizar.

En la actualidad, la informática esta más presente que nunca en nuestra sociedad, cada vez más sistemas y profesiones están intentando informatizarse en mayor o menor medida para automatizar tareas repetitivas o facilitar cálculos complejos.

En el campo de la enfermería aun quedan cosas que informatizar, en particular, el sistema de horarios de los hospitales.

De manera regular, se tiene que ajustar de manera manual el horario de los trabajadores, por esta razón, se cree que es de gran importancia informatizar este sistema.

```ad-info
title: Objetivos

- Realizar un **Sistema** que sea capaz de **Mostrar los Horarios** de los trabajadores.
- **Automatizar** la **Asignación de Personal** en el horario.
- **Contabilizar** trabajo realizado **fuera del horario regular**.
- **Facilitar** la parte de gestión del personal.
```

## Especificación Funcional

En esta sección se explicará las funcionalidades que se asignarán al sistema.

El sistema se va a separar en grupos funcionales los cuales contendrán las funcionalidades de estos grupos.

![[Pasted image 20221018235641.png]]
### Horario

En este grupo funcional se describirán las funcionalidades relacionadas con la visión del horario.

##### - Asignación Automática de Personal

La parte mas importante del proyecto es que tenga la capacidad de asignar al personal mediante una serie de restricciones presentadas al sistema siendo capaz de crear un horario viable cumpliendo todas las restricciones.
También se le dará la opción al gestor de poder modificar el horario.

```ad-warning
title: Restricciones

Las resticiones que el programa deberá tener en cuenta son las siguientes:

- Cada empleado tiene tres tipos de turnos:
	- **Turno de Mañana** -> Duración: 7 Horas 
	- **Turno de Tarde** -> Duración: 7 Horas
	- **Turno de Noche** -> Duración 10 Horas
- Cada empleado se le debe **asignar un turno de manera rotatoria** (Dia->Tarde->Noche)
- **No se puede superar las 36 Horas Semanales/160 Horas Mensuales/1151 Horas Anuales**
 ```
 
##### - Muestreo del Horario

Se necesita mostrar el horario de manera clara, indicando el turno al que se debe asistir, la planta a la que se le asigna y una lista de los pacientes a tratar.

También se deberá dar la opción al usuario de el formato de visualización del horario, los formatos requeridos serán Día / Semana / Mes / Año.

### Perfil de Empleado

En este grupo funcional se centrara en las funcionalidades que forman el sistema de visualización de perfil del empleado.

##### - Estadísticas Laborales

Se representará, de forma gráfica, un **recuento de las horas trabajadas** (Indicando el periodo de tiempo), **la cantidad de turnos asignados**, un **recuento de las horas realizadas fuera del horario convencional** (turnos de noche y días festivos) y el recuento de **Días Laborales** y **Días Festivos.**

##### -  Asignación de Días Libres

Se mostrarán al empleado los días los cuales han sido asignados como días libres. 

##### - Creación de Peticiones

Se dará la opción, al empleado, de **solicitar cambios en el horario** vía mensajería, estos **mensajes serán recibidos por la parte de gestión** del sistema y serán evaluados, **indicando** si las **peticiones han sido aceptadas o denegadas**. 

### Gestión

En este grupo funcional se encuentran las funcionalidades enfocadas solo al apartado de gestión del sistema.

##### - Lista de Personal

Se tendrá acceso a una **lista de todos los empleados** almacenados en el sistema dando la opción de **visualizar las horas y turnos asignados** para saber si el empleado puede ser recolocado, además, se dará la opción de entrar en el perfil de cada empleado.

##### - Gestión del Horario

Se **obtendrá el control sobre el horario** para poder **realizar modificaciones asignando los empleados** a colocar en cada zona del horario.

###### - Buzón de Mensajería

Se tendrá **acceso a un buzón de mensajes** de los empleados con las peticiones de estos. Se dará la opción de **contestar desde el buzón**.

## Especificación Estructural 

En este apartado se explicara la estructura del sistema de manera detallada

Dadas las funcionalidades anteriores se especificará la arquitectura del sistema que mejor se adapte, intentando dar una interfaz sencilla y intuitiva al usuario.

### Arquitectura

En este apartado se procederá a describir la arquitectura que se utilizará.

```ad-warning
title:Requerimientos

Para el sistema se ha especificado **que debe ser accesible desde cualquier lugar** que tenga una conexión a internet estable.
```

Debido a los requerimientos se ha decidido realizar una arquitectura web conjunta con una aplicación móvil, ya que será accesible desde la gran mayoría de dispositivos.

##### - Base de Datos

Dadas las especificaciones funcionales se ha diseñado la siguiente base de datos.

![[BBDD.png]]



```ad-note
title:Ayuda a la lectura de la base de datos

El sistema tiene que gestionar **Hospitales**, de cada hospital guardaremos su nombre. Cada **Hospital contiene de una a muchas plantas**, de cada planta guardaremos su número.

Los **Pacientes** del hospital **serán asignados a cada planta**,un **paciente puede estar asignados a varias plantas** y **una planta puede tener varios pacientes**, de los pacientes guardaremos su nombre, su email, su DNI y su número de teléfono.

De los Empleados guardaremos si nombre, su email y su teléfono. Los **empleados pueden ser Enfermeros o Auxiliares**. Los **empleados pueden ser asignados a una planta**, un **empleado puede ser asignado a muchas plantas y solo puede ser asignado a un turno por día**, de la asignación se guardará la fecha y si es festivo el día. De los turnos se guardará las horas de cada turno.
```

