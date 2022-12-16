<center style="font-weight: bold; font-size: 25 ">Práctica 9</center>

```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
varied_style: true
```

## Ejercicio 1

```ad-statement

Dado un sistema formado por 5 tareas (τ1, τ 2, τ 3, τ 4, τ 5) y 3 recursos compartidos (R, Q y V) con las siguientes características:

![[Inkodo-12152022_40419_PM.png|center|200]]

La siguiente tabla muestra el convenio que se puede utilizar para mostrar en el cronograma los estados en los que puede estar una tarea en cada instante:

- <p style="color: rgb(0,100,255)">E En ejecución </p>
- <p style="color: rgb(255,180,0)">Q En ejecución bloqueando el recurso Q</p>  
- <p style="color: rgb(255,0,255)">R En ejecución bloqueando el recurso R</p>
- <p style="color: rgb(0,255,100)">V En ejecución bloqueando el recurso V</p>
- <p style="color: rgb(255,255,255)">En espera (la casilla se deja en blanco)</p> 
- <p style="color: rgb(180,0,0)"> X Bloqueado</p> 

Asumiendo una única activación de cada una de las tareas:
```

```ad-section
title:Apartado a)

Dibujar el correspondiente cronograma utilizando cada uno de los siguientes protocolos:  
herencia de prioridades, techo de prioridad original (OCPP) y techo de prioridad inmediato (ICPP)
```

- **Herencia de Prioridades**
```ad-paragraph

![[Inkodo-12152022_42637_PM.png]]

```

- **Techo de Prioridad Original (OCPP)**

```ad-paragraph

![[Inkodo-12152022_43314_PM.png]]

```

- **Techo de Prioridad Inmediato (ICPP)**

```ad-paragraph

![[Inkodo-12152022_44426_PM.png]]

```

```ad-section
title: Apartado b)

Observando los cronogramas realizados en el apartado anterior, indica qué tareas sufren  
bloqueos encadenados
```

En el bloqueo por herencia sufren la Tarea 1 y la Tarea 2 de bloqueos encadenados.

- En la Tarea 1 se bloquea porque la Tarea 5 tiene V y después porque la Tarea 2 tiene Q.
- En la Tarea 2 se bloquea porque la Tarea 5 tiene V y tiene prioridad y después porque la Tarea 3 tiene R.

```ad-section
title: Apartado c)

Observando los cronogramas anteriores, indica la diferencia que existe entre OCPP e  
ICPP en cuanto a en qué instantes una tarea puede sufrir un bloqueo.
```

Tanto OCPP como ICPP sufren la misma cantidad de bloqueos, la diferencia esta en que OCPP los sufre durante la ejecución mientras que ICPP los sufre al inicio mayoritariamente.

## Ejercicio 2

```ad-statement

Dado un sistema formado por 3 tareas (τ 1, τ 2 , τ 3) y dos recursos compartidos  
(V y Q) con las siguientes características:

![[Inkodo-12162022_65457_PM.png|center|200]]

Teniendo en cuenta el acceso anidado a los recursos y asumiendo una única activación de  
cada una de las tareas, dibujar el correspondiente cronograma utilizando los siguientes protocolos: herencia de prioridades, techo de prioridad original (OCPP) y techo de prioridad inmediato (ICPP)
```

- **Herencia de Prioridades**

```ad-paragraph

![[Inkodo-12162022_60155_PM.png|center]]

```

- **Techo de Prioridad Original (OCPP)**

```ad-paragraph

![[Inkodo-12162022_60819_PM.png]]

```

- **Techo de Prioridad Inmediato (ICPP)**

```ad-paragraph

![[Inkodo-12162022_61423_PM.png]]

```

## Ejercicio 3

```ad-statement
Dado el sistema de tareas y recursos compartidos expuesto en el ejercicio 1:

![[Inkodo-12152022_40419_PM.png|center|200]]

```

```ad-section
title: Apartado a)

Calcula la duración máxima de bloqueo para cada tarea utilizando el protocolo de  
herencia de prioridades.
```

```ad-paragraph

![[Inkodo-12162022_62818_PM.png]]

```

```ad-section
title:Apartado b)

Calcula la duración máxima de bloqueo para cada tarea utilizando los protocolos de techo  
de prioridad.
```

```ad-paragraph

![[Inkodo-12162022_63226_PM.png]]

```

