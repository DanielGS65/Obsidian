<center style="font-weight: bold; font-size: 25 ">Cuestiones Teóricas : Tema 3</center>

```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
varied_style: true
```

## Cuestiones Breves

```ad-question
title: Enunciado
color: 180,20,180

Indicar las condiciones exigidas por los protocolos de E/S a una correcta solución al problema de la exclusión mutua
```

La exclusión mutua en la informática se refiere a la propiedad que se debería cumplir en la programación concurrente para permitir el acceso compartido a un objeto de manera que en la ejecución de los procesos no difiera el valor de la variable a acceder.

Para garantizar la exclusión mutua se necesita proporcionar un sistema de Entrada/Salida que nos permita cumplir ciertos requisitos.

```ad-info
color: 255, 170, 0
title: Requisitos de Entrada/Salida
Los protocolos de E/S en la _sección crítica_ deben cumplimentar:
- Sólo un proceso puede estar en un determinado momento en su sección crítica
- Cuando un proceso sale de la sección crítica debe dar el acceso a otro proceso que estaba esperando para acceder a la sección crítica.
- Si un proceso quiera entrar en su _sección crítica_ y ésta sección esté libre, debe poder hacerlo.
```

-------------------------

```ad-question
title: Enunciado
color: 180,20,180

Formular varios problemas donde se requieran condiciones de sincronización
```

Algunos ejemplos que necesitan de condiciones de sincronización serian los siguientes:

```ad-info
title: Ejemplos
color: 255, 170, 0

- La edición cooperativa de documentos en línea, ya que cuando mas de un usuario edita un mismo documento al mismo tiempo se requiere de una sincronización entre los cambios y el documento.
- En transferencias en la cual se tiene que modificar un valor cuando este puede ser solicitado mientras se modifica.
- En bases de datos cuando se modifican valores si estos están siendo accedidos en ese momento.
```

---------------------------------------

```ad-question
title: Enunciado
color: 180, 20, 180

¿A qué hace referencia el término _**espera ocupada**_?
```

Es una de las **Soluciones a la Sincronización basada en Memoria Compartida** 

```ad-info
title:Funcionamiento
color: 255,170,0

- El proceso espera mediante la comprobación continua del valor de una variable compartida, manteniendo ocupada la CPU.
- Puede tener dos tipos de soluciones:
	- **Basadas en hardware:** permiten operaciones atómicas más complejas
	- **Basadas en software:** las únicas operaciones atómicas son '_load_' y '_store_' para leer y escribir en direcciones de memoria. No obstante, si dos de estas instrucciones coinciden simultáneamente sería equivalente a una ejecución secuencial.
```

------------------------------------------

```ad-question
title: Enunciado
color: 180,20,180

¿Cuál es el principal inconveniente de las soluciones basadas en espera ocupada?
```

La espera ocupada si que cumple con la propiedad de exclusión mutua, no obstante, acarrea algunos problemas.

```ad-info
title: Problemas de la espera ocupada
color: 255,170,0

- Los procesos ocupan el procesador incluso aunque no puedan avanzar.
- El uso de _Variables Compartidas_ genera esquemas complejos que tienden a tener errores y estar fuertemente acoplados, además de ser inadecuados para esquemas de protección.
```

------------------------------------

```ad-question
title: Enunciado
color: 180, 20, 180

Explicar por qué el algoritmo de Lamport no presenta espera ilimitada.
```

El algoritmo de Lamport cumple con la propiedad de exclusión mutua, no obstante tiene el inconveniente de no presentar **_Espera Ilimitada_**.

```ad-info
title: Espera no Ilimitada en el Algoritmo de Lamport
color: 255, 170, 0

Los números que cogen los procesos pueden aumentar a lo largo de la ejecución, pudiendo llegar a superar la capacidad total de los tipos de datos.
```

## Problemas

```ad-question
title: Enunciado
color: 0,100,180

Discutir la corrección del algoritmo de exclusión mutua siguiente (si es incorrecto encuentra una traza que viole la exclusión mutua, si es correcto pruébalo):

``` python
states = [1, 1]
states[0] = 1 - states[1]
while states[1] != 0:
	states[0] = 1 - states[1]
critical_section()
states[0] = 1
```


Este código es incorrecto debido a que el bucle _While_ siempre se estará ejecutando ya que `states[1]` no se modifica y siempre es `!= 0`.

```ad-question
color:  0,100,180
title: Enunciado
Realiza la misma tarea con este código
``` python
 states = [1, 1]
 
 states[0] = 0
 while states[1] == 0:
	 states[0] = 1
	 while states[1] == 0:
		 pass
	 states[0] = 0
 
critical_section()
states[0] = 1
```

En este código `states[1]` nunca es 0, por lo que nunca entrará en el _while_

```ad-question
title: Enunciado
color:  0,100,180

Adapta al código del programa `counter.py` del _tema 2_ de modo que satisfaga la exclusión mutua en el acceso a la variable compartida `counter` mediante el algoritmo de Peterson

```python
import threading

THREADS = 2
MAX_COUNT = 10000000
counter = 0 

def thread():
	global counter
	print("Thread {}".format(threading.current_thread().name))
	
	for i in range(MAX_COUNT//THREADS):
		counter += 1

def main():
	threads = []

	print("Starting...")
 
	for i in range(THREADS):
		# Create new threads
		t = threading.Thread(target=thread)
		threads.append(t)
		t.start() # start the thread
 
		# Wait for all threads to complete
		for t in threads:
			t.join()
 
		print("Counter value: {} Expected: {}\n".format(counter, MAX_COUNT))

if __name__ == "__main__":
	main()
```

``` python
import threading

THREADS = 2
MAX_COUNT = 10000000
counter = 0

def thread(id):
    global counter
    print("Thread {}".format(threading.current_thread().name))

    if id == 0:
        neg_id = 1
    else:
        neg_id = 0

    for i in range(MAX_COUNT//THREADS):
        cs_ready = [False, False]
        turn = neg_id
        while cs_ready[neg_id] and turn == neg_id:
            pass
        counter += 1
        cs_ready[id] = False

def main():
    threads = []
    print("Starting...")

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