<center style="font-weight: bold; font-size: 25 ">Práctica 7</center>

```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
varied_style: true
```

## Ejercicio 1

```ad-statement

En un sistema concurrente de tareas, si tienes que verificar la planificabilidad de un conjunto de tareas periódicas independientes
```

```ad-section
title: Apartado a)

¿Qué algoritmo de planificación de prioridades fijas debes utilizar? Razona tu respuesta.
```

Se podrían utilizar dos planificadores:

- **Rate Monotonic** si sabemos que su ==DeadLine es igual a su Periodo==.
- Deadline Monotonic si sabemos que su ==DeadLine es mayor que su Periodo==.

Ambos son válidos ya que los dos son ==**Planificadores Óptimos**==

```ad-section
title: Apartado b)

¿Qué algoritmo de planificación de prioridades dinámicas debes utilizar? Razona tu respuesta.
```

Se podrá utilizar el planificador **Earliest Deadline First** si su ==DeadLine es igual a su Periodo==.

Es válido ya que es un ==**Planificador Optimo**==.

## Ejercicio 2

```ad-statement

Dado el siguiente conjunto de tareas concurrentes:

![[Pasted image 20221128123029.png|center|200]]

Verifica la planificabilidad del sistema utilizando el test de factores de utilización:
```

```ad-section
title: Apartado a)
Con el algoritmo de planificación RM
```

```ad-paragraph

- !!!col
	- 1
		![[Pasted image 20221128124650.png|200|center]]
	- 2
		- $u_i = \frac{C_i}{T_i}$ 
		- $L_u(3) = 0,779$

```

```ad-paragraph
![[Pasted image 20221128164302.png|center]]
```


```ad-section
title: Apartado b)
Con el algoritmo de planificación EDF
```

```ad-paragraph

- !!!col
	- 1
		![[Pasted image 20221128123029.png|center|200]]
	- 2
		- $u_i = \frac{C_i}{T_i}$ 
		- $L_u = 1$

```

```ad-paragraph
![[Pasted image 20221128163919.png]]
```

## Ejercicio 3

```ad-statement

Cuando se verifica la planificabilidad de un conjunto de tareas periódicas independientes mediante la representación del correspondiente cronograma, ¿cuál es el menor instante de tiempo en el que se puede afirmar que el sistema es planificable?
```

```ad-section
title: Apartado a)
Si se utiliza un planificador de prioridades fijas
```

Habría que parar en el final del primer periodo de la tarea con el periodo más alto.

Esto es porque el instante critico de un programa con varias tareas con prioridades fijas sucede justo al inicio de este, por lo que solo seria necesario comprobar el primer periodo de cada tarea, por lo que la que tenga mayor periodo tendrá el fin del cronograma.

```ad-section
title: Apartado b)
Si se utiliza un planificador de prioridades dinámicas
```

Habría que parar cuando se alcance el primer Hiperperiodo. El hiper periodo será en el momento que indique el mínimo común múltiplo de todos los periodos de las tareas o también cuando estas tengan un instante de iniciación que sea igual para todas las tareas por segunda vez.

Esto se debe a que una vez llegado al hiperperiodo se repetirá la secuencia otra vez, así que comprobado esa sección ya se sabrá como funcionará en los siguientes instantes.

## Ejercicio 4

```ad-statement
Dado el conjunto de tareas del ejercicio 2, verifica la planificabilidad del sistema con el planificador RM
```

```ad-section
title: Apartado a)

Utilizando el test de tiempos de respuesta
```

```ad-paragraph
![[Inkodo-1222022_32531_PM.png]]
```

```ad-section
title: Apartado b)

Utilizando su correspondiente cronograma.
```

```ad-paragraph
![[Pasted image 20221128152327.png]]
```

## Ejercicio 5

```ad-statement
Dado el siguiente conjunto de tareas:

![[Pasted image 20221202163325.png|center|200]]
```

```ad-section
title: Apartado a)

Verifica la planificabilidad del sistema para el planificador RM
```

Para verificar la planificabilidad del sistema se usara el test de tiempos de respuesta:

```ad-paragraph
![[Inkodo-1222022_32532_PM.png]]
```

```ad-section
title:Apartado b)

Calcula el peor tiempo de respuesta usando un planificador EDF e indica si el sistema es o no planificable con dicho planificador.
```

La única manera de calcular los peores tiempos de respuesta en un sistema con Prioridades Dinámicas es con un Cronograma.

```ad-paragraph
![[Pasted image 20221202163325.png|200]]
![[Pasted image 20221202172326.png]]
```

## Ejercicio 6

```ad-statement
Dado el siguiente conjunto de tareas:

![[Pasted image 20221203113500.png|center|200]]
```

```ad-section
title: Apartado a)

Teniendo en cuenta un planificador de prioridades fijas, ¿qué test utilizarías para verificar la planificabilidad de dicho conjunto, el test de factores de utilización o el test de factores de carga?  

Justifica tu elección y aplícalo.
```

Se tendría que aplicar el Test de Factores de Carga, ya que este mes valido cuando la deadline de la tarea  es menor o igual al periodo de esta ($D_i <= T_i$).

El Test de Factores de Utilización pide que la Deadline de la tarea sea igual al periodo ($D_i = T_i$) por lo que no lo podemos usar en este caso.

Para aplicar las prioridades se usará el planificador DM

```ad-paragraph

![[Inkodo-1232022_103452_AM.png]]

```

```ad-section
title: Apartado b)

Dibujar el cronograma que demuestra la planificabilidad del sistema según la asignación de prioridades del apartado anterior.
```


```ad-paragraph
![[Inkodo-1232022_105750_AM.png]]
```

```ad-section
title: Apartado c)

A partir del cronograma realizado, indicar los tiempos de respuesta en el peor caso de cada tarea.
```

- Tarea 1 -> 3 Instantes
- Tarea 2 -> 6 Instantes
- Terea 3 -> 9 Instantes
- Tarea 4 -> 20 Instantes

## Ejercicio 7

```ad-statement

Dado el siguiente conjunto de tareas:

![[Inkodo-1232022_110728_AM.png|center|200]]
```

```ad-section
title: Apartado a)

Verificar la planificabilidad de dicho conjunto bajo un planificador EDF utilizando el test de factores de carga. Después de aplicar el test ¿puedes afirmar que el sistema es o no planificable?
```

```ad-paragraph

![[Inkodo-1232022_110848_AM.png]]
```

No podemos afirmar si el sistema es realmente panificable, ya que este test solo nos indica si es suficiente, por lo que habría que realizar una prueba mas rigurosa, como un cronograma.

```ad-section
title: Apartado b)

Dibujar el cronograma que demuestre la planificabilidad o no del sistema usando el algoritmo EDF.
```

```ad-paragraph

![[Inkodo-1232022_113217_AM.png]]
```
