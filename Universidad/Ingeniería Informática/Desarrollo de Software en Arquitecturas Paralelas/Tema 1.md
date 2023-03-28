<center style="font-weight: bold; font-size: 25 ">Motivación y aspectos de la computación paralela</center>

```toc
title: "## Índice"
style: number 
min_depth: 2
max_depth: 2
```

## Necesidades de la computación en paralelo

Hay una gran demanda de:

- **Computadores con gran potencia de Calculo**
- **Velocidad de computación**
- **Problemas con complejidad elevada:**
	- Problemas de gran dimensión: coste polinomial de grado alto o aplicabilidad a grandes problemas
	- Problemas de tiempo real
	- Problemas de gran desafío: gran importancia social. Estudio del genoma humano, predicción meteorológica mundial, modelado fenómenos sísmicos, etc.

Las limitaciones de la computación secuencial son:
- **Restricciones Lógicas**
	- Complejidad intrínseca de los algoritmos.
	- Problemas de gran dimensión.
	- Problemas de tiempo real
- **Restricciones tecnológicas:**  
	- Velocidad de transmisión
	- Límite de integración
	- Estancamiento en los incrementos de la frecuencia de reloj
- **Limitaciones económicas:**
	- Es considerablemente muy caro hacer que un único procesador sea más rápido. Es mucho más barato utilizar un número mayor de procesadores para conseguir el mismo propósito (o incluso mejorarlo).

Las Soluciones serian:

- **Software**:
	- Mejoras en los algoritmos
- **Hardware**:
	- Mejoras en la tecnología de computadores
- **Paralelismo**:
	- Replicar unidades de tratamiento de información con el objetivo de repartir tareas entre las  mismas.

Tradicionalmente los programas se han desarrollado para el cálculo en serie:  
- Funcionan en un ordenador con una única CPU.  
- Un problema se divide en un conjunto de instrucciones.  
- Las instrucciones se ejecutan secuencialmente.  
- Únicamente una instrucción se ejecuta cada vez.

El cálculo en paralelo consiste en usar múltiples recursos simultáneamente para resolver un problema dado:  
- Hace uso de un ordenador con varias CPU's.  
- El problema se divide en partes independientes.  
- Cada parte se divide en un conjunto de instrucciones.  
- Las instrucciones se ejecutan secuencialmente.  
- Las partes se resuelven simultáneamente.

```ad-info
title: Definiciones

- **Computador paralelo**: Capaz de ejecutar varias instrucciones simultáneamente.  
- **Computación paralela**: Uso de varios procesadores trabajando juntos para resolver una tarea común:  
	- Cada procesador trabaja en una porción del problema.  
	- Los procesos pueden intercambiar datos, a través de las direcciones de memoria compartidas o mediante una red de interconexión.  
- A las arquitecturas de este tipo se las denomina **Arquitecturas Paralelas.**
```

PRACTICAS:

