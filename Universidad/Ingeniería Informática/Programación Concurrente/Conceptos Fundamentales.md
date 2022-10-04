<center style="font-weight: bold; font-size: 25 ">Conceptos Fundamentales</center>

```toc
title: "## Índice"
style: number 
min_depth: 2
max_depth: 5
varied_style: false
```

### Concepto de Programación Concurrente

Los primeros sistemas operativos fueron los primeros sistemas concurrentes debido a que podían atender a varios usuarios al mismo tiempo.

Los hitos de la Programación concurrente fueron:

- Aparición del concepto de _thread_ o _hilo de ejecución_, que permite que los programas se ejecuten más rápido que los que utilizan el concepto de proceso.
- Aparición de lenguajes de propósito general como Java que dan soporte directo a la programación concurrente.
- Aparición de Internet: campo abonado para el desarrollo y la utilización de programas concurrentes. Navegadores, chats, etc…, están programados usando técnicas de programación concurrente. 

>[!Definicion]
La Concurrencia es el acaecimiento o concurso de varios ==procesos== en un mismo tiempoç

Un proceso es diferente a un programa:

-   Programa:
    -   Conjunto de instrucciones
    -   Estático (para que pueda hacer algo hay que ponerlo en ejecución)
    -   Secuencia de líneas de código que dicen qué hacer con un conjunto de datos
    -   Comparable con una clase en POO[^1]


-   Proceso:
    -   Programa en ejecución (definición incompleta)
    -   Dinámico
    -   Representado por un valor de contador de programa, el contenido de los registros del procesador, una pila y una sección de datos que contiene variables globales.
    -   Se puede comparar con un objeto en POO
    -   Puede haber múltiples procesos que corresponden al mismo programa igual que pasa en POO muchos objetos que provienen de una misma clase.


**Concurrencia**:
-   Dos procesos serán concurrentes cuando la primera instrucción de uno de ellos se ejecute después de la primera instrucción del otro y antes de la última
    -   Existe solapamiento en la ejecución de sus instrucciones, aunque no se ejecuten al mismo tiempo
-   Si los proceso se ejecutan exactamente al mismo tiempo → programación paralela
-   La programación concurrente es un paralelismo potencial, no real. Dependerá del hardware subyacente


Cuando varios procesos se ejecutan concurrentemente pueden
-   Colaborar
-   Competir por recursos


>En ambos casos **es necesario introducir mecanismos de comunicación y sincronización**.
>-   Programación concurrente:
>    -   Permite especificar la ejecución concurrente de las acciones de un programa, así como las técnicas para resolver problemas inherentes a la ejecución concurrente → mecanismos de comunicación y sincronización
>    -   Es más compleja que la programación tradicional

### Beneficios de la Programación Concurrente.

Cuando los procesos se ejecutan concurrentemente puede haber procesos que colaboran entre sí mientras que otros pueden competir por los mismo recursos.
Entonces si añade estos problemas, ¿cuales son los beneficios de la programación concurrente?
-   Mejor aprovechamiento de la CPU
-   Velocidad de ejecución: cuando hay mas de 1 procesador podemos tener cada proceso en un procesador
-   Por ejemplo programas cálculo numérico, tratamiento de imágenes, la propia compilación de código con `make`, etc…

Solución de problemas inherentemente concurrentes:
-   Sistemas de control
-   Tecnologías web: servidores web, chat, servidores correo, navegadores.
-   Aplicaciones basadas en interfaces de usuario (e.g. juegos)
-   Simulación realista de objetos físicos: la simulación de objetos físicos no se puede hacer de manera secuencial porque no se correspondería con la vida real
-   SGBD: la programación concurrente permite aplicar políticas adecuadas para mantener la integridad de los datos.


### Concurrencia y arquitecturas hardware

-   Cuando hablamos de hardware nos referimos al número de procesadores del sistema:
    -   Sistemas monoprocesador: 1 solo procesador
    -   Sistemas multiprocesador: más de un procesador
-   La programación concurrente presenta beneficios tanto en arquitecturas hardware monoprocesador (comunicación mediante variables compartidas) como en arquitecturas hardware multiprocesador

-   Arquitecturas hardware monoprocesador:
    -   También es posible tener ejecución concurrente de procesos
    -   No todos los procesos se ejecutan al mismo tiempo, sólo uno de ellos los estará haciendo, pero la sensación del usuario es de estar ejecutándose al mismo tiempo
    -   El SO va alternando el tiempo de procesador entre los distintos procesos → _Multiprogramación_
    -   Todos los procesos comparten la misma memoria. La forma de sincronizar y comunicar es mediante el uso de variables compartidas.

-   Arquitecturas hardware multiprocesador: permiten que exista el paralelismo real, aunque la realidad es que siempre haya más procesos que procesadores.
-   Las clasificamos en:
    
    Fuertemente acopladas :
    
    Se comunican mediante variables en memoria compartida, ya que los procesadores y otros dispositivos están conectados a un bus: _Multiproceso_
    
    Débilmente acopladas :
    
    No existe memoria compartida por los procesadores, cada procesador tiene su memoria local. Se trata de un _Procesamiento distribuido_. El mecanismo de comunicación más común es el paso de mensajes.


-   El paso de mensajes también se puede aplicar en sistemas de multiproceso y de multiprogramación.
-   La unión del paso de mensajes y la programación concurrente ha llevado a la _programación concurrente orientada a objetos_. El caso más representativo es el de JAVA


#### Concurrencia y arquitecturas hardware (cuadro resumen)

- Programa Concurrente :

	- Conjunto de acciones que pueden ser ejecutadas simultáneamente.

- Programa Paralelo :

	- Programa concurrente para sistema multiprocesador

- Programa Distribuido :

	- Programa paralelo para sistemas sin memoria compartida


Condiciones de Bernstein :

Para poder determinar si dos conjuntos de instrucciones se pueden ejecutar concurrentemente, sea Sk un conjunto de instrucciones a ejecutar, llamamos:

-   L(Sk)={a1,a2,...an} al _conjunto de lectura_, formado por todas las variables cuyos valores son referenciados (se leen) durante la ejecución de las instrucciones Sk

-   E(Sk)={b1,b2,...bm} al _conjunto de escritura_, formado por todas las variables cuyos valores son actualizados durante la ejecución

Para que se puedan ejecutar concurrentemente dos conjuntos de instrucciones Si y Sj se debe cumplir que ninguno escriba lo que el otro lee o escribe:

1.  L(Si)∩E(Sj)=∅

-   E(Si)∩L(Sj)=∅

-   E(Si)∩E(Sj)=∅


```ad-summary
title: Importante

A nivel de programa el atributo '_Static_' representa privacidad, por lo que no se alterara entre procesos.

A nivel de función el atributo '_Static_' otorga provacidad en este nivel.


```


### Ejercicios

#### 4.1 Usando las condiciones de Bernstein, construye una tabla como la anterior para el siguiente código:

```
S1 cuad := x * x;
S2 m1 := a * cuad;
S3 m2 := b * x;
S4 z := m1 + m2;
S5 y := z + c;
```

### Aclaraciones
		
> PC: Un solo programa y muchos procesos que corresponden al mismo programa

[^1]:  POO: Una sola clase y muchos objetos instancias de la clase