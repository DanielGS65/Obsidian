<center style="font-weight: bold; font-size: 25 ">Práctica 2</center>


```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
varied_style: true
```

<p style="font-weight: bold; font-size: 20 ">Progreso</p>

- [x] Hilos en Java
	- [x] a)
	- [x] b)
	- [x] c)
	- [x] d)
- [x] Hilos en Python
- [x] Hilos en Rust

## Hilos en Java

```ad-info
title: Enunciado

Debemos implementar en `Java` un hilo que lance la impresión de una palabra `10` veces. En el programa principal crearemos dos hilos de ese tipo inicializados con dos palabras diferentes y los lanzaremos para que se ejecuten a la vez.

Los hilos se deben implementar de las dos formas vistas en clase de teoría:

```

```ad-question
title: Apartado a)

Creando una clase que herede de la clase Thread y que ejecute los hilos desde el programa principal. Para crear un _hilo_ el programador define una clase que extiende la clase `Thread` que es parte del paquete `java.lang`. Comenta los resultados obtenidos al utilizar este método.
```

``` java

//threadJava.java

public class threadJava extends Thread{
    private static int cont  = 10;
    private char word;

    public threadJava(char word){
        this.word = word;
    } 

    public void run(){
        for(int i = 0; i<cont; i++){
            System.out.print(this.word);
        }
    }
}

//Main.java

public class Main{
    public static void main(String[] args){
        
        threadJava th1 = new threadJava('A');
        threadJava th2 = new threadJava('B');
        try{
            th1.start();
            th2.start();
        }
        catch(Exception e){
            th1.stop();
            th2.stop();
        }
    }
}


// Output

AAAAAAAAAABBBBBBBBBB

```

```ad-question
title: Apartado b)

Crear el hilo implementando la interfaz `Runnable`. Comenta los resultados obtenidos al ejecutar esta método.
```

``` java

// runnableJava.java

public class runnableJava implements Runnable{
    private static int cont  = 10;
    private char word;

    public runnableJava(char word){
        this.word = word;
    } 

    public void run(){
        for(int i = 0; i<cont; i++){
            System.out.print(this.word);
        }
    }
}

//Main.java

public class Main{
    public static void main(String[] args){
        
        runnableJava r1 = new runnableJava('A');
        runnableJava r2 = new runnableJava('B');

        Thread th1 = new Thread(r1);
        Thread th2 = new Thread(r2);

        th1.start();
        th2.start();
    }
}


//Output

AAAAAAAAAABBBBBBBBBB
```

```ad-question
title: Apartado c)

Compara ambos métodos y determina cual consideras que es más adecuado y cuales son las razones que hacen un método más adecuado que el otro.
```

La primera implementación extiende la **clase Thread**, por el contrario la segunda implementación implementa la **interfaz Runnable**, ya de inicio es más limpio implementar una interfaz, pero además, **Runnable** permite que **cada instancia pueda crear varios hilos**, al contrario de **Thread**, que **solo puede generar un hilo por cada instancia**.

```ad-question
title: Apartado d)

Modifica el programa realizado en el apartado 2, para que el primer hilo creado en el main tenga prioridad 1. ¿Qué ocurre con la ejecución de los hilos? ¿Qué puedes decir acerca de la gestión de prioridades por parte de la máquina virtual de tu instalación de Java?
```

``` java
//Main.java

public class Main{
    public static void main(String[] args){
        
        runnableJava r1 = new runnableJava('A');
        runnableJava r2 = new runnableJava('B');

        Thread th1 = new Thread(r1);
        Thread th2 = new Thread(r2);

        th1.setPriority(1);

        th1.start();
        th2.start();
    }
}
```

En la ejecución del código no cambia nada, esto probablemente se deba a que los hilos se ejecutan muy rápido y no les da tiempo a que los dos hilos se ejecuten al mismo tiempo. También hay que tener en cuenta que la máquina virtual puede ser que afecte.

## Hilos en Python

```ad-info
title: Pregunta

Implementa en Python un programa que lance 5 hilos que se encarguen de actualizar una variable global compartida 50,000 veces cada uno. Si el programa funciona correctamente la variable (inicializada a 0) debería acabar valiendo 250,000. Ejecuta el programa varias veces y comenta los resultados que observes.
```

``` python

import threading

globalVariable = 0

def threadPython():

    global globalVariable

    for i in range (0,50000,1):

        globalVariable = globalVariable + 1

    print(globalVariable)

if __name__ == "__main__":

    for i in range (0,5,1):

        x = threading.Thread(target=threadPython, args=())

        x.start()

    print("End Main")
```

Siempre que se ejecuta el resultado final es 250000, lo único que cambia es que, alguna vez, el Main acaba antes que el último proceso.
También cabe destacar que todos los procesos acaban con el numero exacto de la variable, es decir: 50000, 100000, 150000, 200000, 250000.

## Hilos en Rust

```ad-question
title: Enunciado

Escribe un programa en [Rust](https://www.rust-lang.org) que haga exactamente lo mismo que en el caso anterior (python). Ejecuta el programa varias veces y comenta los resultados que observes.


Puedes instalar Rust desde el sistema de paquetería de tu SO o con la herramienta oficial [rustup](https://www.rust-lang.org/learn/get-started) del lenguaje.
```

``` rust

use std::thread;

static mut GLOBAL_VARIABLE: i32 = 0;

fn main(){
    let mut handles = vec![];
    for _i in 0..5{
        let handle = thread::spawn(||{
            unsafe{
                for _j in 0..50000{
                    GLOBAL_VARIABLE+=1;
                }
            }
            
        });
        handles.push(handle);
    }
    
    for handle in handles {
        handle.join().unwrap();
    }
    unsafe{
        println!("{}",GLOBAL_VARIABLE);
    }
}
```

Todos los resultados han sido obtenidos ejecutando el código en _RustPlayground_. 

La variable global solo obtiene el valor 250000 de vez en cuando, regularmente no se obtiene ese valor, sino uno inferior.