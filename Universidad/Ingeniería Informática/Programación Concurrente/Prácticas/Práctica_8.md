<center style="font-weight: bold; font-size: 25 ">Práctica 8</center>

```ad-statement

## Problema de la comida de los filósofos con monitores

Implementa el problema de La comida de las filósofos usando hilos y monitores _POSIX_ (implementados con los tipos `pthread_mutex_t` y `pthread_cond_t`).

Puedes encontrar las características del problema y el código en Python que ilustre la solución en la presentación del tema 6 de teoría, además el código ’C’ distribuido con la presentación del tema 6, con una implementación de las colas de prioridad, te [pueden servir de apoyo](https://www.dlsi.ua.es/asignaturas/pc/practicas/examples/ejemplos-p8.tgz).

NOTA:

Recuerda que el objetivo de la práctica no sólo es proporcionar una solución al problema sino detectar y comentar las ventajas y/o los inconvenientes de esta aproximación a la solución de los problemas de la exclusión mutua y la sincronización.

NOTA:

Debes entregar los archivos `comida.h` y `comida.c` para el _monitor_ y el archivo `filosofos.c` para el programa principal. El ejecutable se debe obtener mediante una orden como ésta: `gcc -o filosofos filosofos.c comida.c -lpthread`
```

Esta aproximación al problema de los filósofos mediante el uso de hilos POSIX nos trae una serie de ventajas:

- Los monitores POSIX al ser un estándar del sistema operativo, estos tendrán una mayor portabilidad en caso de querer usarlo en otras plataformas.
- Tienen un preciso control del flujo del programa.

En cuanto a inconvenientes se podría argumentar que pueden ser mas complejos que otras alternativas. Pero lo que si que es una verdadera desventaja es que estos requieren de más código y que es mucho más probable que pueda generar un mayor uso del procesador y de la memoria, sobre todo en programas complejos.

En cuanto al resultado de ejecución del programa, si se ejecuta sin tiempos de espera (sleeps), tiende a acabar todas las iteraciones de un filosofo antes de dejar pasar a otro. Lo que nos indica que al liberar un semáforo, el recurso que acaba de salir no se coloca en una cola, sino que este tiene prioridad a la hora de entrar otra vez en el semáforo. Por este motivo se han colocado dos sleeps, uno en la sección critica, para comprobar el comportamiento del programa si se ocupan los palillos durante mucho tiempo, y después de la sección critica.

Este sería el código.

```ad-code
```C
/*comida.h*/

#ifndef _COMIDA_H_
#define _COMIDA_H_

#include <stdio.h>
#include <string.h>
#include <errno.h>
#include <pthread.h>
#include <semaphore.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>

#define FILOSOFOS 5

struct meal{
    int tools[FILOSOFOS];
    pthread_cond_t eatCond[FILOSOFOS];
    pthread_mutex_t mx;
};

void pickTools(struct meal *meal, int id);
void giveTools(struct meal *meal, int id);

#endif


/*comida.c*/

#include "comida.h"

void pickTools(struct meal *meal, int id){
    pthread_mutex_lock(&meal->mx);

    while(meal->tools[id] != 2) {
        pthread_cond_wait(&meal->eatCond[id], &meal->mx);
    }

    meal->tools[(id + 1) % FILOSOFOS] = meal->tools[(id+1)%FILOSOFOS] - 1;
    meal->tools[(id + FILOSOFOS - 1) % FILOSOFOS] = meal->tools[(id + FILOSOFOS - 1) % FILOSOFOS] - 1;

    pthread_mutex_unlock(&meal->mx);
    printf("El filosofo %d ha obtenido los palillos para comer. \n", id);
}

void giveTools(struct meal *meal, int id){
    pthread_mutex_lock(&meal->mx);

    meal->tools[(id + 1) % FILOSOFOS] = meal->tools[(id+1)%FILOSOFOS] + 1;
    meal->tools[(id + FILOSOFOS - 1) % FILOSOFOS] = meal->tools[(id + FILOSOFOS - 1) % FILOSOFOS] + 1;

    printf("El filosofo %d ha devuelto los palillos. \n", id);

    if(meal->tools[(id + 1) % FILOSOFOS] == 2) {
        pthread_cond_signal(&meal->eatCond[(id + 1) % FILOSOFOS]);
    }

    if(meal->tools[(id + FILOSOFOS - 1) % FILOSOFOS] == 2) {
        pthread_cond_signal(&meal->eatCond[(id + FILOSOFOS - 1) % FILOSOFOS]);
    }

    pthread_mutex_unlock(&meal->mx);
}
```

```ad-code
``` C
/*filosofos.c*/

#include <stdio.h>
#include <string.h>
#include <errno.h>
#include <pthread.h>
#include <semaphore.h>
#include <stdlib.h>
#include <unistd.h>
#include "comida.h"

#define ITER 10

struct meal *meal;

void *philosopherThread(void *id){
    for(int i=0; i<ITER; i++){
        pickTools(meal, *(int *)id);
        printf("El filosofo %d se dispone a comer. \n", *(int *)id);
        sleep(1);
        giveTools(meal,*(int *)id);
        printf("El filosofo %d vuelve a pensar. \n", *(int *)id);
        sleep(1);
    }
}

int main(){
    pthread_t philosophers[FILOSOFOS];
    int id[FILOSOFOS];
    for(int i=0; i<FILOSOFOS; i++){
        id[i] = i;
    }

    int error;
    int *status;
    

    struct meal aux = {{[0 ... (FILOSOFOS - 1)] = 2}, {[0 ... (FILOSOFOS - 1)] = PTHREAD_COND_INITIALIZER},PTHREAD_MUTEX_INITIALIZER};
    meal = &aux;

    for(int i=0; i<FILOSOFOS; i++){
        error = pthread_create(&philosophers[i], NULL, philosopherThread, &id[i]);
        if(error){
            fprintf(stderr, "Error: %d: %s\n", error, strerror(error));
            exit(-1);
        }
    }

    for (int j=0;j<FILOSOFOS;j++) {
        error = pthread_join(philosophers[j], (void **)&status);
        if(error) {
            fprintf(stderr, "Error: %d: %s\n", error, strerror(error));
        }
    }
}
```

