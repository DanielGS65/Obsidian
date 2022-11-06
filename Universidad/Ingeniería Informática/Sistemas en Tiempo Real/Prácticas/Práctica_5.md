<center style="font-weight: bold; font-size: 25 ">Práctica 5</center>

```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
varied_style: true
```

## Modificaciones Previas

Antes de empezar se añadirá una nueva función en '_pkg_graficos_' que añadirá una tercera opción para devolver la posición inicial de un avión proporcionando las coordenadas en X y la aerovía del avión, esta función servirá para actualizar la posición del avión una vez cambie de aerovía.

``` ADA
-- pkg_graficos.ads
...
FUNCTION Pos_Inicio(pos_x: T_CoordenadaX; aereovia: T_Rango_AereoVia) return T_Punto;
...

-- pkg_graficos.adb
...
FUNCTION Pos_Inicio(pos_x: T_CoordenadaX; aereovia: T_Rango_AereoVia) RETURN T_Punto IS
BEGIN
  return (X => pos_x, Y => y_aereovia(aereovia));
END Pos_Inicio;
...
```

Además he decidido cambiar el color por defecto de los aviones a Azul, para comprobar cuales han sido atendidos y cuales no.

``` ADA
-- pkg_planegenerator.adb

-- ptr_avion.color := T_ColorAparicionAvion'val(pkg_randomColor.Random(random_color));
ptr_avion.color := Blue;
```

## Añadir torre de control

La torre de control será un sistema de mensajería entre tareas que será la encargada de informar a las tareas Avión si la aerovía inferior no contiene el máximo de aviones permitido y esta dar la orden al avión para que cambie de aerovía.

Esta función la llevara el nuevo paquete '_pkg_controlTower'_ el cual contendrá una tarea llamada '_airwayControler_' que será la encargada de recibir las peticiones de los aviones.

La tarea deberá albergar una 'Cita extendida' para poder recibir las peticiones, esta cita será una entry llamada '_descendAirway_':

``` ADA
-- pkg_controltower.ads

with Ada.Integer_Text_IO; USE Ada.Integer_Text_IO;  
with Ada.Text_IO; use Ada.Text_IO; 
with PKG_tipos; use pkg_Tipos;
with PKG_debug; use PKG_debug;
with Ada.Exceptions;  use Ada.Exceptions;
with PKG_graficos; use PKG_graficos;
with pkg_Protected; use pkg_Protected;

package pkg_ControlTower is

   task airwayControler is
      entry descendAirway(ptr_avion : in out T_RecordAvion; response: out Boolean);
   end airwayControler;

end pkg_ControlTower;
```

En la implementación realizaremos un '_Accept_' de la entry que realizará lo siguiente:

Comprobará que la aerovía inferior no contiene el número máximo de aviones. Si esto es cierto, incrementará en uno el número máximo de aviones en la aerovía inferior [^1].

Después, si el avión no tiene asignada una pista de aterrizaje, se le asigna una de manera alternada y se le asigna el color asignada a la aerovía.

Por ultimo se le envía un booleano para confirmar que se le ha concedido el permiso.

``` ADA
--pkg_controlTower.adb

package body pkg_ControlTower is

   task body airwayControler is  
      alternator: boolean := false;
   begin
      
      delay duration(50);
      
      loop 
           
         accept descendAirway (ptr_avion : in out T_RecordAvion; response : out Boolean) do
               
         response := false;
               
            if(protectedArray(ptr_avion.aereovia + 1).getPlaneCont < MAX_AVIONES_AEROVIA) then
               protectedArray(ptr_avion.aereovia + 1).increaseCont;
               response := true;
               if(ptr_avion.pista = SIN_PISTA) then
                  if(alternator) then
                     ptr_avion.color := Red;
                     ptr_avion.pista := T_PistaAterrizaje'val(1);
                     alternator := false;
                  else
                     ptr_avion.color := Green;
                     ptr_avion.pista := T_PistaAterrizaje'val(2);
                     alternator := true;
                  end if;
               end if;
               
            end if;
                  
         end descendAirway;
         
         delay duration(2);

      end loop;
      
   end airwayControler;

end pkg_ControlTower;
```

También se ha añadido a '_pkg_protected_' la función '_getPlaneCont_' pata obtener la cantidad de aviones en una aerovía:

```ADA
-- pkg_potected.ads
...
function getPlaneCont return T_Plane_Counter;
...

-- pkg_protected.adb
...
function getPlaneCont return T_Plane_Counter is
begin
 return planeCont;
end getPlaneCont;
...
```

## Peticiones de concesión de descenso

Ya hemos hecho la parte del receptor de peticiones, ahora toca mandar las peticiones, para ello debemos modificar la clase '_pkg_tareaAvion_'.

Debemos modificar el movimiento de estos, para ello haremos una sentencia '_Select then abort_'.

``` ADA
-- pkg_tareaavion.adb
...
loop
	response := false;
	select
		pkg_ControlTower.airwayControler.descendAirway(ptr_avion.all,response);
	then abort  
		loop
		   protectedArray(ptr_avion.aereovia).movePlane(ptr_avion);
		   delay RETARDO_MOVIMIENTO;
		end loop;
	end select;
end loop;
...
```

Esta sentencia intentará comunicarse con la torre de control, mientras lo hace, este seguirá moviéndose.

Esto en su estado actual no realizaría ninguna acción, ya que si nos concede el permiso no realizaremos nada.

## Descenso de aerovía

Para realizar un descenso a la aerovía tenemos que comprobar un par de cosas:

- Comprobar que la aerovía inferior no tiene el numero máximo de aeronaves.
- Comprobar que nuestra posición actual no esta ocupada en la aerovía inferior

El primero requisito ya esta comprobado por la torre de control, por lo que el resto lo debe hacer el avión.

Primero deberemos comprobar que hay debajo del avión, además de realizar dos funciones extra, una que elimine el avión de la aerovía actual y decremente el número y otra que lo situe en la aerovía inferior sin incrementar su valor [^2] , para ello añadimos estas funciones a '_pkg_protetced_':

``` ADA
-- pkg_protected.ads
...
function checkCurrentPos(pos: in T_CoordenadaX) return Boolean;
...
procedure landing(ptr_avion: Ptr_T_RecordAvion);
procedure changeAirway(ptr_avion: Ptr_T_RecordAvion);
...

-- pkg_protected.adb
...
function checkCurrentPos(pos: in T_CoordenadaX) return Boolean is
  A,B,C,D,E : Boolean;
begin
 A := not aeroregion(Posicion_Rejilla(pos));
 B := not aeroregion(Posicion_Rejilla(pos) + 1);
 C := not aeroregion(Posicion_Rejilla(pos) - 1);
 D := not aeroregion(Posicion_Rejilla(pos) + 2);
 E := not aeroregion(Posicion_Rejilla(pos) - 2);

 if(A and B and C and D and E) then
   return True;
 else
   return False;
 end if;
end checkCurrentPos;
...
procedure landing(ptr_avion: Ptr_T_RecordAvion) is
begin
 Desaparece(ptr_avion.all);
 aeroregion(Posicion_Rejilla(ptr_avion.pos.X)) := False;
 decreaseCont;
end landing;

procedure changeAirway(ptr_avion: Ptr_T_RecordAvion) is
begin
 Aparece(ptr_avion.all);
 aeroregion(Posicion_Rejilla(ptr_avion.pos.X)) := True;
end changeAirway;
...
```

Realizadas estas funciones solo nos quedará modificar '_pkg_tareaAvion_' para que responda a la contestación de la torre de control:

``` ADA
--pkg_tareaavion.adb

package body pkg_TareaAvion is

   task body T_Avion is
      response: Boolean;
      endLoop: Boolean := false;
      currentColor : T_ColorAvion;
   begin
      Put_line("TASK Avion: " & T_IdAvion'Image(ptr_avion.id) & " -Aerovia: " & T_Rango_AereoVia'Image(ptr_avion.aereovia)); 
      Put_line("Aerovia Inicial: " & T_Rango_AereoVia'Image(ptr_avion.aereovia_inicial) & " -Pista: " & T_PistaAterrizaje'Image(ptr_avion.pista));
      Put_line(" -Color: " & T_ColorAvion'Image(ptr_avion.color));
      
      protectedArray(ptr_avion.aereovia).takeOff(ptr_avion);
      
      loop
         response := false;
         select
            pkg_ControlTower.airwayControler.descendAirway(ptr_avion.all,response);
            
            if response then
               currentColor := ptr_avion.color;
               loop
                  ptr_avion.color := currentColor;
                  if(protectedArray(ptr_avion.aereovia + 1).checkCurrentPos(ptr_avion.pos.X)) then
                     endLoop := true;
                     protectedArray(ptr_avion.aereovia).landing(ptr_avion);
                     if(ptr_avion.aereovia < NUM_AEREOVIAS-1) then
                        ptr_avion.aereovia := ptr_avion.aereovia + 1;
                        ptr_avion.velocidad.X := ptr_avion.velocidad.X * (-1);
                        ptr_avion.pos := Pos_Inicio(ptr_avion.pos.x,ptr_avion.aereovia);
                        protectedArray(ptr_avion.aereovia).changeAirway(ptr_avion);
                  
                     else
                        protectedArray(ptr_avion.aereovia + 1).decreaseCont;
                        Put_line("Plane (" & T_IdAvion'Image(ptr_avion.id) & " ," & T_Rango_AereoVia'Image(ptr_avion.aereovia_inicial) & " ) Has Landed");
                        delay duration(100000);
                     end if;
                  
                  else
                     ptr_avion.color := Yellow;
                     protectedArray(ptr_avion.aereovia).movePlane(ptr_avion);
                     delay RETARDO_MOVIMIENTO;
                  end if;
                 
                  exit when endLoop;
               end loop;
            
            endLoop := false;
         end if;
         then abort  
            loop
               protectedArray(ptr_avion.aereovia).movePlane(ptr_avion);
               delay RETARDO_MOVIMIENTO;
            end loop;
         end select;
         
         
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

Como extra se han coloreado los aviones que van a realizar una maniobra en Amarillo.

## Resultado

```ad-info
title: Esquema de Colores

- <font color='Blue'>AZUL</font> -> Aviones sin pista asignada.
- <font color='Red'>ROJO</font> -> Aviones asignados a la pista 1.
- <font color='Green'>VERDE</font> -> Aviones asignados a la pista 2.
- <font color='Yellow'>AMARILLO</font> -> Aviones esperando para realizar una maniobra.
```

```ad-warning
title: Advertencia

Si se lee desde un PDF, el video esta en github en la misma carpeta que el PDF.
El PDF no admite videos.
```

![[Practica_5_Result.mkv]]

<center style="font-weight: bold; font-size: 20 ">Salida por Consola</center>

```
**************************************************************************
**************************************************************************
          PRACTICA STR: CONTROL DE TRAFICO AEREO
**************************************************************************
**************************************************************************
 
Inicializando Entorno Gráfico...
Entorno Gráfico Inicializado.
TASK Avion:  0 -Aerovia:  1
Aerovia Inicial:  1 -Pista: SIN_PISTA
 -Color: BLUE
Start Delay of:
          4
TASK Avion:  0 -Aerovia:  2
Aerovia Inicial:  2 -Pista: SIN_PISTA
 -Color: BLUE
Start Delay of:
          5
TASK Avion:  0 -Aerovia:  3
Aerovia Inicial:  3 -Pista: SIN_PISTA
 -Color: BLUE
Start Delay of:
          5
TASK Avion:  0 -Aerovia:  4
Aerovia Inicial:  4 -Pista: SIN_PISTA
 -Color: BLUE
Start Delay of:
          5
TASK Avion:  1 -Aerovia:  1
Aerovia Inicial:  1 -Pista: SIN_PISTA
 -Color: BLUE
Start Delay of:
          2
TASK Avion:  1 -Aerovia:  2
Aerovia Inicial:  2 -Pista: SIN_PISTA
 -Color: BLUE
Start Delay of:
          6
TASK Avion:  1 -Aerovia:  3
Aerovia Inicial:  3 -Pista: SIN_PISTA
 -Color: BLUE
Start Delay of:
          4
TASK Avion:  1 -Aerovia:  4
Aerovia Inicial:  4 -Pista: SIN_PISTA
 -Color: BLUE
Start Delay of:
          4
TASK Avion:  2 -Aerovia:  1
Aerovia Inicial:  1 -Pista: SIN_PISTA
 -Color: BLUE
Start Delay of:
          4
TASK Avion:  2 -Aerovia:  2
Aerovia Inicial:  2 -Pista: SIN_PISTA
 -Color: BLUE
Start Delay of:
          2
TASK Avion:  2 -Aerovia:  3
Aerovia Inicial:  3 -Pista: SIN_PISTA
 -Color: BLUE
Start Delay of:
          3
TASK Avion:  2 -Aerovia:  4
Aerovia Inicial:  4 -Pista: SIN_PISTA
 -Color: BLUE
Start Delay of:
          5
Stoped Plane Generation
Plane ( 0 , 4 ) Has Landed
Plane ( 1 , 4 ) Has Landed
Plane ( 2 , 4 ) Has Landed
Plane ( 0 , 3 ) Has Landed
Plane ( 1 , 3 ) Has Landed
Plane ( 2 , 3 ) Has Landed
Plane ( 0 , 2 ) Has Landed
Plane ( 1 , 2 ) Has Landed
Plane ( 2 , 2 ) Has Landed
Plane ( 0 , 1 ) Has Landed
Plane ( 1 , 1 ) Has Landed
Plane ( 2 , 1 ) Has Landed
[2022-11-06 16:52:30] process terminated successfully, elapsed time: 03:39.14s
```

[^1]:  Esto se hace para que no pueda decir a dos aviones que pueden bajar a la aerovía inferior cuando solo queda un hueco libre.
[^2]: Ya se había aumentado el número de aviones en la aerovía desde la torre de control