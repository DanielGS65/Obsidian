<center style="font-weight: bold; font-size: 25 "> Introducción al lenguaje de programación ADA</center>

[[Universidad/Ingeniería Informática/Sistemas en Tiempo Real/Prácticas/Práctica_1|Ir a la práctica]] 

```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
```



### Origen y características generales

Es un lenguaje hecho para el desarrollo de sistemas en tiempo real, empotrados, de control y de misión crítica[^1].

> [!Caracteristicas]
> Fue el primer lenguaje creado para este tipo de sistemas.
> 
> Esta diseñado para los sistemas de tiempo real.
> - Procesamiento concurrente
> - Manejo del tiempo
> - Acceso al hardware y interrupciones
> 
> Su sintaxis es descendiente de Pascal.
> - Estructuras en bloques 
> - Fuertemente tipado.
> 
> Esta pensado para construir sistemas grandes y cambiantes.
> - Paquetes y unidades genéricas
> - Interfaces normalizadas con otros lenguajes (C y Fortran)

### Entorno de trabajo ###

Se utilizará el **IDE: GnatStudio** , además, también se utilizara **GtkAda**, una librería grafica para el IDE, se usará para alguna práctica[^2].

Los comandos del lenguaje ADA: [[ADA Cheatsheet]].

### Estructura de un programa en ADA 

Las unidades de programa del lenguaje ADA son:

- **Funciones**: Equivalentes a las funciones de C, es obligatorio que tenga un return.
- **Procedimientos**: Equivalente a las funciones, el cambio que tienen es que estos no pueden tener returns.
-  **Paquete**: Equivalente a una librería.
- **Unidades Genéricas**

> [!Diferencias entre funciones y procedimientos]
> Una función solo puede devolver un valor, por lo que no se pueden usar parámetros de salida o entrada-salida.
> 
> Para usar estos parámetros se deberá usar un procedimiento.
> 
> ==Las funciones tienen que tener un return==

Las adiciones que se realiza en el lenguaje ADA serian las **Tareas**, que se podrían describir como 'Threads' y las **Unidades protegidas**[^3]

Un Programa de ADA estará formado por uno o mas Unidades de programa.

- El procedimiento principal
- Otros procedimientos que se pueden encapsular en paquetes, como procedimientos propios o de librerías.

Para usar los procedimientos de las librerías tenemos dos opciones:
- Notar la librería con un punto -> librería.procedimiento.
- Incluir la notación 'use' en la libreria[^4].

En ADA hay dos tipos de extensión:

- .**adb**: Equivalente a los .c
- .**ads**: Equivalente a los .h

Abra siempre un fichero que tenga un procedimiento sin argumentos que tenga el procedimiento principal del programa, lo que sería el Main.

La compilación se puede compilar por separado (por paquetes)

Los parámetros pueden tener tres modos:

- **in**: Parámetro de entrada.
- **out**: Valor de salida.
- **in out**: valores de entrada y salida.


### Sintaxis ###

No hay distinción de Mayúsculas y minúsculas.

El primer carácter debe ser una letra.

Se puede usar el carácter '`_`'.

Los comentarios son  de esta forma '--'

Los operadores serian: =, /=

La asignación es :=

Los comparadores son: ==or, and, not, xor, or else, and then==[^5]

### Tipos de Datos ###

No se permiten las asignaciones entre tipos de datos distintos.

Se pueden realizar conversiones forzadas.

Se puede establecer la precisión de algunos tipos con '*range*'.


### Unidades Genéricas

Son unidades destinadas a ser reutilizadas pudiendo ser modificadas en la implementación cambiando su tipo de Dato de entrada.

### Concurrencia en ADA

En ADA tenemos un tipo denominado ==*task*== que es un tipo específicamente usado en concurrencia.

Se pueden declarar en los Packages.

Se intentará declarar siempre en packetes, aunque no sea necesario.

Se pueden declarar asignándole un type, esto se realiza para poder crear varias instancias de esa tarea. También se puede declarar de manera anónima si no se requiere varias instancias de la tarea.

Se pueden asignar parámetros a las tareas con type para poder cambiar el comportamiento de dicha tarea y que se pueda reutilizar por otras instancias.

Las tareas declaradas empezaran su proceso de declaración cuando empiece el begin del proceso padre. Cuando estas empiezan su declaración el proceso padre se queda en suspensión[^6].

Hasta que todas las tareas no terminan el programa no terminara y el padre se quedará en suspensión[^7].

También se puede generar tarea dinámica en un begin usando punteros de ADA, el padre no tendrá que esperarse a su activación




## Aclaraciones ##

[^1]: Fue solicitado por el departamento de defensa de los EEUU para uso de sistemas de aviación y de armamento.

[^2]: Se usará una máquina virtual de Ubuntu, Contraseña: str.

[^3]: Es necesario el uso de las Unidades protegidas ya que se podría estar utilizando memoria compartida con el riesgo de que se acceda al mismo tiempo a esa zona de memoria.

[^4]: Al usar la clausula 'use' no se necesitará poner el nombre de la librería para usar sus funciones, no obstante, si se usa una función local con el mismo nombre creará conflicto.

[^5]: Los comparadores **and** y **or** son sin cortocircuito (Comprobará todas las entradas), para que tenga cortocircuito se utiliza en **or else** y en **and then**.

[^6]: Esto sucede para que el padre pueda recoger las excepciones de las tareas hijas cuando estas se están declarando.

[^7]: El proceso padre termina el último por si es necesario que los procesos hijos accedan a su espacio local.
