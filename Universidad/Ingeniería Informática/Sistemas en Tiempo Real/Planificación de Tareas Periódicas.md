<center style="font-weight: bold; font-size: 25 ">PLANIFICACIÓN DE TAREAS PERIÓDICAS</center>

```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
varied_style: true
```

## Planificadores basados en prioridades

```ad-info
title: Scheduling

- El scheduling proporciona:  
	- Un **PLANIFICADOR**  
		- Algoritmo que determina el orden del uso de recursos del sistema (en particular, la CPU)  
		- Restringe el indeterminismo de un sistema concurrente (sistema predecible)  
	- Un **TEST DE PLANIFICABILIDAD**  
		- Método para determinar si el sistema cumplirá con las restricciones temporales establecidas al usar un determinado planificador
```

```ad-tip
title: Planificador óptimo

En **cualquiera de las categorías de planificadores**, se dice que un **planificador es óptimo** dentro de esa categoría **cuando es capaz de encontrar una planificación factible** de cualquier conjunto de tareas planificable
```

```ad-example
title: Consecuencias de utilizar un planificador óptimo  
  
- Si un **conjunto de tareas no es planificable con el planificador óptimo**, podemos afirmar que ==**no es planificable**==, ya que no lo será con ningún otro planificador (de esa misma categoría)  

- Si un **conjunto de tareas no es planificable con un planificador no óptimo**, **no podemos garantizar que no sea planificable**
```

- En los sistemas de tiempo real la prioridad de un proceso se deriva de sus requerimientos temporales  
- De todos los procesos listos para ejecución, se ejecuta el de mayor prioridad  
- En un esquema expulsivo (preemptive), hay un cambio inmediato al proceso de mayor prioridad  
- En un esquema no expulsivo (non-preemptive), el proceso de baja prioridad podrá continuar hasta que acabe  
- Los esquemas expulsivos permiten a los procesos de mayor prioridad ser más reactivos y, por lo tanto, son los más adecuados

```ad-info
title: Tipos de Planificadores

- **Prioridades fijas**: Se basa en parámetros fijos de la tarea y se establece antes de su ejecución
	- **Rate Monotonic**:
	```ad-warning
	title:
	color: 255,70,0
	- Se supone que $D_i = T_i$  
	- La prioridad se asigna en función del **PERIODO**  
	- A menor periodo ⇒ mayor prioridad  
	- $T_i < T_j ⇒ P_i > P_j$
	```
	- **Deadline Monotonic**
	```ad-warning
	title:
	color: 255,70,0
	- Se supone que $D_i ≤ T_i$
	- La prioridad se asigna en función del **DEADLINE**
	- A menor deadline ⇒ mayor prioridad  
	- $D_i < D_j ⇒ P_i > P_j$
	```
- **Propiedades Dinamicas**: Se basa en parámetros dinámicos de la tarea y la prioridad cambia durante su ejecución
	- **Earliest Deadline First**
	```ad-warning
	title:
	color: 255,70,0
	- Se elige la tarea con **Deadline Absoluto** más cercano
	```
```

```ad-example
title: Fija Vs Dinámica

- EDF permite mayores utilizaciones que FPS  
- EDF es más complejo de implementar y es ejecutado on-line  
- En situaciones de sobrecarga, EDF es impredecible mientras que FPS es más predecible
```

## Tests de planificabilidad

```ad-info
title: Tipos de Test de Planificabilidad

- Si un test de planificabilidad es **suficiente**, un ==resultado positivo== del test ==garantiza que todos los deadlines siempre se cumplen==  
- Si un test de planificabilidad es **necesario**, un ==resultado negativo== del test ==implica que algún deadline será incumplido==
```


### Test de factor de utilización

```ad-tip
title: Repaso

- El factor de utilización de una tarea es el porcentaje de uso de procesador que requiere una tarea para su ejecución: $u = \frac{C}{T}$
- y es diferente del factor de carga: $ch = \frac{C}{D}$
```

#### Utilización para Tareas con Prioridades Fijas

```ad-tip
title: Condicion
Se trata solo de una Condición **SUFICIENTE**
```

$\sum^N_i{\frac{C_i}{T_i}} ≤ L_u(NumTareas)$

```ad-warning
title: Importante
$L_u$ sera proporcionado en el problema a realizar, **NO HACE FALTA CALCULARLO**
```

#### Utilización para Tareas con prioridades Dinámicas

```ad-tip
title: Condición
Se trata de una Condición **SUFICIENTE** y **NECESARIA**
```

$\sum^N_i{\frac{C_i}{T_i}} ≤ 1$

### Test de factor de carga

#### Utilización para Tareas con Prioridades Fijas

```ad-tip
title: Condicion
Se trata solo de una Condición **SUFICIENTE**
```

$\sum^N_i{\frac{C_i}{D_i}} ≤ N(2^\frac{1}{N} - 1)$

#### Utilización para Tareas con prioridades Dinámicas

```ad-tip
title: Condicion
Se trata solo de una Condición **SUFICIENTE**
```

$\sum^N_i{\frac{C_i}{D_i}} ≤ 1$

### Test basado en el cronograma

Se trata de obtener el peor tiempo de respuesta de cada tarea observando el cronograma.

```ad-tip
title: Conceptos a estudiar
- **Instante crítico** : Máxima carga del procesador (En las propiedades fijas es el instante inicial de la tarea con el mayor periodo)
- **Hiperperiodo** : mínimo común múltiplo de los periodos de activación de todas las tareas (También es cuando todas las tareas se vuelven a activar al mismo tiempo)
```

#### Utilización para Tareas con Prioridades Fijas

```ad-tip
title: Condición
Se trata de una Condición **SUFICIENTE** y **NECESARIA**
```

Solo sería necesario realizar el cronograma durante el **Instante Crítico**, a no ser que nos pidan los peores tiempos de respuesta.

#### Utilización para Tareas con prioridades Dinámicas

```ad-tip
title: Condición
Se trata de una Condición **SUFICIENTE** y **NECESARIA**
```

Solo sería necesario realizar el cronograma hasta el **HiperPeriodo**.

### Test de tiempos de respuesta




