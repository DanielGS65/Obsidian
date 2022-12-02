<center style="font-weight: bold; font-size: 25 ">Planificación de Tareas</center>

```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
varied_style: true
```

## Requerimientos en tareas de tiempo real

```ad-tip
title: ### Requerimientos de tiempo
- **Completar el trabajo antes de un plazo**  
(deadline)  
- **Procesar actividades periódicas**:  
	- Requeridas a intervalos regulares  
- R**esponder a eventos aperiódicos**:  
	- Requeridos a intervalos irregulares  
	- Eventos esporádicos: intervalo mínimo entre eventos consecutivos
```

```ad-success
title:Cumplimiento de los requerimientos de tiempo

- Todas las acciones deben ocurrir **dentro del  plazo especificado**  
	- **STR crítico (hard)**  
		- Una sola ==respuesta tardía puede tener consecuencias fatales==
		
- Se pueden **peder plazos ocasionalmente**
	- **STR flexible (soft)**  
		- El valor o ==utilidad de la respuesta decrece con el tiempo== 
	- **STR firme (firm)**  
		- Una ==respuesta tardía no tiene valor==****
```ad-warning
title: Atención
Garantizar requerimientos en el peor caso posible
```

## Scheduling

El _**scheduling**_ proporciona:  
 - Un **PLANIFICADOR**  
	- Algoritmo que determina el orden del uso de recursos del sistema (en particular, la CPU)  
	- Restringe el indeterminismo de un sistema concurrente (sistema predecible)  
- Un **TEST DE PLANIFICABILIDAD**  
	- Método para determinar si el sistema cumplirá con las restricciones temporales establecidas al usar un determinado planificador


Cuando se trata de asignar la CPU, el planificador determina el orden de ejecución de los procesos.

```ad-tip
title: Tests de planificabilidad
- Test basado en factores de utilización  
- Test basado en factores de carga  
- Test basado en tiempos de respuesta  
- Test basado en el cronograma (diagrama de tiempo)
```

```ad-quote
title: Formulación general del problema de scheduling

- Un conjunto de n tareas  
- Un conjunto de m procesadores  
- Un conjunto de r tipos de recursos compartidos  
- Un grafo acíclico dirigido con las relaciones de precedencia  
- Restricciones de tiempo asociadas a las tareas
```

```ad-info
title: Simplificaciones del problema
collapse:close

- Sistema con un único procesador  
- Modelo expulsivo (preemtive)  
- Uso de prioridades fijas  
- Eliminar relaciones de precedencia  
- Eliminar recursos compartidos  
- Asumir activación simultánea de las tareas  
- Conjuntos homogéneos de tipos de tareas (sólo periódicas o sólo aperiódicas)
```

## Modelo simple de procesos


```ad-example
title: Caracterización

- Conjunto con un número fijo de procesos  
- Procesos periódicos con periodos Ti conocidos  
- Procesos independientes  
- Se ignoran las sobrecargas (overhead) del núcleo de ejecución (se asume que tienen coste cero)  
- Procesos con un deadline igual a su periodo Ti = Di  
- Tiempo de ejecución C conocido y fijo  
. Los procesos se ejecutan en un único procesador
```

```ad-question
title: Tiempos de cómputo

Hay dos formas de obtener el valor C de una tarea:  
- Medida del tiempo de ejecución  
	- Dificultad para determinar cuándo se produce el peor caso  
- Análisis del código ejecutable  
	1. Se descompone el código en un grafo de bloques secuenciales  
	2. Se calcula el tiempo de ejecución de cada bloque  
	3. Se busca el camino más largo  
	- La estimación de C puede ser muy pesimista  
	- Es difícil tener en cuenta los efectos de los dispositivos hardware (cachés, pipelines, etc.)  

Para poder calcular el tiempo de cómputo hay que evitar utilizar estructuras con un tiempo de cómputo no acotado (bucles no acotados, recursión no acotada, tareas dinámicas)
```

## Caracterización de las tareas

```ad-note
title: Notación estándar

- _C_: Tiempo de ejecución en el peor de los casos  
- _D_: Plazo de respuesta (deadline) relativo  
- _I_ : Tiempo de interferencia  
- _N_: Número de procesos en el sistema  
- _P_: Prioridad  
- _R_: Tiempo de respuesta en el peor de los casos  
- _T_: Periodo de activación  
- _u_: Factor de utilización del procesador de una determinada tarea
- $r_k$ : Instante de la activación (release time) (k+1)-ésima  
	- $r_0$ : Instante de la primera activación del proceso  
- $d_k$ : deadline absoluto de la (k+1)-ésima activación  
- ch: Factor de carga del procesador de una determinada tarea  
```ad-warning
title: Formulas
- En una tarea periódica:  
	- $u = \frac{C}{T}$
	- $r_k = r_0 + k * T$
- $d_k = rk + D$  
- $ch = \frac{C}{D}$
```

## Tipos de planificadores

- _**Expulsivo vs No expulsivo**_
	- **Expulsivo (preemptive)**  
		- La tarea que está ejecutándose puede ser interrumpida en cualquier instante para asignar el procesador a otra tarea  
		- Pueden evitar incumplimientos de plazo  
		- Permiten a los procesos de mayor prioridad ser más reactivos
	 - **No expulsivo**  
		- Una tarea una vez que comienza su ejecución, no abandona el procesador hasta que termina  
		- En arquitecturas con un solo procesador no se requiere mecanismos de exclusión mutua para acceder a recursos compartidos
- **_Estático vs Dinámico_**  
	- **Estático**  
		- Las decisiones de scheduling se basan en parámetros fijos, asignados a las tareas antes de su activación (off-line)  
		- La planificación generada es almacenada en una tabla y después ejecutada por el dispatcher  
		- No sobrecarga el sistema en tiempo de ejecución  
	- **Dinámico**  
		- Las decisiones de scheduling se basan en parámetros dinámicos que pueden cambiar durante la evolución del sistema (on-line)  
		-  Se obtienen planificaciones más flexibles adecuadas para tareas aperiódicas y situaciones excepcionales de sobrecarga

- **_Basado en garantía vs del mejor esfuerzo_**  
	- **Basado en garantía**  
		- En STR críticos  
		- No se tolera ningún incumplimiento de plazo  
		- Se utiliza test de aceptación para aceptar una nueva tarea  
		- Se pueden rechazar tareas innecesariamente al asumir el peor caso  
	- **Del mejor esfuerzo**  
		- En STR no críticos  
		- Se tolera algún incumplimiento de plazo  
		- Intenta “hacer lo mejor posible” para cumplir los plazos  
		- Mejora los tiempos de respuesta promedios  
		- Se rechazan tareas en condiciones reales de sobrecarga


```ad-info
title: Planificador óptimo

En **cualquiera de las categorías de planificadores**, se dice que un **planificador es óptimo** dentro de esa categoría **cuando es capaz de encontrar una planificación factible** de cualquier conjunto de tareas planificable
```

```ad-example
title: Consecuencias de utilizar un planificador óptimo  
  
- Si un **conjunto de tareas no es planificable con el planificador óptimo**, podemos afirmar que ==**no es planificable**==, ya que no lo será con ningún otro planificador (de esa misma categoría)  

- Si un **conjunto de tareas no es planificable con un planificador no óptimo**, **no podemos garantizar que no sea planificable**
```

