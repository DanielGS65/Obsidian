<center style="font-weight: bold; font-size: 25 ">Práctica 6</center>

```ad-statement

## Problema de los Caníbales

-   Una tribu cena en comunidad una gran olla que contiene **M** misioneros cocinados.
-   Cuando un miembro de la tribu quiere comer, él mismo se sirve de la olla un misionero, a menos que esté vacía. Los miembros de la tribu se sirven de la olla de uno en uno.
-   Si la olla está vacía, el que va a cenar despierta al cocinero y se espera a que esté llena la olla.
-   Desarrollar el código de las acciones de los miembros de la tribu y el cocinero usando semáforos.

**Usa las siguientes variables:**

olla :

Entero que indica el número de misioneros en la olla. Estará inicializada a **M**.

mutex :

Semáforo para proteger la exclusión mutua sobre la variable olla. Inicializado a `1`.

espera :

Semáforo utilizado para hacer que el que va a cenar se detenga hasta que el cocinero llene la olla cuando está vacía. Inicializado a `0`.

coci :

Semáforo inicializado a `0` y usado para que el cocinero no haga nada cuando la olla no está vacía.
```

```ad-code
``` C
/*************************************************************************/
/*      gcc -o canibal canibal.c -lpthread   */
/*************************************************************************/

#include <stdio.h>
#include <string.h>
#include <errno.h>
#include <pthread.h>
#include <semaphore.h>
#include <stdlib.h>
#include <unistd.h>

#define CANIBALES 50
#define CAPACIDAD_OLLA 15

int olla = CAPACIDAD_OLLA;

//Semaforos
sem_t espera;  
sem_t coci;
//Semaforo Binario
pthread_mutex_t mutex;

void *canibal_code (void *id){
    pthread_mutex_lock(&mutex);

    if(olla == 0){
        sem_post(&coci);
        printf("El Canibal %d va a despertar al Cocinero\n",*(int *)id);
        sem_wait(&espera);
    }
    olla = olla - 1;
    printf("El Canibal %d ha comido\n",*(int *)id);
    printf("Capacidad de olla: %d\n", olla);
    pthread_mutex_unlock(&mutex);

    pthread_exit(id);
}

void *cocinero_code (void *id){
    int num_comidas = 0;
    if(CANIBALES % CAPACIDAD_OLLA == 0){num_comidas = CANIBALES/CAPACIDAD_OLLA - 1;}
    else{num_comidas = CANIBALES/CAPACIDAD_OLLA;}

    for(int i = 0; i<num_comidas; i++){
        sem_wait(&coci);
        printf("El Cocinero se ha despertado\n");
        olla = CAPACIDAD_OLLA;
        printf("La olla ha sido rellenada\n");
        printf("Capacidad de olla: %d\n", olla);
        sem_post(&espera);
    }
    pthread_exit(id);
}

int main() {
    pthread_t canibales[CANIBALES];
    pthread_t cocinero[1];
    int id[CANIBALES];
    for(int i=0; i< CANIBALES ; i++){
        id[i] = i;
    }
    int error;
    int *salida;
    //Inicialización de Semaforos Binarios
    pthread_mutex_init(&mutex, NULL);
    //Inicialización de Semaforos
    sem_init(&espera,0,0);
    sem_init(&coci,0,0);
    //Creación de los hilos Canibales
    for(int h=0; h<CANIBALES; h++){
        error = pthread_create( &canibales[h], NULL, canibal_code, &id[h]);
        if (error){
            fprintf (stderr, "Error: %d: %s\n", error, strerror (error));
            exit(-1);
        }
    }

    //Creación del Hilo Cocinero
    error = pthread_create( &cocinero[0], NULL, cocinero_code, &id[0]);
        if (error){
            fprintf (stderr, "Error: %d: %s\n", error, strerror (error));
            exit(-1);
        }
    
    //Union de los Hilos Canibales
    for(int h =0; h < CANIBALES; h++){
        error = pthread_join(canibales[h], (void **)&salida);
        if (error)
            fprintf (stderr, "Error: %d: %s\n", error, strerror (error));
    }

    //Union del Hilo Cocinero
    error = pthread_join(cocinero[0], (void **)&salida);
    if (error)
        fprintf (stderr, "Error: %d: %s\n", error, strerror (error));
    
    //Destruit los semaforos
    sem_destroy(&espera);
    sem_destroy(&coci);
    pthread_mutex_destroy(&mutex); 

    return 0;
}
```
