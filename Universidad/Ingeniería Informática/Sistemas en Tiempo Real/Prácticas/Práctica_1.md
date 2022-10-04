<center style="font-weight: bold; font-size: 25 ">Práctica 1</center>

```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
varied_style: true
```

### Progreso ###
- [x] Ejercicio 1
	- [x] a)
	- [x] b)
	- [x] c)
- [x] Ejercicio 2
	- [x] a)
	- [x] b)
	- [x] c)
- [x] Ejercicio 3
	- [x] a)
	- [x] b)
	- [x] c)
	- [x] d)
- [x] Ejercicio 4
	- [x] a)
	- [x] b)
	- [x] c)
	- [x] d)
- [x] Ejercicio 5
	- [x] a)
	- [x] b)
- [x] Ejercicio 6
	- [x] a)
	- [x] b)
	- [x] c)
- [x] Ejercicio 7
	- [x] a)
	- [x] b)
	- [x] c)
- [x] Ejercicio 8
	- [x] a)
	- [x] b)

### Ejercicio 1: Mi primer programa en Ada
>[!Enunciado]
>Utilizando el entorno GnatStudio, crea un proyecto denominado practica1 utilizando el tipo de  proyecto “Basic → Simple Ada Project”. 
>
>Fíjate que en la carpeta que indiques dónde se creará el  proyecto (opción “Location → Deploy project in”) se creará un fichero con extensión .gpr y las  carpetas /src y /obj.
>
>El nombre del fichero que contiene el programa principal se denominará  “ejercicio1.adb” y su contenido se detalla a continuación. Edita, compila, crea el ejecutable (build) y  ejecútalo:
>``` ADA
procedure ejercicio1 is  
s : String = “Comenzamos las prácticas de STR”;  
begin  
Ada.Text_Io.Put(“Hola Mundo!!! ”);  
Ada.Text_Io.Put_Line(s);  
end ejercicio1;
>```
>
#### a) Corrige los errores de compilación.

``` ADA
		with Ada.Text_Io;
		
		procedure ejercicio1 is
		   s : String := "Comenzamos las prácticas de STR";
		begin
		       Ada.Text_Io.Put("Hola Mundo!!!");
		       Ada.Text_Io.Put_Line(s);
		end ejercicio1;
```

#### b) Utiliza la cláusula use con el paquete Ada.Text_Io.

``` ADA
	with Ada.Text_Io; use Ada.Text_Io;

	procedure ejercicio1 is
	   s : String := "Comenzamos las prácticas de STR";
	begin
	       Put("Hola Mundo!!!");
	       Put_Line(s);
	end ejercicio1;
```

#### c) Piensa en una ventaja y un inconveniente del uso de la cláusula use, así como alguna recomendación de uso que se te ocurra.

La ventaja de usar la clausula 'use' es la posibilidad de ahorrar las llamadas a la librería incluida con el 'with', el inconveniente es que si dos paquetes utilizan un mismo comando creará conflicto. 


### Ejercicio 2: Mi primer paquete (package) en Ada

>[!Enunciado]
>Dentro del fichero ejercicio1.adb, añade el siguiente procedimiento denominado otroMensaje a continuación del procedimiento ejercicio1:
>``` ADA
procedure otroMensaje is  
begin  
Put_Line(“Vamos a iniciarnos en el lenguaje Ada”);  
end otroMensaje;
>```
>

#### a) ¿Por qué no compila ahora el fichero ejercicio1.adb?
No compila debido a que hay mas de un *procedure* en el fichero que contiene el archivo *main*.

#### b) Mueve el procedimiento anterior a un paquete denominado pkg_ejercicio2 que tendrás que crear dentro de tu proyecto
>[!Enunciado]
>Se debe crear desde la opción de menú File → New File. Los dos ficheros asociados al paquete (con extensiones .ads y .adb) deben guardarse en el directorio /src de vuestro proyecto. Puede ser necesario utilizar la opción File → Project → Reload Project (o directamente desde el botón correspondiente de la barra de herramientas) para que se actualice la vista de tu proyecto y se visualicen los nuevos ficheros añadidos.

``` ADA
-- -------pkg_ejercicio2.ads-------

with Ada.Text_Io; use Ada.Text_Io;
package pkg_ejercicio2 is

   procedure otroMensaje;
   
end pkg_ejercicio2;

-- -------pkg_ejercicio2.adb-------

package body pkg_ejercicio2 is
   
   procedure otroMensaje is
   begin
      Put_Line("Vamos a iniciarnos en el lenguaje Ada");
   end otroMensaje;

end pkg_ejercicio2;
```

#### c) Desde el programa principal ejercicio1 invoca al procedimiento 'otroMensaje'. Ejecútalo y comprueba su correcto funcionamiento.

``` ADA
-- -------ejercicio1.adb-------

with Ada.Text_Io; use Ada.Text_Io;
with pkg_ejercicio2;

procedure ejercicio1 is
   s : String := "Comenzamos las prácticas de STR";
begin
   Put("Hola Mundo!!!");
   Put_Line(s);
   pkg_ejercicio2.otroMensaje;

end ejercicio1;
```


### Ejercicio 3: Ámbito de declaraciones de tipos y variables. Paquetes predefinidos de I/O

#### a) Añade en el paquete pkg_ejercicio2 las siguientes declaraciones de tipos y variables :
``` ADA
-- Enunciado
type TdiasSemana is (Lunes, Martes, Miercoles, Jueves, Viernes, Sabado, Domingo);  
numAlumnos : Integer := 19;  
private  
notaMedia : Float := 6.12;
```

``` ADA
-- pkg_ejercicio2.ads

with Ada.Text_Io; use Ada.Text_Io;
package pkg_ejercicio2 is
   
   type TdiasSemana is (Lunes, Martes, Miercoles, Jueves, Viernes, Sabado, Domingo);  
   numAlumnos : Integer := 19; 

   procedure otroMensaje;
   
private
   
   notaMedia : Float := 6.12;
   
end pkg_ejercicio2;

```

#### b) Dentro del mismo proyecto practica1, añade un nuevo fichero denominado ejercicio3.adb que incluya un procedimiento denominado ejercicio3 inicialmente con un body vacío, es decir, un body con una única sentencia “null;”.

``` ADA
-- ejercicio3.adb

procedure ejercicio3 is
begin
   
   null;
   
end ejercicio3;

```

#### c) Añade desde el menú contextual del proyecto este nuevo fichero como otro programa principal de nuestro proyecto.
> [!Enunciado]
> Se realizará accediendo a Project->Properties->Main.
Observa que al tener en un mismo  
proyecto varios programas principales, desde los botones correspondientes nos aparecerá un desplegable (pulsar botón derecho del ratón) para hacer el build o el run del fichero que queremos que actúe como programa principal.

![[Pasted image 20220912165315.png]]

#### d) Añade en el body del procedimiento ejercicio3, las sentencias que permitan imprimir por pantalla el contenido de las dos variables declaradas en el paquete. 
>[!Enunciado]
>Para ello utiliza los paquetes predefinidos Ada.Integer_Text_IO y Ada.Float_Text_IO (Anexo A del Manual de Referencia de Ada (ARM): Contents → A.10.8, A.10.9, que se puede acceder desde la opción Help->GNAT->Ada 2012 Reference Manual).
> 
>Recuerda que también dispones del paquete Ada.Text_IO si necesitas imprimir mensajes de texto por pantalla. ¿Qué has hecho para acceder desde el procedimiento ejercicio3 a la variable privada declarada en el paquete que tenemos en nuestro proyecto, manteniendo su ámbito privado? Imprímela además con un sólo decimal.

``` ADA
-- -------pkg_ejercicio2.adb-------
package body pkg_ejercicio2 is
   
   procedure otroMensaje is
   begin
      Put_Line("Vamos a iniciarnos en el lenguaje Ada");
   end otroMensaje;
   
   function getNotaMedia return Float is
      begin
      return notaMedia;
   end getNotaMedia;

end pkg_ejercicio2;

-- -------ejercicio3.adb-------
with Ada.Text_Io; use Ada.Text_Io;
with Ada.Integer_Text_Io;
with Ada.Float_Text_Io;
with pkg_ejercicio2; use pkg_ejercicio2;

procedure ejercicio3 is
     
begin
   
   Ada.Integer_Text_IO.Put(numAlumnos);
   Put_Line("");
   
   Ada.Float_Text_IO.Put(getNotaMedia, Aft => 1, Exp => 0);
   Put_Line("");
     
end ejercicio3;

```


### Ejercicio 4: Sentencias de selección y bucles. Paquetes genéricos predefinidos.

#### a) Añade en tu programa principal ejercicio1 la lectura por pantalla de un número de tipo Natural que representará un mes 
>[!Enunciado]
>Debes usar Ada.Integer_Text_IO ya que Natural es un subtipo de Integer.  
>
>Después, y utilizando la sentencia case, se debe imprimir por pantalla el nombre de la estación del año correspondiente, teniendo en cuenta que los meses 1, 2 y 12 son invierno; 3, 4 y 5 primavera; 6, 7 y 8 verano; 9, 10 y 11 otoño. Si el número de mes no es correcto, se imprimirá “Mes incorrecto”

``` ADA
-- ejercicio1.adb

with Ada.Text_Io; use Ada.Text_Io;
with Ada.Integer_Text_IO;
with pkg_ejercicio2;

procedure ejercicio1 is
   s : String := "Comenzamos las prácticas de STR";
   n : Natural;
begin
   Put("Hola Mundo!!!");
   Put_Line(s);
   pkg_ejercicio2.otroMensaje;
   
   Put_Line("Escribe un número del Mes.");
   Put(">");
   Ada.Integer_Text_IO.Get(n);
   
   case n is
      when 1|2|12 => Put_Line("Invierno");
      when 3..5 => Put_Line("Primavera");
      when 6..8 => Put_Line("Verano");
      when 9..11 => Put_Line("Otoño");
      when others => Put_Line("Mes Incorrecto");
   end case;

end ejercicio1;
```

#### b) Añade en tu programa principal ejercicio3 el siguiente bucle for que recorre los valores del tipo  enumerado TdiasSemana y los imprime por pantalla. 
``` ADA
for dia in TdiasSemana loop  
Put(dia);  
Ada.Text_IO.New_Line;  
end loop;
```
>[!Enunciado]
>Si sólo incluyes este código, el programa no compila ¿Por qué?. 
>
>Para corregir el error, ten en cuenta que para imprimir valores de un tipo enumerado necesitas una instancia del paquete genérico Ada.Text_IO.Enumeration_IO (Anexo A.10.10 del ARM). 
>
>¿Por qué no hay que declarar la variable día?


El código inicial no compila porque no hay un interprete para poder mostrar el resultado del *Put*.

``` ADA
-- ejercicio3.adb

with Ada.Text_Io; use Ada.Text_Io;
with Ada.Integer_Text_Io;
with Ada.Float_Text_Io;
with pkg_ejercicio2; use pkg_ejercicio2;

procedure ejercicio3 is
   
   package enumPrint is new Ada.Text_IO.Enumeration_IO(TdiasSemana);
   
begin
   
   Ada.Integer_Text_IO.Put(numAlumnos);
   Put_Line("");
   
   Ada.Float_Text_IO.Put(getNotaMedia, Aft => 1, Exp => 0);
   Put_Line("");
   
   Put_Line("Dias de la Semana");
      
   for dia in TdiasSemana loop
      enumPrint.Put(dia);
      Ada.Text_IO.New_Line;  
   end loop;
   
end ejercicio3;
```

No hace falta declararla debido a que la clase día es el producto de un enumerado, debido a que usamos el paquete de Enumeration_IO este es capaz de obtener los contenidos del enum.

#### c) A continuación del bucle for anterior, escribe las sentencias que permitan imprimir un mensaje en  pantalla solicitando que se introduzca por teclado un día cualquiera
>[!Enunciado]
>El dia se guardará en una variable y se imprimirá por pantalla un mensaje indicando si hay o no clases de la asignatura en ese día. Por ejemplo, si se introduce Martes, se imprimirá el mensaje “El Martes no hay clases de STR”

``` ADA
-- ejercicio3.adb
with Ada.Text_Io; use Ada.Text_Io;
with Ada.Integer_Text_Io;
with Ada.Float_Text_Io;
with pkg_ejercicio2; use pkg_ejercicio2;

procedure ejercicio3 is
   
   day: Integer;
   package enumPrint is new Ada.Text_IO.Enumeration_IO(TdiasSemana);
   
begin
   
   Ada.Integer_Text_IO.Put(numAlumnos);
   Put_Line("");
   
   Ada.Float_Text_IO.Put(getNotaMedia, Aft => 1, Exp => 0);
   Put_Line("");
   
   Put_Line("Dias de la Semana");
      
   for dia in TdiasSemana loop
      enumPrint.Put(dia);
      Ada.Text_IO.New_Line;  
   end loop;
   
   Put_Line("Escribe un número del 1 al 7 que represente el dia de la semana.");
   Put("> ");
   Ada.Integer_Text_IO.Get(day);
   day := day - 1;
   
   Ada.Text_IO.New_Line; 
 
   if(day <= 6 and day >= 0) then
      Put("El ");
      enumPrint.Put(TdiasSemana'Val(day));
      
      if(day = 1) then
         Put_Line(" hay clase de STR.");
      else
         Put_Line(" no hay clase de STR.");
      end if;
   else
      Put_Line("No se ha introducido un número válido.");
   end if;
end ejercicio3;
```

#### d) Comprueba qué ocurre si no se introduce un valor que pertenece a este tipo enumerado.

Que nos soltará esta excepción: 

ADA.IO_EXCEPTIONS.DATA_ERROR : a-tiinio.adb:104 instantiated at a-inteio.ads:18

Esto nos sucede debido a que elegir una dirección del enumerado se necesita o un Integer o el valor de la posición del enumerado.

### Ejercicio 5: Bloques y manejo de excepciones
#### a) Ejecuta el programa ejercicio1 e introduce un número de mes negativo. ¿Qué ocurre? ¿Por qué?

Al introducir un número negativo  suelta este error: 
CONSTRAINT_ERROR : ejercicio1.adb:15 ==range check failed==

Esto se debe a que al analizar el número negativo da un fallo en la comprobación de los rangos.
Esto es debido a que la variable ==es de tipo natural==.

>[!Enunciado]
>Modifica el código para que en ese caso se muestre por pantalla el mensaje “El número de mes debe ser > 0”

``` ADA
-- ejercicio1.adb
with Ada.Text_Io; use Ada.Text_Io;
with Ada.Integer_Text_IO;
with pkg_ejercicio2;

procedure ejercicio1 is
   s : String := "Comenzamos las prácticas de STR";
   n : Natural;
begin
   Put("Hola Mundo!!!");
   Put_Line(s);
   pkg_ejercicio2.otroMensaje;
   
   Put_Line("Escribe un número del Mes.");
   Put(">");
   Ada.Integer_Text_IO.Get(n);
   
   case n is
      when 1|2|12 => Put_Line("Invierno");
      when 3..5 => Put_Line("Primavera");
      when 6..8 => Put_Line("Verano");
      when 9..11 => Put_Line("Otoño");
      when others => Put_Line("Mes Incorrecto");
   end case;
   
exception
   when event: others => Put_Line("El número del mes debe ser mayor que 0.");

end ejercicio1;
```

#### b) Añade en el programa ejercicio1 la sentencia put_line(“FIN DEL PROGRAMA”); 

>[!Enunciado]
>Esta sentencia debe colocarse de forma que sea el último mensaje que aparezca por pantalla independientemente de que el número de mes introducido sea negativo o positivo. 
>
>Dicha sentencia solo debe aparecer una vez en tu código.

``` ADA
-- ejercicio1.adb

with Ada.Text_Io; use Ada.Text_Io;
with Ada.Integer_Text_IO;
with pkg_ejercicio2;

procedure ejercicio1 is
   s : String := "Comenzamos las prácticas de STR";
   n : Natural;
begin
   Put("Hola Mundo!!!");
   Put_Line(s);
   pkg_ejercicio2.otroMensaje;
   
   Put_Line("Escribe un número del Mes.");
   Put(">");
   begin
      Ada.Integer_Text_IO.Get(n);
   
      case n is
      when 1|2|12 => Put_Line("Invierno");
      when 3..5 => Put_Line("Primavera");
      when 6..8 => Put_Line("Verano");
      when 9..11 => Put_Line("Otoño");
      when others => Put_Line("Mes Incorrecto");
      end case;
      
   exception
      when event: others => Put_Line("El número del mes debe ser mayor que 0.");
   end;
   
   Put_Line("FIN DEL PROGRAMA");
   
end ejercicio1;
```


### Ejercicio 6. Generación de números aleatorios
>[!Enunciado]
>Para generar números aleatorios de tipo discreto, Ada dispone del paquete genérico Ada.Numerics.Discrete_Random (Anexo A.5.2 del ARM).  
>
>En el siguiente programa tienes un ejemplo de instanciación y uso de dicho paquete
>``` ADA
>with Ada.Numerics.Discrete_Random; -- paquete genérico predefinido  
>with Ada.Integer_Text_IO; USE Ada.Integer_Text_IO;  
>with Ada.Text_IO; use Ada.Text_IO; 
>procedure ejercicio6 is  
>	subtype T_Digito is Integer range 0..9;  
>	-- crear instancia del paquete genérico predefinido  
>	package Pkg_DigitoAleatorio is new Ada.Numerics.Discrete_Random (T_Digito);  
>		generador_digito : pkg_DigitoAleatorio.Generator; -- declarar generador de valores aleatorios tipo T_Digito  
>	digito : T_Digito;  
>begin  
>	pkg_DigitoAleatorio.Reset (Generador_Digito); -- Inicializa generador números aleatorios  
>	loop  
>		digito := pkg_DigitoAleatorio.Random(generador_digito); -- generar número aleatorio  
>		Put(digito);  
>		skip_line;  
>	end loop;  
>end ejercicio6;
>```

#### a) ¿Qué hace este programa? 
>[!Enunciado]
>Inclúyelo en tu proyecto como otro programa principal denominado ejercicio6 y verifica tu respuesta ejecutándolo. 
>
>Puedes interrumpir su ejecución desde la opción Run main, que aparece en la barra de herramientas cuando un programa se está ejecutando

El programa genera un número aleatorio y se espera a que se presione la tecla '_Enter_' para repetir la generación del número aleatorio.

El programa es un bucle infinito, por lo que no termina.

#### b) En la sentencia Put(digito), ¿qué paquete se está utilizando? Justifica tu respuesta.

Se esta utilizando el paquete ''_Ada.Integer_Text_IO_" ya que se requiere que se saque por pantalla un Integer.

#### c) Implementa un nuevo programa principal en tu proyecto denominado numerosAleatorios que genere números reales entre 0.0 y 1.0
>[!Enunciado]
>Utiliza el paquete (no genérico) predefinido Ada.Numerics.Float_Random (Anexo A.5.2 del ARM). 
>
>Los números de deben mostrar en pantalla con 4 decimales.

``` ADA
with Ada.Numerics.Float_Random;
with Ada.Float_Text_Io; use Ada.Float_Text_Io;
with Ada.Text_IO; use Ada.Text_IO; 

procedure numerosaleatorios is  
	subtype T_Digito is Float range 0.0..1.0;  
	
   generador_digito : Ada.Numerics.Float_Random.Generator;  
   
   digito : T_Digito;  
   
begin  
   Ada.Numerics.Float_Random.Reset (Generador_Digito); 
   
	loop  
		digito := Ada.Numerics.Float_Random.Random(generador_digito); 
		Put(digito, Aft => 4, Exp => 0);  
		skip_line;  
   end loop;  
   
end numerosaleatorios;
```

### Ejercicio 7. Introducción a tareas concurrentes creadas de forma ESTÁTICA

>[!Enunciado]
>Para empezar a familiarizarnos con la programación concurrente en Ada, añade a tu proyecto el siguiente programa principal main_tareas_estaticas y el paquete pkg_tareas_estaticas:
>``` ADA
>-- Main
>with pkg_tareas_estaticas; use pkg_tareas_estaticas;  
>procedure main_tareas_estaticas is  
>tarea1 : T_Tarea(0,9);  
>tarea2 : T_Tarea(10,99);  
>tarea3 : T_Tarea(100,999);  
>begin  
>null;  
>end main_tareas_estaticas;
>
>-- Package.ads
>package pkg_tareas_estaticas is  
>task type T_Tarea (a, b : integer);  
>end pkg_tareas_estaticas;
>
>-- Package.adb
>with Ada.Numerics.Discrete_Random;  
>with Ada.Integer_Text_IO; use Ada.Integer_Text_IO;  
>with Ada.Text_IO; use Ada.Text_IO;  
>
>package body pkg_tareas_estaticas is  
>task body T_Tarea is  
>	subtype T_Num is Integer range a..b;  
>	package pkg_NumAleatorio is new Ada.Numerics.Discrete_Random (T_Num);  
>	use pkg_NumAleatorio;  
>	generador_num : Generator;  
>	num : T_Num;  
>
>begin  
>	Reset (generador_num);  
>	for i in 1..20 loop  
>		num:= Random(generador_num);  
>		Put(num);  
>		New_Line;  
>		delay(0.01); -- La tarea queda suspendida 0.01 segundos  
>	end loop;  
>end T_Tarea;  
>begin  
>	put_line(“Sentencias de inicialización del paquete”);  
>end pkg_ tareas_estaticas;

#### a) Observa que el programa principal sólo contiene la sentencia vacía “null;” e intenta comprender qué hace este programa antes de ver el resultado de su ejecución.

El programa principal genera tres tareas del Tipo '_T_Tarea_'. Aunque lo que tenga el main sea un 'null' las tareas se ejecutaran al declararse.

Cada tarea generará 20 números aleatorios entre los rangos asignados en la generación de estas tareas.

Las tres tareas generaran los números sin sincronizarse entre ellas.

#### b) Ejecuta de nuevo el programa pero comentando la sentencia delay, y reflexiona sobre la diferencia en el resultado si dicha sentencia está o no comentada.

Si se ejecuta con el delay, los outputs de las tareas se muestran de manera intercalada.

Al quitar el delay y sabiendo que las tareas son tan pequeñas, cuando la tarea se ejecuta, esta termina antes de que la siguiente empiece, por lo que se muestran todos los outputs de la tarea de manera seguida antes de que la siguiente pueda empezar.

#### c) En el body de cualquier paquete existe una parte opcional al final del mismo que comienza con la palabra reservada begin, y en la que se pueden incluir sentencias. ¿Qué tipo de sentencias crees que pueden ser convenientes utilizar? ¿Cuándo se ejecutan estas sentencias?

El begin del final del paquete es el begin asignado al archivo en si. El begin del inicio es el asignado al tipo T_Tarea.

El begin del paquete se ejecuta antes que las tareas, por lo que este se podría utilizar como enlace para las tareas o para la sincronización entre ellas.

### Ejercicio 8: Introducción a tareas concurrentes creadas de forma DINÁMICA

>[!Enunciado]
>Como ejemplo de creación de tareas de forma dinámica, copia el siguiente paquete en el proyecto de esta práctica, cuya especificación es la siguiente:
>
>``` ADA
>package pkg_tareas_dinamicas is  
>
>	MIN_VELOCIDAD_AVION : CONSTANT := -10;  
>	MAX_VELOCIDAD_AVION : CONSTANT := 10;  
>	VELOCIDAD_VUELO : CONSTANT := 5;  
>	NUM_INICIAL_AVIONES_AEROVIA : CONSTANT := 3;  
>	NUM_AEREOVIAS : CONSTANT := 6;  
>	type T_RangoVelocidad is new integer range MIN_VELOCIDAD_AVION..MAX_VELOCIDAD_AVION;  
>	-- identificador numérico de cada avión  
>	type T_IdAvion is mod NUM_INICIAL_AVIONES_AEROVIA;  
>	-- rango del número de aereovias de distintas altitudes  
>	type T_Rango_AereoVia is new integer range 1..NUM_AEREOVIAS;  
>	-- velocidad de los aviones en los ejes X e Y  
>	
>	type T_Velocidad is  
>	record  
>		X : T_RangoVelocidad;  
>		Y : T_RangoVelocidad;  
>	end record;  
>	-- tipo registro para almacenar los datos de un avión  
>	
>	type T_RecordAvion is  
>	record  
>		id : T_IdAvion; -- identificador del avion  
>		velocidad : T_Velocidad;  
>		aereovia : T_Rango_AereoVia;  
>		tren_aterrizaje : Boolean;  
>	end record;  
>	
>	type Ptr_T_RecordAvion is access T_RecordAvion;  
>	TASK TareaGeneraAviones;  
>	-- Tipo tarea encargada del comportamiento de un avion  
>	TASK TYPE T_TareaAvion(ptr_avion : Ptr_T_RecordAvion);  
>	type Ptr_TareaAvion is access T_TareaAvion;  
>end pkg_tareas_dinamicas;
>``` 
>Como puedes comprobar, en este paquete se han declarado constantes, tipos de datos, una tarea estática y un tipo tarea que usaremos para crear instancias dinámicas de dicho tipo.
>
>El body del paquete es el siguiente:
>``` ADA
>with Ada.Text_IO; use Ada.Text_IO;  
>
>package body pkg_tareas_dinamicas is  
>
>	task body TareaGeneraAviones is  
>	tarea_avion : Ptr_TareaAvion;  
>	ptr_avion : Ptr_T_RecordAvion;  
>	
>begin  
>	for id in T_IdAvion loop  
>		for aereovia in T_Rango_AereoVia'First..T_Rango_AereoVia'Last - 2 loop  
>			-- inicializar datos de un nuevo avion  
>			ptr_avion := new T_RecordAvion;  
>			ptr_avion.id := id;  
>			ptr_avion.velocidad.x := VELOCIDAD_VUELO;  
>			ptr_avion.velocidad.y := 0;  
>			ptr_avion.aereovia := aereovia;  
>			ptr_avion.tren_aterrizaje := False;  
>			-- Crear una tarea para el comportamiento del avion  
>			tarea_avion := NEW T_TareaAvion(ptr_avion);  
>		end loop;  
>	end loop;  
>end TareaGeneraAviones;  
>
>TASK BODY T_TareaAvion IS  
>begin  
>	Put_line("TASK Avion: " & T_IdAvion'Image(ptr_avion.id) & " -"  
>	& T_Rango_AereoVia'Image(ptr_avion.aereovia));  
>	end T_TareaAvion;  
>end pkg_tareas_dinamicas
>```

#### a) Crea un programa principal llamado main_tareas_dinamicas que permita ejecutar las tareas declaradas en este paquete.

Para poder ejecutar todas las tareas se tendría que hacer un main de la siguiente manera:

``` ADA
with pkg_tareas_dinamicas;

procedure main_tareas_dinamicas is
      
begin
   
   null;
   
end main_tareas_dinamicas;
```

Lo único que hace falta para ejecutar las tareas es llamar al paquete en el main para que se ejecuten las tareas (Explico el porque en el apartado 'b)').

#### b)Entiende el código proporcionado en este paquete, ya que te servirá de ayuda para implementar la siguiente práctica.

Para entender como funciona el código primero tenemos que ver que tareas se crean en el paquete.

Se declaran dos tareas:

- Una tarea anónima llamada '_TareaGeneraAviones_'.
- Una tarea no anónima llamada '_T_TareaAvion_'

La tarea anónima se ejecutará al inicio del programa, ya que esta no puede ser llamada, y ejecutará su body.

El body de la tarea anónima tiene dos funciones, inicializar variables y crear de manera dinámica a la tarea '_T_TareaAvion_'. Esta tarea tiene un bucle, por lo que realizará varias veces esta función. 

De esta manera también se ejecutará después de que la tarea anónima se ejecute.

Por último la '_T_TareaAvion_' tiene como función realizar outputs de los resultados de la tarea anónima.

En cuanto al número de tareas ejecutadas tendriamos la tarea main, la tarea anónima y 
```T_Rango_AereoVia'First..T_Rango_AereoVia'Last - 2``` tareas de tipo '_T_TareaAvion_'.