<center style="font-weight: bold; font-size: 25 ">Práctica 2</center>

```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
varied_style: true
```

<p style="font-weight: bold; font-size: 20 ">Progreso</p>

- [x] Visualizar el entorno estático simulado
- [x] Creación de aviones entorno estático simulado
- [x] Vuelo de aviones


## Visualizar el entorno estático simulado

Para poder visualizar el entorno grafico del sistema solo es necesario poner la siguiente sentencia en el Main.

``` ADA
with PKG_graficos;

procedure Main is  

begin
   pkg_graficos.Simular_Sistema;
end Main;
```

## Creación de aviones entorno estático simulado

Para la creación de aviones se ha construido un nuevo paquete, el paquete 'pkg_planegenerator'.

``` ADA
-- pkg_planegenerator.ads

with pkg_TareaAvion; use pkg_TareaAvion;
with Ada.Numerics.Discrete_Random;
with Ada.Integer_Text_IO; USE Ada.Integer_Text_IO;  
with Ada.Text_IO; use Ada.Text_IO; 
with PKG_tipos; use pkg_Tipos;
with PKG_debug; use PKG_debug;
with Ada.Exceptions;  use Ada.Exceptions;
with PKG_graficos; use PKG_graficos;

package pkg_planeGenerator is
   
   procedure generateDelay;
   
   procedure createPlane(id: in T_IdAvion; aerovia : in T_Rango_AereoVia);
   
   TASK TYPE T_GeneraAviones;  
   
end pkg_planeGenerator;
```

Este paquete será el encargado de la generación de aviones. Para generarlos vamos a tener que hacer tres apartados:

### Creación de aviones

Para la creación de aviones se ha creado una procedure que sea capaz de generar aviones, esta cogerá por parámetro los valores de ID y Aerovía del avión, en este caso, el color se elige de manera aleatoria obteniendo el rango del tipo 'T_ColorAparicionAvion' y la pista del avion siempre será el valor 0 (SIN PISTA).

Al generar la información del avión se creara una nueva tarea de tipo T_Avion (después entraré mas en detalle).

``` ADA
-- pkg_planegenerator.adb

procedure createPlane (id: in T_IdAvion; aerovia: in T_Rango_AereoVia) is
      
      ptr_avion : Ptr_T_RecordAvion;
      
      tarea_avion : Ptr_T_Avion;
      
      type T_Color_Range is new Integer range 0 .. T_ColorAparicionAvion'Range_Length - 1;
      package pkg_randomColor is new Ada.Numerics.Discrete_Random(T_Color_Range);
      random_color : pkg_randomColor.generator;
        
   begin
         
      pkg_randomColor.Reset(random_color);
      
      ptr_avion := new T_RecordAvion;  
      ptr_avion.id := id;
      if(aerovia mod 2 = 0) then
         ptr_avion.velocidad.x := -VELOCIDAD_VUELO;
      else
         ptr_avion.velocidad.x := VELOCIDAD_VUELO;
      end if;
      ptr_avion.velocidad.y := 0; 
      ptr_avion.pos := Pos_Inicio(aerovia);
      
      ptr_avion.aereovia := T_Rango_AereoVia'val(aerovia);
      ptr_avion.aereovia_inicial := T_Rango_AereoVia'val(aerovia);
      
      ptr_avion.pista := T_PistaAterrizaje'val(0);
      ptr_avion.color := T_ColorAparicionAvion'val(pkg_randomColor.Random(random_color));
      ptr_avion.tren_aterrizaje := False;
      
      tarea_avion := new T_Avion(ptr_avion);
   
   end createPlane;
```

### Generación del Delay

Para la generación del Delay solo es necesario generar un número aleatorio dentro del rango asignado y realizar una conversión forzada al tipo 'Duration'.

``` ADA
-- pkg_planegeneration

 procedure generateDelay is
      
      package pkg_random is new Ada.Numerics.Discrete_Random(T_RetardoAparicionAviones);
      int_delay : T_RetardoAparicionAviones;
      random_num : pkg_random.generator;
      
   begin
      
      pkg_random.reset(random_num);
      
      int_delay := pkg_random.Random(random_num);
      Put_Line("Start Delay of:");
      Put(int_delay);
      Put_Line("");
      
      delay (Duration(int_delay));
   end generateDelay;
```

### Ejecución de los dos procedimientos

Para ejecutar los dos procedimientos al mismo tiempo se realizará una tarea que ejecute estos dos procedimientos, aparte de generar las id y las aerovías de cada uno. Esto no se hace en el main ya que este esta ejecutando el entorno gráfico.

En esta tarea también se colocará un manejador de excepciones.

``` ADA
-- pkg_planegenerator

task body T_GeneraAviones is
      id : T_IdAvion := 0;
      aerovia : T_Rango_AereoVia := 1;
      limit : Integer := 4;
      cont : Integer := 0;
      stop : Boolean := false;
      
   begin
      
      loop 
         if(cont < 3) then
            createPlane(id,aerovia);
            generateDelay;
            cont := cont + 1;
            id := id + 1;
         else
            id := 0;
            cont := 0;
            if(aerovia < NUM_AEREOVIAS) then
               aerovia := aerovia + 1;
            else
               stop := true;
            end if;
         end if;
         exit when(stop);
      end loop;
      Put_Line("Stoped Plane Generation");
   exception
      when event: others =>
         PKG_debug.Escribir("ERROR en TASK : T_GeneraAviones");
         Put(Exception_Message(Event));
   end T_GeneraAviones;
```

Por último, esta tarea se creará en el main del programa:

``` ADA
-- main.adb

with PKG_tipos; use PKG_tipos;
with Ada.Integer_Text_IO; USE Ada.Integer_Text_IO;
with Ada.Text_IO; use Ada.Text_IO;
with pkg_planeGenerator; use pkg_planeGenerator;
with PKG_graficos;

procedure Main is
   tareaGeneradora : T_GeneraAviones; -- Al declararla ya se ejecutará
begin
   pkg_graficos.Simular_Sistema;
end Main;

```

## Vuelo de aviones

Para realizar el vuelo de los aviones primero se creará la tarea 'T_Avion' la cual será la encargada de mostrar la información del avión y de mostrarlo gráficamente.

Esta tarea se ubicara en el paquete 'pkg_tareaAvion'

``` ADA
-- pkg_tareaavion.ads

with Ada.Numerics.Discrete_Random;
with Ada.Integer_Text_IO; USE Ada.Integer_Text_IO;  
with Ada.Text_IO; use Ada.Text_IO; 
with PKG_tipos; use pkg_Tipos;
with PKG_debug; use PKG_debug;
with Ada.Exceptions;  use Ada.Exceptions;
with PKG_graficos; use PKG_graficos;

package pkg_TareaAvion is

   task type T_Avion (ptr_avion: Ptr_T_RecordAvion);
   
   type Ptr_T_Avion is access T_Avion;

end pkg_TareaAvion;
```

Para hacer mover al avión se utilizara el paquete '`pkg_graficos`' y se usarán las instrucciones _APARECE_ y _ACTUALIZA_MOVIMIENTO_ pasándoles por argumento el puntero del avión e indicándole que pase toda la información (ptr_avion.all), además para el movimiento se hará un bucle que haga una pausa del tiempo indicado en _RETARDO_MOVIMIENTO_.

Esta tarea también tendrá manejo de excepciones.

``` ADA
-- pkg_tareaavion.adb

package body pkg_TareaAvion is

   task body T_Avion is
      
   begin
      Put_line("TASK Avion: " & T_IdAvion'Image(ptr_avion.id) & " -Aerovia: " & T_Rango_AereoVia'Image(ptr_avion.aereovia)); 
      Put_line("Aerovia Inicial: " & T_Rango_AereoVia'Image(ptr_avion.aereovia_inicial) & " -Pista: " & T_PistaAterrizaje'Image(ptr_avion.pista));
      Put_line(" -Color: " & T_ColorAvion'Image(ptr_avion.color));
      
      Aparece(ptr_avion.all);
      loop
         Actualiza_Movimiento(ptr_avion.all);
         delay RETARDO_MOVIMIENTO;
      end loop;
      
   exception
      when event: others =>
         PKG_debug.Escribir("ERROR en TASK : T_Avion");
         Put(Exception_Message(Event));
   end T_Avion;

end pkg_TareaAvion;
```