<center style="font-weight: bold; font-size: 25 ">Práctica 3</center>


```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
varied_style: true
```

<p style="font-weight: bold; font-size: 20 ">Progreso</p>


```ad-question
title: Enunciado
color: 180,20,180

Probar con hilos POSIX (subapartados a y b), con hilos Java (subapartados b y c) y con hilos Python (c y d) los siguientes algoritmos vistos en clase que utilizan la espera ocupada (algoritmos no eficientes) para la exclusión mutua:

- a. Primer intento (alternancia)
- b. Segundo intento (falta de exclusión mutua)
- c. Tercer intento (posible interbloqueo)
- d. Cuarto intento (espera infinita)

Según la ejecución de cada uno de los algoritmos, anota en tu memoria de prácticas los fallos detectados en cada uno. Si no observas la salida esperada prueba a modificar los tiempos de respuesta de tus hilos para obtenerla si fuera posible. A la hora de estudiar el comportamiento de los hilos en la sección crítica ten en cuenta que, en Java, las asignaciones de tipos primitivos se realiza de forma atómica. No olvides comentar tu código.
```

## Primer Intento: Alternancia

### C/Posix

```ad-example
title: Código
color: 255, 200, 20
``` C
/**
   $hilos
   Compilación: cc -o hilos hilos.c -lpthread
**/
#include <stdlib.h>
#include <string.h>
#include <stdio.h>
#include <pthread.h>
//Variables Globales:
//Valor Crítico
int I = 0;
//Turno de los procesos
int turno = 1;
void *codigo_del_hilo (void *id){
//Id del proceso
  int i = *(int *)id;
//Id contraria del proceso
  int j = (i == 1)? 2 : 1;
  int k;
  for(k=0; k<100; k++){
    // protocolo de entrada
    // Si no es el turno del proceso se mete en el while hasta que sea su turno
    while(turno == j);
    // Sección crítica
    I = (I + 1)%10;
    printf("En hilo %d, I=%d\n", i,I);
    // protocolo salida
    //Cede el turno al otro proceso
    turno = j;
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

### Observaciones

El código garantiza la exclusión mutua, por lo que no se modifica la variable `I` si otra tarea ya la esta cambiando, no obstante este intento tiene algunos problemas:

```ad-warning
title: Problemas

- El mayor problema que tiene esta ligado a como se entrega el turno, si alguno de los dos procesos no pudiera realizar la operación este no devolvería el turno al otro proceso y, en este caso, acabaría la tarea sin alcanzar al número deseado.
- Los procesos están muy acoplados, cosa que no es muy ideal.
```

## Segundo Intento: Falta de Exclusión Mutua

### C/Posix

```ad-example
title: Código
color: 255, 200, 20
``` C
/**
   $hilos
   Compilación: cc -o hilos hilos.c -lpthread
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


void *codigo_del_hilo (void *id){
//Id del proceso
  int id1 = *(int *)id;
//Id contraria del proceso
  int id2 = (id1 == 1)? 2 : 1;
  for(;;){
    while(C[id2] == 1);
        C[id1] = 1;
        //Pausa
        sleep(0.5);
        //Cuando sea su turno, modifica la variable
        I = I + 1;
        printf("En el hilo %d, I= %d",id1, I);
        //Libera el turno
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

### Java
```ad-example
title: Código
color: 255, 200, 20
``` java
import java.lang.Math; // para random
public class Intento2 extends Thread {
  // Variable Crítica
  static int n = 1;
  // Indicador del turno
  static volatile int C[] = {0,0};
  int id1; // identificador del hilo
  int id2; // identificador del otro hilo

  public void run() {
    try {
      for(;;){
	    //Si no es turno, pide el turno
        while(C[id2] == 1);
        C[id1] = 1;
        //Pausa
        sleep((long)(100*Math.random()));
        //Cuando sea su turno, modifica la variable
        n = n + 1;
        System.out.println("En hilo "+id1+", n = "+n);
        //Libera su el turno
        C[id1] = 0;
      }
    }
    catch( InterruptedException e ){return;}
  }

  Intento2(int id) {
	//Assignación de Id's
    this.id1 = id;
    this.id2 = (id == 1)? 0 : 1;
  }

  public static void main(String args[]) {
	// Creación de los Hilos
    Thread thr1 = new Intento2(0);
    Thread thr2 = new Intento2(1);
	//Inicio de los Hilos
    thr1.start();
    thr2.start();
  }
}
```

### Observaciones

Aunque podría parecer que este también cumple la exclusión mutua esto solo sucede porque tenemos un '_sleep_' que impide en la mayoría de intentos el acceso simultaneo de la variable crítica, pero si quitamos el '_sleep_' podemos observar que si se rompe la exclusión mutua.

```ad-warning
title: Problemas

- No garantiza la exclusión mutua.
```

## Tercer Intento: Posible interbloqueo 

### Java

```ad-example
title: Código
color: 255, 200, 20
``` Java
import java.lang.Math; // para random
public class intento3 extends Thread {
  // Variable Crítica
  static int n = 1;
  // Indicador del turno
  static volatile int C[] = {0,0};
  int id1; // identificador del hilo
  int id2; // identificador del otro hilo
  final int MAX_COUNT = 10000; // Contador Máximo

  public void run() {
    for(int i=0;i<MAX_COUNT;i++){
        //Si no tiene acceso a la sección crítica entra en el bucle
        C[id1] = 1;
        while (C[((id1+1)%2)] == 1);
        //Accede a la Sección Crítica
        n = n+1;
        //Cede el turno
        C[id1] = 0;
    }
  }

  intento3(int id) {
	//Assignación de Id's
    this.id1 = id;
    this.id2 = (id == 1)? 0 : 1;
  }

  public static void main(String args[]) {
	// Creación de los Hilos
    Thread thr1 = new intento3(0);
    Thread thr2 = new intento3(1);
	//Inicio de los Hilos
    thr1.start();
    thr2.start();
  }
}
```

### Python

```ad-example
title: Código
color: 255, 200, 20
``` python
import threading

#Estructura de turnos
states = [False, False]

THREADS = 2
MAX_COUNT = 100000
#Variable Crítica
counter = 0

#Función que restringe el acceso a la zona crítica.
def entry_critical_section(i):
    global states
    states[i] = True
    #Si no le tiene acceso a la zona crítica entra en el bucle
    while states[(i+1)%2]:
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
    for j in range(MAX_COUNT//THREADS):
	    #Se encola para entrar
        entry_critical_section(i)
        #Entra en la sección crítica
        critical_section(i)
        #Sale de la sección Crítica
        exit_critical_section(i)

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

    print("Counter value: {} Expected: {}\n".format(counter, MAX_COUNT))


if __name__ == "__main__":
    main()

```

### Observaciones

Este intento respeta la exclusión mutua pero es muy probable que sufra de un bloqueo, por lo que el programa nunca termina. En nuestro caso, sufre un bloqueo y el programa no termina.

```ad-warning
title: Problema

- El programa sufre de Interbloqueo y provoca un fallo en el programa.
```

## Cuarto Intento: Espera infinita

### Python

```ad-example
title: Código
color: 255, 200, 20
``` python
import threading

#Estructura de turnos
states = [False, False]

THREADS = 2
MAX_COUNT = 100000
#Variable Crítica
counter = 0

#Función que restringe el acceso a la zona crítica.
def entry_critical_section(i):
    global states
    states[i] = True
    #Si no le tiene acceso a la zona crítica entra en el bucle
    while states[(i+1)%2]:
      states[i] = False
      states[i] = True
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
    for j in range(MAX_COUNT//THREADS):
	    #Se encola para entrar
        entry_critical_section(i)
        #Entra en la sección crítica
        critical_section(i)
        #Sale de la sección Crítica
        exit_critical_section(i)

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

    print("Counter value: {} Expected: {}\n".format(counter, MAX_COUNT))


if __name__ == "__main__":
    main()

```

### Observaciones

El cuarto intento si que garantiza la exclusión mutua y nos da el resultado correcto, el único problema es que podemos encontrarnos con un problema de Espera Infinita.

```ad-warning
title:Problema

- No nos garantiza que se acceda a la sección crítica en un tiempo finito, lo que implica que algun proceso puede esperar infinitamente para acceder a la sección crítica.
```
