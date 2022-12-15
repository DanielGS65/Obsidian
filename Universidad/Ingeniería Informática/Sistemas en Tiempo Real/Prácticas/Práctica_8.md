<center style="font-weight: bold; font-size: 25 ">Práctica 8</center>


```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
varied_style: true
```

```ad-statement
Dado el sistema con el siguiente conjunto de tareas periódicas y aperiódicas:
Donde $r_i$ denota el instante de llegada de la petición aperiódica
```ad-paragraph

- !!!col
	- 1
		![[Inkodo-1292022_41806_PM.png|center|200]]
	- 2
		![[Inkodo-1292022_42329_PM.png|center|200]]
		
```

## Ejercicio 1


```ad-statement

Utilizando una planificación background:
```

```ad-section
title:Apartado a)
Indica el tiempo de respuesta para las actividades aperiódicas
```

Para ver los tiempos de respuesta se realizará un Cronograma:

```ad-paragraph
![[Inkodo-1292022_51840_PM.png]]
```

```ad-section
title: Apartado b)

¿Cómo afecta la ejecución de las tareas aperiódicas a la planificabilidad de las tareas periódicas?
```

En este caso, al usar la planificación Background, no afectaría en nada, ya que las tareas aperiódicas solo se ejecutan cuando no hay ninguna tarea periódica ejecutándose.

```ad-section
title: Apartado c)

¿Cómo se podrían mejorar los tiempos de respuesta de las tareas aperiódicas?
```

Se podrían mejorar si se irrumpiese la ejecución  de alguna tarea periódica para ejecutar las tareas aperiódicas intentando siempre que no se pierda ninguna tarea periódica, ya que estas tienen margen.

## Ejercicio 2

```ad-statement

Utilizando un servidor por consulta con $T_s = 5$ y $C_s = 2$:
```

```ad-section
title: Apartado a)

Indica el tiempo de respuesta para las actividades aperiódicas
```

```ad-paragraph
![[Inkodo-1292022_54428_PM.png]]
```

```ad-section
title: Apartado b)

Calcula la máxima utilización del procesador que puede ser asignada al servidor por  
consulta de forma que garantice la planificabilidad de las tareas periódicas
```

```ad-paragraph

![[Inkodo-12112022_41320_PM.png]]

```

```ad-section
title: Apartado c)

Según el valor calculado en el apartado anterior, ¿este servidor garantiza la planificabilidad de las tareas periódicas?
```

No, ya que el servidor utilizado tiene $C_i = 2$ y $T_i = 5$, por lo que $u_s = 0.4$ el cual es un valor mayor al calculado, por lo que no se garantiza.

```ad-section
title: Apartado d)

¿Cómo afectaría a la planificabilidad de las tareas periódicas si la tarea aperiódica a2  
tuviese los parámetros $r_2 = 5$ y $C_2 = 2$? Justifica tu respuesta.
```

```ad-paragraph

![[Inkodo-12102022_55413_PM.png]]

```


La tarea 3 no podría terminar a tiempo, por lo que no seria panificable. 

## Ejercicio 3

```ad-statement
Utilizando un servidor diferido con $T_s = 5$ y $C_s = 2$
```

```ad-section
title: Apartado a)
Indica el tiempo de respuesta para las actividades aperiódicas
```

```ad-paragraph

![[Inkodo-12102022_61840_PM.png]]

```

```ad-section
title: Apartado b)

Calcula la máxima utilización del procesador que puede ser asignada al servidor diferido  
de forma que garantice la planificabilidad de las tareas periódicas
```

```ad-paragraph

![[Inkodo-12122022_101225_AM.png]]

```

## Ejercicio 4

```ad-statement

Utilizando un servidor esporádico con $T_s = 5$ y $C_s = 2$:
```

```ad-section
title: Apartado a)
Indica el tiempo de respuesta para las actividades aperiódicas
```

```ad-paragraph

![[Inkodo-12102022_64120_PM.png]]

```

```ad-section
title: Apartado b)

Calcula la máxima utilización del procesador que puede ser asignada al servidor  
esporádico de forma que garantice la planificabilidad de las tareas periódicas
```

```ad-paragraph

![[Inkodo-12112022_44041_PM.png]]

```

