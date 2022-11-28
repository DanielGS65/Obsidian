<center style="font-weight: bold; font-size: 25 ">Práctica 4</center>

```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
varied_style: true
```

```ad-statement

Probar con hilos POSIX, con hilos Java o con Python (al menos uno en cada lenguaje), los siguientes algoritmos vistos en clase que utilizan la espera ocupada para la exclusión mutua:
-   Algoritmo de Dekker
-   Algoritmo de Peterson
-   Algoritmo de Hyman
-   Algoritmo de Lamport

Según la ejecución de cada uno de los algoritmos, anota en tu memoria si son correctos o incorrectos, y si son incorrectos qué propiedades no están cumpliendo. Si no observas la salida esperada prueba a modificar los tiempos de respuesta de tus hilos para obtenerla si fuera posible.

-   A la hora de estudiar el comportamiento de los hilos en la sección crítica ten en cuenta que p.e.;
    -   En Java, las asignaciones de tipos primitivos se realiza de forma atómica.
    -   En Python existe el llamado [GIL].
-   Debes ejecutar tu programa con el comando `taskset` para que tu ordenador trabaje en modo _monoprocesador_. Hazlo así: `taskset -c 0 proceso`.
    
Puedes cambiar el **0** por el número de núcleo que ejecutará la tarea
```

## Algoritmo de Dekker

Realizado en POSIX:

```ad-code

```C
/**
   $hilos
   Compilación: gcc -o Dekker Dekker.c -lpthread
   Ejecución: taskset -c 0 ./Dekker
**/
#include <stdlib.h>
#include <string.h>
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>
//Variables Globales:
//Valor Crítico
int I = 0;
//Turno de los procesos
int C[] = {0,0};
int turn = 0;

void *codigo_del_hilo (void *id){
//Id del proceso
  int id1 = *(int *)id;
//Id contraria del proceso
  int id2 = (id1 == 1)? 2 : 1;

  id1 = id1 - 1;
  id2 = id2 - 1;
  for(;;){
    turn = 0;
    C[id1] = 1;
    while(C[id2] == 1){
        C[id1] = 0;
        while (turn != 0);
        C[id1] = 1;
    }
    I = I + 1;
    printf("En el hilo %d, I= %d \n",id1, I);
    C[id1] = 0;
    turn = 1;
  }
  pthread_exit (id);
}
int main(){
  int h;pthread_t hilos[2];
  int id[2]={1,2};
  int error;
  int *salida;
  //Creación de los hilos
  for(h=0; h<2; h++){
    error = pthread_create( &hilos[h], NULL, codigo_del_hilo, &id[h]);
    if (error){
      fprintf (stderr, "Error: %d: %s\n", error, strerror (error));
      exit(-1);
    }
  }
  // Union de los Hilos
  for(h =0; h < 2; h++){
    error = pthread_join(hilos[h], (void **)&salida);
    if (error)
      fprintf (stderr, "Error: %d: %s\n", error, strerror (error));
    else
      printf ("Hilo %d terminado\n", *salida);
  }
}
```

#### Análisis de Resultados

En la ejecución se han cumplido todas la propiedades necesarias para poder afirmar que este algoritmo cumple con las condiciones para usarlo en la programación concurrente:

- **Cumple la Exclusión Mutua**: Solo un proceso puede entrar a la sección crítica al mismo tiempo.
- **Cumple el Progreso de Ejecución**: Al menos un proceso está en la sección crítica.
- **Espera limitada**: Un proceso debe poder entrar en la sección crítica en un tiempo limitado.

En la ejecución tampoco se puede comprobar un solape entre los valores de la variable crítica.

## Algoritmo de Peterson

Realizado en Python:

```ad-code

```python
#Ejecución: taskset -c 0 python3 Peterson.py

import threading

#Estructura de turnos
states = [False, False]

THREADS = 2
turn = 0
#Variable Crítica
counter = 0

#Función que restringe el acceso a la zona crítica.
def entry_critical_section(i):
    global states
    global turn
    j = (i+1)%2
    states[i] = True
    turn = j
    #Si no le tiene acceso a la zona crítica entra en el bucle
    while (states[j] and turn == j):
      pass
#Sección Crítica
def critical_section(i):
    global counter
    counter += 1
#Salida de la función crítica
def exit_critical_section(i):
    global states
    #Cede el turno
    states[i] = False
#Código del thread
def thread(i):
    while True:
	    #Se encola para entrar
        entry_critical_section(i)
        #Entra en la sección crítica
        critical_section(i)
        #Sale de la sección Crítica
        exit_critical_section(i)
        print("Thread: {} | Counter value: {}\n".format(i,counter))

def main():
    threads = []
    #Creación de Hilos
    for i in range(THREADS):
        # Create new threads
        t = threading.Thread(target=thread, args=(i,))
        threads.append(t)
        t.start() # start the thread

    # Wait for all threads to complete
    for t in threads:
        t.join()


if __name__ == "__main__":
    main()

```

#### Análisis de Resultados

Como en el anterior algoritmo, en la ejecución se han cumplido todas la propiedades necesarias para poder afirmar que este algoritmo cumple con las condiciones para usarlo en la programación concurrente:

- **Cumple la Exclusión Mutua**: Solo un proceso puede entrar a la sección crítica al mismo tiempo.
- **Cumple el Progreso de Ejecución**: Al menos un proceso está en la sección crítica.
- **Espera limitada**: Un proceso debe poder entrar en la sección crítica en un tiempo limitado.

## Algoritmo de HyMan

Realizado en POSIX:

```ad-code

```C
/**
  $hilos
  Compilación: gcc -o Hyman Hyman.c -lpthread
  Ejecución: taskset -c 0 ./Hyman
**/
#include <stdlib.h>
#include <string.h>
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>
//Variables Globales:
//Valor Crítico
int I = 0;
//Turno de los procesos
int C[] = {0,0};
int turn = 0;

void *codigo_del_hilo (void *id){
//Id del proceso
  int id1 = *(int *)id;
//Id contraria del proceso
  int id2 = (id1 == 1)? 2 : 1;

  id1 = id1 - 1;
  id2 = id2 - 1;
  for(;;){
    turn = id1;
    C[id1] = 1;
    while(turn != id1){
        while (C[id1] == 1);
        turn = id1;
    }
    I = I + 1;
    printf("En el hilo %d, I= %d \n",id1, I);
    C[id1] = 0;
  }
  pthread_exit (id);
}
int main(){
  int h;pthread_t hilos[2];
  int id[2]={1,2};
  int error;
  int *salida;
  //Creación de los hilos
  for(h=0; h<2; h++){
    error = pthread_create( &hilos[h], NULL, codigo_del_hilo, &id[h]);
    if (error){
      fprintf (stderr, "Error: %d: %s\n", error, strerror (error));
      exit(-1);
    }
  }
  // Union de los Hilos
  for(h =0; h < 2; h++){
    error = pthread_join(hilos[h], (void **)&salida);
    if (error)
      fprintf (stderr, "Error: %d: %s\n", error, strerror (error));
    else
      printf ("Hilo %d terminado\n", *salida);
  }
}
```

#### Análisis de Resultados

En la ejecución se han cumplido todas la propiedades, no obstante, el algoritmo de Hayman no cumple todas las propiedades:

- **No Cumple la Exclusión Mutua**: No asegura que solo un proceso pueda entrar a la sección crítica al mismo tiempo.

## Algoritmo de Lamport

Código realizado en Java:

```ad-code

```java
//Compilación: javac .*java
//Ejecución: taskset -c 0 java Lamport

import java.lang.Math; // para random
import java.util.Arrays;
public class Lamport extends Thread {
  // Variable Crítica
  static int n = 0;
  // Indicador del turno
  static volatile boolean C[] = {false,false};
  static volatile int Num[] = {0,0};

  int id1; // identificador del hilo
  int id2; // identificador del otro hilo

  public void run() {
    while(true){
        C[id1] = true;
        int max = Num[1];
        if(Num[0] > Num[1]){
            max = Num[0];
        }
        Num[id1] = 1 + max;
        C[id1] = false;
        for(int i = 0; i < 2; i++){
            while(C[id2]);
            while ((Num[id2] > 0) && (Num[id2] < Num[id1]) || (Num[id2] == Num[id1]) && (id2 < id1));
        }

        n = n+1;
        System.out.println("Thread: " + id1 + ", n = " + n);

        Num[id1] = 0;
    }
  }

  Lamport(int id) {
	//Assignación de Id's
    this.id1 = id;
    this.id2 = (id == 1)? 0 : 1;
  }

  public static void main(String args[]) {
	// Creación de los Hilos
    Thread thr1 = new Lamport(0);
    Thread thr2 = new Lamport(1);
	//Inicio de los Hilos
    thr1.start();
    thr2.start();
  }
}
```

#### Análisis de Resultados

En la ejecución se han cumplido todas la propiedades necesarias para poder afirmar que este algoritmo también cumple con las condiciones para usarlo en la programación concurrente:

- **Cumple la Exclusión Mutua**: Solo un proceso puede entrar a la sección crítica al mismo tiempo.
- **Cumple el Progreso de Ejecución**: Al menos un proceso está en la sección crítica.
- **Espera limitada**: Un proceso debe poder entrar en la sección crítica en un tiempo limitado.
