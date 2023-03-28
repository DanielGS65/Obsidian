<center style="font-weight: bold; font-size: 25 ">Práctica 7</center>

```ad-statement

## Problema de la barbería

Implementa el problema de la barbería usando hilos, cerrojos y semáforos anónimos POSIX (nuevamente el código del programa `buffer-circular-hilos.c` visto en el tema 4 de teoría te puede servir de apoyo).

## Características del problema

-   Hay tres barberos y tres sillas de barbero
-   La sala de espera tiene un sofá de cuatro plazas
-   Hay una sala de espera de pie
-   Hay una caja para cobrar

## Restricciones

-   Si la barbería está llena no pueden entrar más clientes, la capacidad es de 20 clientes.
-   Al entrar los clientes toman asiento en el sofá o se quedan de pie.
-   Cuando un barbero queda libre coge al cliente que lleva más tiempo esperando en el sofá y el cliente que está de pie que lleve más tiempo toma asiento en el sofá.
-   Al finalizar el corte de pelo cualquier barbero puede aceptar el pago, pero debido a que solamente hay una caja registradora, sólo acepta el pago de un cliente cada vez.
-   Los barberos dividen su tiempo entre cortar el pelo, aceptar pagos y dormir en su silla esperando clientes.
-   La barbería cerrará cuando no queden más clientes que atender.

## Semáforos utilizados

`max_capacidad`:

controla la capacidad de clientes que tiene la barbería. **Inicializado a 20**.

`sofa`:

controla el uso del sofá de los clientes. **Inicializado a 4**.

`silla_barbero`:

controla las sillas que usan los barberos para cortar el pelo. **Inicializado a 3**.

`cliente_listo`:

este semáforo sirve para indicar que el cliente ya esta sentado en la silla.

`terminado`:

indica que el corte de pelo ha terminado. **Inicializado a 0**.

`dejar_silla_barbero`:

usado cuando el cliente abandona la silla después del corte de pelo. **Inicializado a 0**.

`pago`:

controla el pago de los clientes. **Inicializado a 0**.

`recibo`:

entrega al cliente de un recibo de pago, indica que se ha aceptado el pago. **Inicializado a 0**.

`coord`:

controla la ocupación de los barberos, tanto para cortar el pelo como para cobrar. **Inicializado a 3**.
```

```ad-code

``` C
/*************************************************************************/
/*      gcc -o barbershop barbershop.c -lpthread   */
/*************************************************************************/

#include <stdio.h>
#include <string.h>
#include <errno.h>
#include <pthread.h>
#include <semaphore.h>
#include <stdlib.h>
#include <unistd.h>

#define CLIENTES 50
#define BARBEROS 3
#define SILLAS 3
#define CAPACIDAD 20
#define SOFA 4

sem_t max_capacidad;
sem_t sofa;
sem_t silla_barbero;
sem_t cliente_listo;
sem_t terminado;
sem_t dejar_silla_barbero;
sem_t pago;
sem_t recibo;
sem_t coord;

void *barberThread(void *id){
    while(1){
        sem_wait(&cliente_listo);
        sem_wait(&coord);
        printf("Barbero %d le ha cortado el pelo a un cliente. \n", *(int *)id);
        sem_post(&coord);
        sem_post(&terminado);
        sem_wait(&dejar_silla_barbero);
        sem_post(&silla_barbero);
    }
}

void *tellerThread(void *id){
    while(1){
        sem_wait(&pago);
        sem_wait(&coord);
        printf("Pago Aceptado. \n");
        sem_post(&coord);
        sem_post(&recibo);
    }
}

void *clientThread(void *id){
    printf("Cliente %d espera para entrar en la barbería. \n", *(int *)id);
    sem_wait(&max_capacidad);
    printf("Cliente %d entra. \n", *(int *)id);

    sem_wait(&sofa);
    printf("Cliente %d Se sienta en el sofa. \n", *(int *)id);

    sem_wait(&silla_barbero);
    sem_post(&sofa);
    printf("Cliente %d se sienta en la silla. \n", *(int *)id);
    sem_post(&cliente_listo);
    sem_wait(&terminado);

    sem_post(&dejar_silla_barbero);
    printf("Cliente %d ha recibido un corte de pelo. \n", *(int *)id);
    sem_post(&pago);
    sem_wait(&recibo);
    printf("Cliente %d ha realizado el pago. \n", *(int *)id);

    sem_post(&max_capacidad);
    printf("Cliente %d ha abandonado la barbería. \n", *(int *)id);
}

int main(){ 
    pthread_t clients[CLIENTES];
    int clientId[CLIENTES];
    for(int i=0; i<CLIENTES; i++){
        clientId[i] = i;
    }

    pthread_t barbers[BARBEROS];
    int barberId[BARBEROS];
    for(int i=0; i<BARBEROS; i++){
        barberId[i] = i;
    }

    pthread_t teller[1];
    int tellerId[1];
    tellerId[0] = 0;

    sem_init(&max_capacidad,0,CAPACIDAD);
    sem_init(&sofa,0,SOFA);
    sem_init(&silla_barbero,0,SILLAS);
    sem_init(&cliente_listo,0,0);
    sem_init(&terminado,0,0);
    sem_init(&dejar_silla_barbero,0,0);
    sem_init(&pago,0,0);
    sem_init(&recibo,0,0);
    sem_init(&coord,0,BARBEROS);

    int error;
    int *status;

    for(int i=0; i<BARBEROS; i++){
        error = pthread_create( &barbers[i], NULL, barberThread, &barberId[i]);
        if (error){
            fprintf (stderr, "Error: %d: %s\n", error, strerror (error));
            exit(-1);
        }
    }

    for(int j=0; j<CLIENTES; j++){ 
        error = pthread_create( &clients[j], NULL, clientThread, &clientId[j]);
        if (error){
            fprintf (stderr, "Error: %d: %s\n", error, strerror (error));
            exit(-1);
        }
    }


    error = pthread_create( &teller[0], NULL, tellerThread, &tellerId[0]);
    if (error){
        fprintf (stderr, "Error: %d: %s\n", error, strerror (error));
        exit(-1);
    }

    for(int j = 0; j < CLIENTES; j++) {
        error=pthread_join(clients[j], (void **) &status);
        if (error){
          fprintf (stderr, "Error: %d: %s\n", error, strerror (error));
        }
    }

    sem_destroy(&max_capacidad);
    sem_destroy(&sofa);
    sem_destroy(&silla_barbero);
    sem_destroy(&cliente_listo);
    sem_destroy(&terminado);
    sem_destroy(&dejar_silla_barbero);
    sem_destroy(&pago);
    sem_destroy(&recibo);
    sem_destroy(&coord);

    return 0;
}  
```