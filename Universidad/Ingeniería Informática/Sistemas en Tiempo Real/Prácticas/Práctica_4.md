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

En la declaración del paquete crearemos el tipo de dato _T_Protected_ que albergará un array de booleanos y un contador de aviones. Con este tipo declararemos de forma privada un array de tipo _T_Protected_ de tantas posiciones como aerovías existan.

Además se declarará la funcion _Posición_Rejilla_.

``` ADA

--pkg_Protected.ads

with PKG_graficos; use PKG_graficos;

package pkg_Protected is
   
   type T_Plane_Counter is new integer range 0 .. MAX_AVIONES_AEROVIA;
   

   type T_Protected is
      record
         aeroregion: T_Rejilla_Ocupacion;
         planeCont: T_Plane_Counter;
      end record;

   function Posicion_Rejilla(pos_x : T_CoordenadaX) return T_Rango_Rejilla_X;
   
private
   
   protectedArray : array(T_Rango_AereoVia) of T_Protected;
   
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

Primero tenemos que tener claro que hace falta retocar. Primero tendremos que **modificar la creación de los aviones para que incremente el valor de aviones por aerovía.** Aunque no se creen varios aviones al mismo tiempo, se ha creado de tal manera que sea sección crítica, ya que el objeto a modificar sigue siendo el mismo (_protectedArray_). Además, tambien hay que **modificar las posiciones protegidas cada vez que se mueva un avión**, eliminando su anterior posición y protegiendo la nueva. De la misma manera, hay que **leer el array de booleanos para saber si un avión puede aparecer**.

Para ello modificaremos la declaración de _pkg_Protected_ para crear los procedimientos de manera protegida creando un _**type protected**_ con estos procedimientos, se llamará _PlaneQueue_ y ademas declararemos un tipo puntero que acceda a este _protected_:

``` ADA

--pkg_Protected.ads

with PKG_tipos; use pkg_Tipos;
with Ada.Integer_Text_IO; USE Ada.Integer_Text_IO;  
with Ada.Text_IO; use Ada.Text_IO; 
with PKG_graficos; use PKG_graficos;


package pkg_Protected is
   
   type T_Plane_Counter is new integer range 0 .. MAX_AVIONES_AEROVIA;
   

   type T_Protected is
      record
         aeroregion: T_Rejilla_Ocupacion;
         planeCont: T_Plane_Counter;
      end record;

   function Posicion_Rejilla(pos_x : T_CoordenadaX) return T_Rango_Rejilla_X;
   
   
   protected type PlaneQueue is
      procedure switchPosition(aerovia: in T_Rango_AereoVia; old_pos: in T_CoordenadaX; new_pos: in T_CoordenadaX);
      procedure increaseCont(aerovia: in T_Rango_AereoVia);
      procedure decreaseCont(aerovia: in T_Rango_AereoVia);
      procedure movePlane(ptr_Avion: Ptr_T_RecordAvion);
      function checkTakeOff(aereovia: T_Rango_AereoVia) return Boolean;
   end PlaneQueue;
   
   type Ptr_Queue is access PlaneQueue;
   
private
   
   protectedArray : array(T_Rango_AereoVia) of T_Protected;
   
end pkg_Protected;
```

Se usarán las funciones _increaseCont_ y _decreaseCont_ para modificar el número de aviones por aerovía, _movePlane_ se usará para mover el avión y cambiar las posiciones, de esta manera otro avión no se moverá mientras se cambian las posiciones usando _switchPosition_, y la  _checkTakeOff_ se usará para comprobar si un avión puede salir a la aerovía de manera segura.

La implementación de estas funciones es la siguiente:

``` ADA

--pkg_Protected.adb

package body pkg_Protected is

   function Posicion_Rejilla(pos_x : T_CoordenadaX) return T_Rango_Rejilla_X is
   begin
      return T_Rango_Rejilla_X(pos_x * TAM_X_REJILLA / (X_INICIO_DER_AVION+1));
   end Posicion_Rejilla;

   protected body PlaneQueue is

      function checkTakeOff(aereovia: T_Rango_AereoVia) return Boolean is
         A,B,C,D : Boolean;
      begin
         A := not boolean(protectedArray(aereovia).aeroregion(T_Rango_Rejilla_X'First));
         B := not boolean(protectedArray(aereovia).aeroregion(T_Rango_Rejilla_X'First + 1));
         C := not boolean(protectedArray(aereovia).aeroregion(T_Rango_Rejilla_X'Last));
         D := not boolean(protectedArray(aereovia).aeroregion(T_Rango_Rejilla_X'Last - 1));

         if(A and B and C and D) then
           return True;
         else
           return False;
         end if;

      end checkTakeOff;

      procedure increaseCont(aerovia: in T_Rango_AereoVia) is
      begin
         protectedArray(aerovia).planeCont := protectedArray(aerovia).planeCont + 1;
      end increaseCont;
      
      procedure decreaseCont(aerovia: in T_Rango_AereoVia) is
      begin
         protectedArray(aerovia).planeCont := protectedArray(aerovia).planeCont - 1;
      end decreaseCont;
      
      procedure switchPosition(aerovia: in T_Rango_AereoVia; old_pos: in T_CoordenadaX; new_pos: in T_CoordenadaX) is
      begin
         protectedArray(aerovia).aeroregion(Posicion_Rejilla(old_pos)) := False;
         protectedArray(aerovia).aeroregion(Posicion_Rejilla(new_pos)) := True;
      end switchPosition;

      procedure movePlane(ptr_Avion: Ptr_T_RecordAvion) is
      begin
         switchPosition(ptr_avion.aereovia, ptr_Avion.pos.X, Nueva_PosicionX(ptr_Avion.pos.X, ptr_Avion.velocidad.X));
         Actualiza_Movimiento(ptr_avion.all);
      end movePlane;

   end PlaneQueue;

end pkg_Protected;

```

Después de modificar el paquete Protected ahora toca cambiar las llamadas en el resto de paquetes.

Primero tenemos que modificar la creación de los aviones en el _pkg_PlaneGenerator_ incrementando el valor de aviones en la aerovía y pasando el puntero del tipo PlaneQueue para usar las funciones protegidas:

```ad-info
title:Cambios

``` ADA
--pkg_planegenerator.ads

procedure createPlane(id: in T_IdAvion; aerovia : in T_Rango_AereoVia; queue : in Ptr_Queue);



--pkg_planegenerator.adb

procedure createPlane(id: in T_IdAvion; aerovia : in T_Rango_AereoVia; queue : in Ptr_Queue) is

begin
...
	tarea_avion := new T_Avion(ptr_avion,queue);
	
end createPlane;


task body T_GeneraAviones is
...
begin
	queue := new PlaneQueue;
	loop
		...
		stopCheck := False;
            loop
               if(queue.checkTakeOff(aerovia)) then
                  createPlane(T_IdAvion(id),aerovia,queue);
                  stopCheck := True;
               end if;
               exit when stopCheck;
            end loop;
            queue.increaseCont(aerovia);
			...
end T_GeneraAviones
```

Para acabar debemos modificar la tarea _T_TareaAvion_ para que esta admita el nuevo parámetro y que al moverse llame a la nueva función protegida, además de eliminar un avión de la aerovía en caso de choque.

```ad-info
title:Cambios

``` ADA

--pkg_tareaavion.ads

task type T_Avion (ptr_avion: Ptr_T_RecordAvion; queue: Ptr_Queue);


--pkg_tareaavion.adb

task body T_Avion is
begin
	...
	loop
		queue.movePlane(ptr_avion);
        delay RETARDO_MOVIMIENTO;
	end loop;
exception
	when event: DETECTADA_COLISION =>
		...
		queue.decreaseCont(ptr_avion.aereovia);
end T_Avion;

```
