<center style="font-weight: bold; font-size: 25 ">Introducción a los Sistemas de Tiempo Real</center>

[[Universidad/Ingeniería Informática/Sistemas en Tiempo Real/Prácticas/Práctica_2| Ir a la práctica]]

```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
varied_style: true
```

##  Definición

Un sistema en tiempo real es un ==sistema que interacciona en un entorno de manera repetida==.

Debe responder a los estímulos del entorno en un plazo de tiempo determinado.

Se requiere que se responda de manera correcta y en un plazo de tiempo limitado.

## Características

Los sistemas en tiempo real suelen ser ==concurrentes==.

Dependencia del tiempo:

- Se debe garantizar que todas las tareas acaben antes de su deadline.
- El comportamiento del programa no es determinista.

El sistema debe ser fiable, debe cumplir sus obligaciones de manera correcta y en su deadline.

El sistema debe ser seguro, siendo capaz de recuperarse ante un fallo.


## Tipos de Sistemas en Tiempo Real

### Según su Criticidad

- **Hard Real Time**: 
	- Todas las acciones son críticas.

- **Soft Real Time**:
	- Se puede perder plazos
	- La utilidad del tiempo decrece con el tiempo.

- **Firm Real Time**:
	- Se puede perder plazos
	- Una respuesta tardía no tiene valor.

### Según su activación.

Se pueden activar por tiempo o por eventos

Esquemas de Activación:
- **Periódica** : Se ejecuta con un periodo (en ciclos)
- **Aperiódica**: Se ejecuta de manera irregular, responde a estímulos.
- **Esporádica**: Debe tener una separación entre activaciones[^1]

## Arquitecturas Software en Sistemas de Tiempo Real.

- **Ejecutivo Cíclico**: Un único programa de ejecución secuencial con un bucle de control.
	- Las lecturas se hacen con la misma frecuencia.
	- Ineficaz ante restricciones temporales.
	- Mientras se realiza una tarea, las anteriores no están activas.
	- Si algún elemento falla, todo el programa fallará.

- **Sistema Operativo en Tiempo Real**: Programas separados o procedimientos distintos en varios programas secuenciales.
	- A veces no queda claro que son actividades concurrentes o procedimientos.

- **Lenguaje de Programación en Tiempo Real**: Un solo programa que tenga concurrencia
	- Tipos:
		- **Ensamblador**: Eficiente pero Costoso.
		- **Secuencial**: Necesitan un S.O.
		- **Concurrente**: Incluido en el lenguaje.

## Aclaraciones

[^1]: Las tareas esporádicas ==pueden ser periódicas o aperiódicas==.
