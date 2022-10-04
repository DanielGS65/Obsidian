<center style="font-weight: bold; font-size: 25 ">Práctica 1</center>

```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
varied_style: true
```

<p style="font-weight: bold; font-size: 20 ">Progreso</p>

- [ ] Procesos en Unix/C
	- [x] a)
	- [x] b)
	- [ ] c)
	- [ ] d)
- [ ] Hilos POSIX
	- [ ] a)
	- [ ] b)

## Procesos en Unix/C

```ad-info
title: Enunciado
collapse: open
Estudia el siguiente programa:

``` C
/* PROCESOS */

#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>

#define NUM_PROCESOS 5

int I = 0;

void codigo_del_proceso(int id) {
  int i;

  for (i = 0; i < 50; i++)
    printf("Proceso(id=%d): i = %d, I = %d\n", id, i, I++);

  // el id se almacena en los bits 8 al 15 antes de devolverlo al padre
  exit(id);
}

int main() {
  int p;
  int id[NUM_PROCESOS] = {1, 2, 3, 4, 5};
  int pid;
  int salida;

  for (p = 0; p < NUM_PROCESOS; p++) {
    pid = fork();
    if (pid == -1) {
      perror("Error al crear un proceso: ");
      exit(-1);
    } else if (pid == 0) // Codigo del hijo
      codigo_del_proceso(id[p]);
  }

  // Codigo del padre
  for (p = 0; p < NUM_PROCESOS; p++) {
    pid = wait(&salida);
    printf("Proceso(pid=%d) con id = %x terminado y status = %d \n", pid,
           salida >> 8, WEXITSTATUS(salida));
  }
  return 0;
}
```

```ad-question
title:Apartado a)

Comenta qué se espera que ocurra en cada porción de código y la salida. ¿Qué crees que hace `WEXITSTATUS` ? ¿Es una función o una _macro_ del preprocesador de `C`?
```

``` C
/* PROCESOS */

#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>

#define NUM_PROCESOS 5

int I = 0;

void codigo_del_proceso(int id) {
  int i;

  for (i = 0; i < 50; i++)
    printf("Proceso(id=%d): i = %d, I = %d\n", id, i, I++);

  // el id se almacena en los bits 8 al 15 antes de devolverlo al padre
  exit(id);
}

// Función principal del programa
int main() {
  //Variable creada como contador para los bucles	
  int p;
  // Array contenedos de las id de los procesos
  int id[NUM_PROCESOS] = {1, 2, 3, 4, 5};
  //Variable que almacena la ID del proceso (Process ID).
  int pid;
  //
  int salida;

  //Bucle que crea los procesos.
  for (p = 0; p < NUM_PROCESOS; p++) {
    //Se genera el proceso
    pid = fork();
    //En caso de que falle la creación se da un error y termina el proceso
    if (pid == -1) {
      perror("Error al crear un proceso: ");
      exit(-1);
      // Si el PID = 0 (es el proceso Hijo), entonces se llamará a la función codigo_del_proceso 
    } else if (pid == 0) // Codigo del hijo
      codigo_del_proceso(id[p]);
  }

  // Codigo del padre
  //Bucle que hace que los procesos padres esperen a que los procesos hijos terminen su ejecución
  for (p = 0; p < NUM_PROCESOS; p++) {
	//El PID sera el PID del proceso una vez termine su proceso hijo, ademas se le pasará la variable 'salida' por referencia.
    pid = wait(&salida);
    printf("Proceso(pid=%d) con id = %x terminado y status = %d \n", pid,
           salida >> 8, WEXITSTATUS(salida));
  }
  return 0;
}
```

WEXITSTATUS devolverá una evaluación de la finalización del proceso hijo perteneciente al proceso en el cual se ejecuta esta instrucción. Esta instrucción es una ==_macro_ de C==.

```ad-question
title:Apartado b)

Ahora edita y ejecuta un programa de ejemplo con dos procesos concurrentes que impriman en pantalla `1` y `2` respectivamente.
```

``` C
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>

int main(){

    int pid;

    pid = fork();

    if(pid == 0){
        printf("2 \n");
    }
    else if(pid == -1){
        printf("\n Warning: An ERROR has ocurred at creating a new process");
    }
    else{
        printf("1 \n");
    }

    return 0;
}
```

```ad-question
title:Apartado c)

Transforma el código anterior para que definamos una única función a la que se le pase como parámetro el valor entero que se desea imprimir. Instancia a continuación dos procesos que ejecuten dicha función a la que le pasaremos como parámetro un `1` y un `2` respectivamente.
```