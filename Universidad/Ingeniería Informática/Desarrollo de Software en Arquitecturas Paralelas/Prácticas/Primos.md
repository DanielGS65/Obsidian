<center style="font-weight: bold; font-size: 26 ">Práctica 2 - PRIMOS</center>

```toc
title: "## Índice"
style: number 
min_depth: 2
max_depth: 3
```

## Introducción

Para esta práctica se nos ha pedido que se realice la paralelización de un código secuencial el cual tiene como función la detección de números primos en un amplio rango de números.

El código que se nos pide probar tiene el siguiente funcionamiento:

![[Flujo de Programa.png]]

<center>Figura 1: Flujo del Programa Secuencial</center>

En el programa se realizan **dos bucles**:

- El primer bucle es un **recorrido de todos los números del rango seleccionado**.
- El segundo bucle es una comprobación sobre cada número en el cual **se mira número por número** para comprobar **si el número seleccionado es primo**.

## Programa Paralelo

Para realizar el programa paralelo separaremos la ejecución del primer bucle explicado en la introducción:

![[Primos Paralelo.png]]

<center>Figura 2: Esquema de la estructura paralela</center>

### División de la carga

Para realizar la paralelización del primer bucle se necesitará primero saber **como se va a dividir la carga** de trabajo.

Para dividirla se modificará el bucle el que se usa en el programa secuencial:

```C
for (int i = 2; i <= n; i++){
        if (esPrimo(i)==1)
            total++;
}
```

Al modificarlo tendremos en cuenta el **rango del proceso** y el **numero total de procesos** intentando lograr que cada proceso ejecute los números que ningún otro proceso este cubriendo:

```C
for (int i = 2+rank; i <= n; i = i + numproc){
        if (esPrimo(i)==1)
            total++;
}
```

### Recolecta de Datos

Una vez comprobados todos los números deberemos introducir, dentro del bucle, la instrucción **MPI_Reduce**:

```C
while(n < nmax){
	primos = numero_primos_par(n, numprocs, rank);
	MPI_Reduce(&primos, &total_global, 1, MPI_INT, MPI_SUM,0,MPI_COMM_WORLD);
	if(rank == 0){
				printf("Primos menores que %7d: %7d.", n, total_global);
	}
}
```

De esta manera **se sumaran todas la variables del resto de procesos** al proceso padre.

### Medición del tiempo

Para realizar la medición del tiempo usaremos la instrucción **MPI_WTime**, además de la instrucción **MPI_Gather** para recolectar los tiempos parciales del resto de procesos, por ultimo usaremos la instrucción **MPI_Barrier** para hacer que todos los procesos empieces a la vez.

```C
while(n < nmax){
	MPI_Barrier(MPI_COMM_WORLD);
	if(rank == 0){iTime = MPI_Wtime();}
	ipTime = MPI_Wtime();
	primos = numero_primos_par(n, numprocs, rank);
	fpTime = MPI_Wtime();
	tpTime = fpTime - ipTime;
	MPI_Barrier(MPI_COMM_WORLD);
	total_global = 0;
	if(rank == 0){fTime = MPI_Wtime();}
	MPI_Reduce(&primos, &total_global, 1, MPI_INT, MPI_SUM,0,MPI_COMM_WORLD);
	MPI_Gather(&tpTime,1, MPI_DOUBLE_PRECISION, pTime,1 , MPI_DOUBLE_PRECISION, 0 ,MPI_COMM_WORLD);
	if(rank == 0){
		printf("Primos menores que %7d: %7d. Tiempo paralelo Total: %5.2f segundos. Tiempos Parciales: |", n, total_global,(fTime - iTime));
		for(int i = 0; i<numprocs; i++){
			printf("P%d->%5.2fseg|",i,pTime[i]);
		}
		printf("\n");
		printf("SpeedUp: %5.2f | Eficiencia: %5.2f| \n", sTime[pos]/(fTime - iTime), (fTime - iTime)/(double)numprocs);
	}
}
```

## Análisis de Resultados

Una vez paralelizado el programa vamos a comprobar el rendimiento del mismo, para ello primero ejecutaremos el programa con 4 procesos para ver como se distribuye la carga entre los núcleos del ordenador.

### Distribución de la Carga

```chart
type: bar
labels: [Proceso 0, Proceso 1, Proceso 2, Proceso 3]
series:
  - title: Tiempo en Segundos
    data: [0.01,2.75,0.01,2.76]
tension: 0.2
width: 80%
labelColors: false
fill: true
beginAtZero: true
bestFit: false
bestFitTitle: undefined
bestFitNumber: 0
```

<center>Figura 3: Gráfica de ejecución con n = 5000000 y np = 4</center>

Como podemos observar en la figura 3, **solo la mitad de los procesos están realizando una carga de trabajo significativa**, esto es debido a como esta hecho el algoritmo.

Un **número impar puede ser primo**, no obstante **solo hay un número par primo**, y ese sería el número 2, además, **todos los números pares son divisibles entre 2**.

Si a un **número par le sumamos otro numero par nunca saldrá un numero impar**.

En el bucle en el que se comprueba si el número es primo **empezamos por el número 2** **más el rango del proceso y iremos sumándole el numero total de procesos**. En el caso de la figura 3, el Proceso 0 **empezaría en 2 y se le sumaria 4**, por lo que **siempre dará un resultado par** y (excepto el 2) siempre no primo, además al ser divisible entre 2, **solo realizará una vez el segundo bucle**.

Para minimizar este suceso ejecutaremos el programa con un número impar de procesos, como el 7:


```chart
type: bar
labels: [Proceso 0, Proceso 1, Proceso 2, Proceso 3, Proceso 4, Proceso 5, Proceso 6]
series:
  - title: Tiempo en Segundos
    data: [1.53,1.33,1.61,1.37,1.44,0.03,1.58]
tension: 0.2
width: 80%
labelColors: false
fill: true
beginAtZero: true
bestFit: false
bestFitTitle: undefined
bestFitNumber: 0
```

<center>Figura 4: Gráfica de ejecución con n = 5000000 y np = 7</center>

Como podemos ver, solo el Proceso 5 ha sufrido de la inexistencia de números primos debido a que este proceso ha empezado por el numero 7 (2 + 5) y ha sumado 7 de manera continua.

Para poder solucionar este problema **deberíamos modificar la división de la carga**. Una manera de hacer esto sería **enviar a cada proceso un rango de números**, por ejemplo, a n = 5000 y a np = 5 enviar los números del 2 al 1000 al proceso 0, del 1001 al 2000 al proceso 1, del 2001 al 3000 al proceso 2, etc. Para realizar esto con MPICH usaríamos o en SENDV y el RECV o el MPI_SCATTER.

```ad-note
title: Aclaración
El resto de graficas se estan realizando sobre el algoritmo explicado al inicio, no se ha implementado la solución al problema descrito arriba
```

### Comparación de Rendimiento en un solo Computador


Hora compararemos el rendimiento del programa aumentando el numero de núcleos con n = 5000000


```chart
type: bar
labels: [1 Proceso, 2 Procesos, 3 Procesos, 4 Procesos]
series:
  - title: Tiempo en Segundos
	data: [5.82, 4.71,3.04,2.82]
tension: 0.2
width: 80%
labelColors: false
fill: true
beginAtZero: true
bestFit: false
bestFitTitle: undefined
bestFitNumber: 0
```
```chart
type: bar
labels: [1 Proceso, 2 Procesos, 3 Procesos, 4 Procesos]
series:
  - title: Speed-Up
	data: [0.92,1.21,1.88,2.05]
  - title: Eficiencia
	data: [0,0.61,0.63,0.51]
tension: 0.2
width: 80%
labelColors: false
fill: true
beginAtZero: true
bestFit: false
bestFitTitle: undefined
bestFitNumber: 0
```


<center>Figura 5: Gráfica de ejecución con np variable y n = 5000000</center>

Podemos ver que todas las ejecuciones **nos dan una eficiencia de ,aproximadamente, 0.5**, esto se debe a varios factores:

- El **algoritmo no es óptimo** a la hora de paralelizarlo, como hemos visto antes, debido a que la mitad de procesos no realizan una carga de trabajo significativa, la prueba es la ejecución de tres procesos tiene una mejor eficiencia (0.61)
- La **eficiencia de los procesos MPI es peor que la ejecución del programa de manera secuencial**, esto se puede comprobar al ejecutar el programa con MPI pero con solo un proceso (np = 1), este tardara más que el secuencial.

### Comparación de Rendimiento en un MultiComputador

```chart
type: bar
labels: [1 Computador,2 Computadores, 3 Computadores, 4 Computadores, 5 Computadores, 6 Computadores]
series:
  - title: Tiempo en Segundos
	data: [0.73, 0.58, 0.73, 0.58, 0.58, 0.57]
tension: 0.2
width: 80%
labelColors: false
fill: true
beginAtZero: true
bestFit: false
bestFitTitle: undefined
bestFitNumber: 0
```
```chart
type: bar
labels: [1 Computador,2 Computadores, 3 Computadores, 4 Computadores, 5 Computadores, 6 Computadores]
series:
  - title: Speed-Up
	data: [4.66, 5.88, 4.66, 5.88, 5.88, 6.01]
  - title: Eficiencia
	data: [0.66, 0.84, 0.66, 0.84, 0.84, 0.87]
tension: 0.2
width: 80%
labelColors: false
fill: true
beginAtZero: true
bestFit: false
bestFitTitle: undefined
bestFitNumber: 0
```

<center>Figura 6: Gráfica de ejecución con número de ordenadores variable, np = 7 y n = 5000000</center>

Aquí podemos ver que **la eficiencia se acerca más a 1** siendo el **promedio de 0.75**, esto se debe a que lo estamos ejecutando con 7 procesos y solo uno de estos procesos no tiene una carga apreciable. También podemos observar que **no cambia mucho el tiempo de ejecución** y **algunos de estos cambios pueden ser debidos a la comunicación entre ordenadores**. El motivo de que no disminuya apreciablemente el tiempo de ejecución al aumentar el número de ordenadores se debe a que, al no ser un programa muy pesado, aunque metamos mucha mas potencia este no va a mejorar mucho su tiempo, ya que **hemos alcanzado el limite de mejora**, si aumentásemos el rango de números a analizar podríamos notar un poco más de diferencia al aumentar el numero de ordenadores, además de que **deberíamos aumentar el numero de procesos**.