<center style="font-weight: bold; font-size: 25 ">Práctica 5</center>

```ad-statement
Probar el problema de los lectores/escritores con prioridad en la lectura usando hilos y cerrojos (semáforos binarios) POSIX.

Crea 10 hilos lectores (que lean el recurso 10 veces cada uno) y 5 hilos escritores (que modifiquen el recurso 5 veces cada uno). El recurso puede ser, por ejemplo, una variable entera con un valor inicial -1 y al que cada escritor le asigna un valor igual a su identificador (entre 0 y 4).

Puedes añadir a cada hilo un pequeño retardo aleatorio para observar mejor la ejecución del programa. El código del programa buffer-circular-hilos.c visto en el tema 4 de teoría te puede servir de apoyo (observa que para este ejercicio no son necesarios los semáforos POSIX).

- Características del problema:
	- _Lectores_ : desean leer un recurso, dos o más pueden acceder simultáneamente al recurso
	- _Escritores_ : actualizan la información del recurso, sólo uno puede acceder al recurso: _acceso exclusivo al recurso_

- Variables y semáforos utilizados:

	- **mx** : controla el acceso en exclusión mutua a la variable compartida readers y sirve de barrera para los escritores.
	- **writer** : funciona como un semáforo de exclusión mutua para los escritores, también lo utiliza el primer/último lector para entrar/salir de la sección crítica, pero no será utilizada mientras haya otros lectores o escritores en la sección crítica
	- **readers** : número de lectores en la sección crítica
```

```ad-example
title: Código de Ejemplo
``` C
/*************************************************************************/
/*      gcc -o buffer-circular-hilos buffer-circular-hilos.c -lpthread   */
/*************************************************************************/
#include <stdio.h>
#include <string.h>
#include <errno.h>
#include <pthread.h>
#include <semaphore.h>

#define TAMBUF 8     // Tamaño del búfer circular
#define NUMDATOS 100 // Número de datos a enviar

// El buffer circular y los correspondientes punteros
int buffer[TAMBUF];
int bufin = 0;
int bufout = 0;

// Semaforo binario
pthread_mutex_t buffer_lock = PTHREAD_MUTEX_INITIALIZER;

// Variable suma
unsigned long sum = 0;

// Semaforos generales
sem_t hay_datos;
sem_t hay_sitio;

// Funciones de escritura y lectura del buffer circular
void obten_dato(int *itemp) {
    pthread_mutex_lock(&buffer_lock);
    *itemp = buffer[bufout];
    bufout = (bufout + 1) % TAMBUF;
    pthread_mutex_unlock(&buffer_lock);

    return;
}

void pon_dato(int item) {
    pthread_mutex_lock(&buffer_lock);
    buffer[bufin] = item;
    bufin = (bufin + 1) % TAMBUF;
    pthread_mutex_unlock(&buffer_lock);
    return;
}

// Funciones productor-consumidor
void *productor(void *arg1) {
    int i;

    for (i = 1; i <= NUMDATOS; i++) {
        sem_wait(&hay_sitio);
        pon_dato(i*i);
        sem_post(&hay_datos);
    }

    pthread_exit( NULL );
}

void *consumidor(void *arg2) {
    int i, midato;

    for (i = 1; i<= NUMDATOS; i++) {
        sem_wait(&hay_datos);
        obten_dato(&midato);
        sem_post(&hay_sitio);
        sum += midato;
    }

    pthread_exit( NULL );
}

// Funcion principal
int main() {
    pthread_t tidprod, tidcons;
    unsigned long i, total;

    total = 0;

    for (i = 1; i <= NUMDATOS; i++)
        total += i*i;

    printf("El resultado deberia ser %u\n", total);

    // Inicializacion de semaforos
    sem_init(&hay_datos, 0, 0);
    sem_init(&hay_sitio, 0, TAMBUF);

    // Se crean los hilos
    pthread_create(&tidprod, NULL, productor, NULL);
    pthread_create(&tidcons, NULL, consumidor, NULL);

    // Se espera a que los hilos terminen
    pthread_join(tidprod, NULL);
    pthread_join(tidcons, NULL);

    printf("Los hilos produjeron el valor %u\n", sum);

    return 0;
}
```

```ad-code
``` C
/*************************************************************************/
/*      gcc -o reader-writer reader-writer.c -lpthread   */
/*************************************************************************/

#include <stdio.h>
#include <string.h>
#include <errno.h>
#include <pthread.h>
#include <semaphore.h>
#include <stdlib.h>
#include <unistd.h>

#define MAX_READ 10
#define MAX_WRITE 5

int I = -1;

sem_t writer_sem; //Semaforo 
pthread_mutex_t mx; //Semaforo Binario
int readers = 0;

void *writer_code (void *id){
    int id1 = *(int *)id;
    for(int i=0; i<MAX_WRITE; i++){
        sem_wait(&writer_sem); //Se bloquea el acceso para modificar el valor de I
        I = *(int *) id;
        printf("Escritor %d ha actualizado el valor I a %d \n",id1,I);
        sem_post(&writer_sem); //Se libera el acceso.
        usleep(10);
    }
    pthread_exit(id);
}
void *reader_code (void *id){
    int id1 = *(int *)id;
    usleep(50);
    for(int i = 0; i<MAX_READ; i++){
        pthread_mutex_lock(&mx); //Se bloquea el acceso a la variable readers
        readers++;
        pthread_mutex_unlock(&mx); //Se desbloquea el acceso a la variable readers
        if(readers == 1){
            sem_wait(&writer_sem); // Se bloquea el acceso a los escritores si hay algun lector en cola
        }
        printf("Lector %d ha leido la I con un valor de: %d \n", id1,I);
        pthread_mutex_lock(&mx); //Se bloquea el acceso a la variable readers
        readers--;
        pthread_mutex_unlock(&mx); //Se desbloquea el acceso a la variable readers
        if(readers == 0){
            sem_post(&writer_sem); // Se desbloquea el acceso a los escritores si hay algun lector en cola
        }
        
        usleep(1);
    }
    pthread_exit(id);
}



int main() {
    int h;
    pthread_t reader[MAX_READ];
    pthread_t writer[MAX_WRITE];
    int id[MAX_READ]={0,1,2,3,4,5,6,7,8,9};
    int error;
    int *salida;
    //Inicialización de Semaforos
    pthread_mutex_init(&mx, NULL);
    sem_init(&writer_sem,0,1); //Se inicializa diciendole que sera compartido entre Hilos (0) y que tendra el valor 1
    //Creación de los hilos
    for(h=0; h<MAX_READ; h++){
        error = pthread_create( &reader[h], NULL, reader_code, &id[h]);
        if (error){
        fprintf (stderr, "Error: %d: %s\n", error, strerror (error));
        exit(-1);
        }
    }

    for(h=0; h<MAX_WRITE; h++){
        error = pthread_create( &writer[h], NULL, writer_code, &id[h]);
        if (error){
        fprintf (stderr, "Error: %d: %s\n", error, strerror (error));
        exit(-1);
        }
    }
    // Union de los Hilos
    for(h =0; h < MAX_READ; h++){
        error = pthread_join(reader[h], (void **)&salida);
        if (error)
        fprintf (stderr, "Error: %d: %s\n", error, strerror (error));
        else
        printf ("Hilo %d terminado\n", *salida);
    }

    for(h =0; h < MAX_WRITE; h++){
        error = pthread_join(writer[h], (void **)&salida);
        if (error)
        fprintf (stderr, "Error: %d: %s\n", error, strerror (error));
        else
        printf ("Hilo %d terminado\n", *salida);
    }
    //Acabar con los semaforos
    pthread_mutex_destroy(&mx); 
    sem_destroy(&writer_sem);
}

```

```
//-----------------------------SALIDA-----------------------------//
Lector 3 ha leido la I con un valor de: -1 
Lector 5 ha leido la I con un valor de: -1 
Lector 6 ha leido la I con un valor de: -1 
Lector 0 ha leido la I con un valor de: -1 
Lector 2 ha leido la I con un valor de: -1 
Lector 9 ha leido la I con un valor de: -1 
Lector 1 ha leido la I con un valor de: -1 
Escritor 0 ha actualizado el valor I a 0 
Escritor 0 ha actualizado el valor I a 0 
Lector 5 ha leido la I con un valor de: 0 
Lector 6 ha leido la I con un valor de: 0 
Lector 3 ha leido la I con un valor de: 0 
Lector 0 ha leido la I con un valor de: 0 
Lector 2 ha leido la I con un valor de: 0 
Escritor 2 ha actualizado el valor I a 2 
Escritor 3 ha actualizado el valor I a 3 
Lector 2 ha leido la I con un valor de: 3 
Escritor 0 ha actualizado el valor I a 0 
Lector 9 ha leido la I con un valor de: 0 
Escritor 1 ha actualizado el valor I a 1 
Escritor 2 ha actualizado el valor I a 2 
Lector 1 ha leido la I con un valor de: 2 
Escritor 3 ha actualizado el valor I a 3 
Lector 0 ha leido la I con un valor de: 3 
Lector 5 ha leido la I con un valor de: 3 
Lector 6 ha leido la I con un valor de: 3 
Lector 3 ha leido la I con un valor de: 3 
Lector 0 ha leido la I con un valor de: 3 
Escritor 1 ha actualizado el valor I a 1 
Lector 9 ha leido la I con un valor de: 1 
Lector 2 ha leido la I con un valor de: 1 
Escritor 0 ha actualizado el valor I a 0 
Escritor 2 ha actualizado el valor I a 2 
Lector 1 ha leido la I con un valor de: 2 
Escritor 3 ha actualizado el valor I a 3 
Lector 9 ha leido la I con un valor de: 3 
Lector 2 ha leido la I con un valor de: 3 
Escritor 1 ha actualizado el valor I a 1 
Escritor 0 ha actualizado el valor I a 0 
Lector 2 ha leido la I con un valor de: 0 
Escritor 1 ha actualizado el valor I a 1 
Lector 9 ha leido la I con un valor de: 1 
Lector 0 ha leido la I con un valor de: 1 
Lector 5 ha leido la I con un valor de: 1 
Lector 6 ha leido la I con un valor de: 1 
Lector 3 ha leido la I con un valor de: 1 
Lector 0 ha leido la I con un valor de: 1 
Lector 5 ha leido la I con un valor de: 1 
Lector 6 ha leido la I con un valor de: 1 
Lector 3 ha leido la I con un valor de: 1 
Escritor 1 ha actualizado el valor I a 1 
Lector 2 ha leido la I con un valor de: 1 
Lector 0 ha leido la I con un valor de: 1 
Lector 1 ha leido la I con un valor de: 1 
Lector 5 ha leido la I con un valor de: 1 
Lector 9 ha leido la I con un valor de: 1 
Lector 2 ha leido la I con un valor de: 1 
Lector 0 ha leido la I con un valor de: 1 
Lector 5 ha leido la I con un valor de: 1 
Lector 6 ha leido la I con un valor de: 1 
Escritor 2 ha actualizado el valor I a 2 
Escritor 3 ha actualizado el valor I a 3 
Lector 3 ha leido la I con un valor de: 3 
Lector 0 ha leido la I con un valor de: 3 
Lector 2 ha leido la I con un valor de: 3 
Lector 9 ha leido la I con un valor de: 3 
Lector 5 ha leido la I con un valor de: 3 
Lector 0 ha leido la I con un valor de: 3 
Lector 6 ha leido la I con un valor de: 3 
Lector 9 ha leido la I con un valor de: 3 
Lector 2 ha leido la I con un valor de: 3 
Lector 1 ha leido la I con un valor de: 3 
Lector 3 ha leido la I con un valor de: 3 
Escritor 2 ha actualizado el valor I a 2 
Escritor 3 ha actualizado el valor I a 3 
Lector 9 ha leido la I con un valor de: 3 
Hilo 0 terminado
Lector 5 ha leido la I con un valor de: 3 
Lector 5 ha leido la I con un valor de: 3 
Lector 9 ha leido la I con un valor de: 3 
Lector 6 ha leido la I con un valor de: 3 
Lector 3 ha leido la I con un valor de: 3 
Lector 1 ha leido la I con un valor de: 3 
Lector 1 ha leido la I con un valor de: 3 
Lector 6 ha leido la I con un valor de: 3 
Lector 3 ha leido la I con un valor de: 3 
Lector 1 ha leido la I con un valor de: 3 
Lector 6 ha leido la I con un valor de: 3 
Lector 3 ha leido la I con un valor de: 3 
Lector 1 ha leido la I con un valor de: 3 
Lector 1 ha leido la I con un valor de: 3 
Hilo 1 terminado
Hilo 2 terminado
Hilo 3 terminado
Escritor 4 ha actualizado el valor I a 4 
Escritor 4 ha actualizado el valor I a 4 
Escritor 4 ha actualizado el valor I a 4 
Escritor 4 ha actualizado el valor I a 4 
Escritor 4 ha actualizado el valor I a 4 
Lector 7 ha leido la I con un valor de: 4 
Lector 4 ha leido la I con un valor de: 4 
Lector 8 ha leido la I con un valor de: 4 
Lector 4 ha leido la I con un valor de: 4 
Lector 4 ha leido la I con un valor de: 4 
Lector 8 ha leido la I con un valor de: 4 
Lector 7 ha leido la I con un valor de: 4 
Lector 4 ha leido la I con un valor de: 4 
Lector 8 ha leido la I con un valor de: 4 
Lector 7 ha leido la I con un valor de: 4 
Lector 4 ha leido la I con un valor de: 4 
Lector 8 ha leido la I con un valor de: 4 
Lector 4 ha leido la I con un valor de: 4 
Lector 8 ha leido la I con un valor de: 4 
Lector 7 ha leido la I con un valor de: 4 
Lector 4 ha leido la I con un valor de: 4 
Lector 4 ha leido la I con un valor de: 4 
Lector 7 ha leido la I con un valor de: 4 
Lector 4 ha leido la I con un valor de: 4 
Lector 8 ha leido la I con un valor de: 4 
Lector 7 ha leido la I con un valor de: 4 
Lector 4 ha leido la I con un valor de: 4 
Lector 8 ha leido la I con un valor de: 4 
Lector 7 ha leido la I con un valor de: 4 
Lector 8 ha leido la I con un valor de: 4 
Lector 8 ha leido la I con un valor de: 4 
Hilo 4 terminado
Hilo 5 terminado
Lector 8 ha leido la I con un valor de: 4 
Hilo 6 terminado
Lector 7 ha leido la I con un valor de: 4 
Lector 7 ha leido la I con un valor de: 4 
Lector 7 ha leido la I con un valor de: 4 
Hilo 7 terminado
Hilo 8 terminado
Hilo 9 terminado
Hilo 0 terminado
Hilo 1 terminado
Hilo 2 terminado
Hilo 3 terminado
Hilo 4 terminado
```

