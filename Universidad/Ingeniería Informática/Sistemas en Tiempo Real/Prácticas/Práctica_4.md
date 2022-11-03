<center style="font-weight: bold; font-size: 25 ">Práctica 4</center>

```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
varied_style: true
```

<p style="font-weight: bold; font-size: 20 ">Progreso</p>

- [x] Definir objetos protegidos
- [x] Añadir operaciones al tipo protegido que usarán las tareas de tipo T_TareaAvion

## Definir objetos protegidos

Se creará el paquete _pkg_Protected_.

En la declaración del paquete crearemos el tipo de dato PlaneQueue que sera un tipo protegido el cual albergará un array de booleanos y un contador de aviones. Con este tipo declararemos un array PlaneQueue de tantas posiciones como aerovías existan.

Además se declarará la funcion _Posición_Rejilla_.

``` ADA

--pkg_Protected.ads

with PKG_tipos; use pkg_Tipos;

package pkg_Protected is
   
   type T_Plane_Counter is new integer range 0 .. MAX_AVIONES_AEROVIA;

   function Posicion_Rejilla(pos_x : T_CoordenadaX) return T_Rango_Rejilla_X;
   
   
   protected type PlaneQueue is
   private
      aeroregion: T_Rejilla_Ocupacion;
      planeCont: T_Plane_Counter;
   end PlaneQueue;
   
   protectedArray : array(T_Rango_AereoVia) of PlaneQueue;
   
end pkg_Protected;

```

En la implementación solo realizaremos la función _Posición_Rejilla_.

``` ADA
-- pkg_Protected.adb

package body pkg_Protected is

   function Posicion_Rejilla(pos_x : T_CoordenadaX) return T_Rango_Rejilla_X is
   begin
      return T_Rango_Rejilla_X(pos_x * TAM_X_REJILLA / (X_INICIO_DER_AVION+1));
   end Posicion_Rejilla;

end pkg_Protected;
```

## Añadir operaciones al tipo protegido que usarán las tareas de tipo T_TareaAvion

Para hacer que todo funciona primero tendremos que hacer algunas modificaciones al programa.

Primero tenemos que tener claro que hace falta retocar. Primero tendremos que **modificar la aparición de los aviones para que incremente el valor de aviones por aerovía.**, además de comprobar si puede entrar el avión. También hay que **modificar las posiciones protegidas cada vez que se mueva un avión** y cada vez que aparece un avión, eliminando su anterior posición y protegiendo la nueva. De la misma manera, hay que **leer el array de booleanos para saber si un avión puede aparecer**.

Para ello modificaremos la declaración de _pkg_Protected_ para crear los procedimientos de manera protegida creando una serie de funcionalidades que nos permitirá colocar un avión en una aerovía cuando se pueda.

``` ADA

--pkg_Protected.ads

with PKG_tipos; use pkg_Tipos;
with Ada.Integer_Text_IO; USE Ada.Integer_Text_IO;  
with Ada.Text_IO; use Ada.Text_IO; 
with PKG_graficos; use PKG_graficos;


package pkg_Protected is
   
   type T_Plane_Counter is new integer range 0 .. MAX_AVIONES_AEROVIA;

   function Posicion_Rejilla(pos_x : T_CoordenadaX) return T_Rango_Rejilla_X;
   
   
   protected type PlaneQueue is
      procedure switchPosition(old_pos: in T_CoordenadaX; new_pos: in T_CoordenadaX);
      procedure increaseCont;
      procedure decreaseCont;
      procedure movePlane(ptr_Avion: Ptr_T_RecordAvion);
      function checkTakeOff return Boolean;
      entry takeOff(ptr_avion: Ptr_T_RecordAvion);
   private
      aeroregion: T_Rejilla_Ocupacion;
      planeCont: T_Plane_Counter;
   end PlaneQueue;
   
   protectedArray : array(T_Rango_AereoVia) of PlaneQueue;
   
end pkg_Protected;

```

La descripción de las funciones son las siguientes:

```ad-info
title: checkTakeOff

Comprueba si el espacio aereo de una aereovía esta libre para introducir un avión.
```

```ad-info
title: switchPosition

Modifica el array _aeroregion_ borrando donde esta el avión y indicando donde estará.
```

```ad-info
title: movePlane

Llama a _switchPosition_ y ademas mueve el avión, esto se hace asi para evitar que otro avión pueda moverse mientras este no ha modificado toda la información.
```

```ad-info
title: takeOff

Es un entry que primero comprueba si un avión puede aparecer (checkTakeOff) y desupues le hace aparecer.
```

```ad-info
title: increaseCont/decreaseCont

Incrementa o decrementa el numero de avioens por aereovía.
```

La implementación de estas funciones es la siguiente:

``` ADA

pkg_protected.adb

package body pkg_Protected is

   function Posicion_Rejilla(pos_x : T_CoordenadaX) return T_Rango_Rejilla_X is
   begin
      return T_Rango_Rejilla_X(pos_x * TAM_X_REJILLA / (X_INICIO_DER_AVION+1));
   end Posicion_Rejilla;

   protected body PlaneQueue is

      function checkTakeOff return Boolean is
         A,B,C,D : Boolean;
      begin
         A := not aeroregion(T_Rango_Rejilla_X'First);
         B := not aeroregion(T_Rango_Rejilla_X'First + 1);
         C := not aeroregion(T_Rango_Rejilla_X'Last);
         D := not aeroregion(T_Rango_Rejilla_X'Last - 1);

         if(A and B and C and D) then
           return True;
         else
           return False;
         end if;

      end checkTakeOff;
      
      entry takeOff(ptr_avion: Ptr_T_RecordAvion) when checkTakeOff is
      begin
         Aparece(ptr_avion.all);
         aeroregion(Posicion_Rejilla(ptr_avion.pos.X)) := True;
         increaseCont;
      end takeOff;

      procedure increaseCont is
      begin
         planeCont := planeCont + 1;
      end increaseCont;

      procedure decreaseCont is
      begin
         planeCont := planeCont - 1;
      end decreaseCont;

      procedure switchPosition(old_pos: in T_CoordenadaX; new_pos: in T_CoordenadaX) is
      begin
         aeroregion(Posicion_Rejilla(old_pos)) := False;
         aeroregion(Posicion_Rejilla(new_pos)) := True;
      end switchPosition;

      procedure movePlane(ptr_Avion: Ptr_T_RecordAvion) is
      begin
         switchPosition(ptr_Avion.pos.X, Nueva_PosicionX(ptr_Avion.pos.X, ptr_Avion.velocidad.X));
         Actualiza_Movimiento(ptr_avion.all);
      end movePlane;

   end PlaneQueue;

end pkg_Protected;

```

Por ultimo modificaremos el paquete pkg_tareaavion para llamar a estas funciones:

``` ADA
--pkg_tareaavion.adb

package body pkg_TareaAvion is

   task body T_Avion is
      
   begin
      Put_line("TASK Avion: " & T_IdAvion'Image(ptr_avion.id) & " -Aerovia: " & T_Rango_AereoVia'Image(ptr_avion.aereovia)); 
      Put_line("Aerovia Inicial: " & T_Rango_AereoVia'Image(ptr_avion.aereovia_inicial) & " -Pista: " & T_PistaAterrizaje'Image(ptr_avion.pista));
      Put_line(" -Color: " & T_ColorAvion'Image(ptr_avion.color));
      
      protectedArray(ptr_avion.aereovia).takeOff(ptr_avion);
      loop
         protectedArray(ptr_avion.aereovia).movePlane(ptr_avion);
         delay RETARDO_MOVIMIENTO;
      end loop;
      
   exception
      when event: DETECTADA_COLISION =>
         PKG_debug.Escribir("ERROR en TASK : T_Avion");
         Put(Exception_Message(Event));
         Desaparece(ptr_avion.all);
         protectedArray(ptr_avion.aereovia).decreaseCont;
   end T_Avion;
   
end pkg_TareaAvion;

```