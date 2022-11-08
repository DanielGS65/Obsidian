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
    // Si es el turno del proceso se mete en el while
    while(turno == j);
    // Sección crítica
    I = (I + 1)%10;
    printf("En hilo %d, I=%d\n", i,I);
    // protocolo salida
    //Cuando le quitan el turno pide el turno
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
        while(C[id2] == 1);
        C[id1] = 1;
        sleep((long)(100*Math.random()));
        n = n + 1;
        System.out.println("En hilo "+id1+", n = "+n);
        C[id1] = 0;;
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

## Tercer Intento: Posible interbloqueo 

### Java

```ad-example
title: Código
color: 255, 200, 20
``` C
```

### Python

```ad-example
title: Código
color: 255, 200, 20
``` C
```

### Observaciones

## Cuarto Intento: Espera infinita

### Python

```ad-example
title: Código
color: 255, 200, 20
``` C
```

### Observaciones