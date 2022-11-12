<center style="font-weight: bold; font-size: 25 ">Práctica 6</center>

```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
varied_style: true
```

<p style="font-weight: bold; font-size: 20 ">Progreso</p>

- [x] Permiso de aterrizaje
- [x] Maniobra de aproximación a la pista
- [x] Maniobra de aterrizaje en la pista

## Permiso de aterrizaje

Para realizar el permiso de aterrizaje tendremos que darle una respuesta distinta al avión cuando este llame a la torre de control. No obstante, esta no va a ser quien de la respuesta directamente, ya que esta se ocupará del resto de aviones mientras otro controlador se hace cargo de los aviones.

Para realizar esta acción encolaremos los aviones que quieran aterrizar a otro entry el cual estará situado en un nuevo objeto protegido.

Antes de nada realizaremos unas modificaciones al código para que los aviones no desaparezcan al llegar a la aerovía 6:

``` ADA
-- pkg_tareaavion.adb
...
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
			 lower_airway(ptr_avion.all,endTask);
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
```

Ahora tendremos que modificar la torre de control para que encole peticiones a otro entry, además de crear los nuevos _entry_'s.

Para la torre de control añadiremos el tipo protegido _**runWay**_ que contendrá dos _entry_'s (Uno para cada pista de aterrizaje), dos booleanos, para comprobar la disponibilidad de la pista de aterrizaje, y un método para liberar las pistas de aterrizaje.

``` ADA
-- pkg_controlTower.ads

with Ada.Integer_Text_IO; USE Ada.Integer_Text_IO;  
with Ada.Text_IO; use Ada.Text_IO; 
with PKG_tipos; use pkg_Tipos;
with PKG_debug; use PKG_debug;
with Ada.Exceptions;  use Ada.Exceptions;
with PKG_graficos; use PKG_graficos;
with pkg_Protected; use pkg_Protected;

package pkg_ControlTower is
   
   
   protected type runWay is
      entry allowLandingPISTA1(ptr_avion : in out T_RecordAvion; response: out Boolean);
      entry allowLandingPISTA2(ptr_avion : in out T_RecordAvion; response: out Boolean);
      procedure freeRunway(ptr_avion: in T_RecordAvion);
   private
      PISTA1_isFree: Boolean := true;
      PISTA2_isFree: Boolean := true;
   end runWay;
   
   task airwayControler is
      entry descendAirway(ptr_avion : in out T_RecordAvion; response: out Boolean);
   end airwayControler;
   
   runwayControler: runWay;
   
end pkg_ControlTower;
```

Se cambiara el comportamiento de la _'Cita extendida'_, ahora comprobara la aerovía del avión, si esta en la ultima este encolara el avión a uno de los entry's, dependiendo de la pista que tiene asignada..

Los entry's nuevos tendrán un _when_ que determinará cuando pueden entrar. Al entrar se les bajará el tren de aterrizaje y se avisará que la pista estará ocupada.

``` ADA
-- pkg_controlTower.adb

package body pkg_ControlTower is
   
   task body airwayControler is  
      alternator: boolean := false;
   begin
      
      delay duration(50);
      
      loop 
         accept descendAirway (ptr_avion : in out T_RecordAvion; response : out Boolean) do
            response := false;
            
            if(ptr_avion.aereovia = 6) then
               if(ptr_avion.pista = PISTA1) then
                  requeue runwayControler.allowLandingPISTA1;
               elsif(ptr_avion.pista = PISTA2) then
                  requeue runwayControler.allowLandingPISTA2;
               end if;
            
            elsif(protectedArray(ptr_avion.aereovia + 1).getPlaneCont < MAX_AVIONES_AEROVIA) then
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
   
   protected body runWay is
      procedure freeRunway(ptr_avion : in T_RecordAvion) is
      begin
         if(ptr_avion.pista = PISTA1) then
            PISTA1_isFree:= true;
         elsif(ptr_avion.pista = PISTA2) then
            PISTA2_isFree := true;
         end if;
      end freeRunway;
      
      entry allowLandingPISTA1(ptr_avion : in out T_RecordAvion; response: out Boolean) when PISTA1_isFree is
      begin
         ptr_avion.tren_aterrizaje:= true;
         PISTA1_isFree:= false;
         response:= false;
      end allowLandingPISTA1;
      
      entry allowLandingPISTA2(ptr_avion : in out T_RecordAvion; response: out Boolean) when PISTA2_isFree is
      begin
         ptr_avion.tren_aterrizaje:= true;
         PISTA2_isFree:= false;
         response:= false;
      end allowLandingPISTA2;
      
   end runWay;
end pkg_ControlTower;
```

## Maniobra de aproximación a la pista

Una vez recibida la orden el avión se dispondrá a aterrizar, para ello primero debe encontrar la entrada a la pista. 

Solamente deberemos decir que el avión pare cuando encuentre el punto de entrada:

``` ADA
-- pkg_tareaavion.adb

loop
	response := false;
	select
		pkg_ControlTower.airwayControler.descendAirway(ptr_avion.all,response);
	
		if response then
		...
		elsif(ptr_avion.tren_aterrizaje) then
		   posPista := Pos_Inicio(ptr_avion.pista);
		   endLoop := false;
		   loop
			  if(ptr_avion.pos.X = posPista.X) then
				 leaveAirway(ptr_avion.all);
				 runwayControler.freeRunway(ptr_avion.all);
				 endTask:=True;
				 endLoop:= True;
			  else
				 protectedArray(ptr_avion.aereovia).movePlane(ptr_avion);
				 delay RETARDO_MOVIMIENTO;
			  end if;
			  exit when endLoop;
		   end loop;
		end if;
		...
```

## Maniobra de aterrizaje en la pista

Para finalizar tendremos que hacer que el avión pase por la pista de aterrizaje hasta el final de esta y después desaparecer, liberando la pista de aterrizaje.

Para ello crearemos un nuevo procedure en _pkg_protected_ llamado _**landingSecuence**_ que realizará el aterrizaje:

``` ADA
--pkg_protected.ads

...
procedure landingSecuence(ptr_avion: in out T_RecordAvion);
...


--pkg_protected.adb

...
procedure landingSecuence(ptr_avion: in out T_RecordAvion) is
  endLoop : Boolean;
begin
  endLoop := false;
  protectedArray(ptr_avion.aereovia).leaveAirway(ptr_avion);

  ptr_avion.velocidad.X := 0;
  ptr_avion.velocidad.Y := VELOCIDAD_VUELO;
  Aparece_En_Pista(ptr_avion);
  loop
	 if(Fin_Aterrizaje(ptr_avion.pos.Y)) then
		Desaparece_En_Pista(ptr_avion);
		Put_line("Plane (" & T_IdAvion'Image(ptr_avion.id) & " ," & T_Rango_AereoVia'Image(ptr_avion.aereovia_inicial) & " ) Has Landed");
		endLoop := true;
	 else
		Actualiza_Movimiento_En_Pista(ptr_avion);
		ptr_avion.velocidad.y := T_RangoVelocidad(Float'Ceiling(Float(VELOCIDAD_INICIO_ATERRIZAJE) * (1.0 - Float(ptr_avion.pos.y - Y_INICIO_PISTA) / Float(LON_PISTA))));
		delay(RETARDO_MOVIMIENTO);
	 end if;
	 exit when endLoop;
  end loop;
end landingSecuence;
...
```

Por último hacemos que el avión llame a este metodo:

``` ADA
-- pkg_tareaavion.adb

loop
	response := false;
	select
		pkg_ControlTower.airwayControler.descendAirway(ptr_avion.all,response);
	
		if response then
		...
		elsif(ptr_avion.tren_aterrizaje) then
		   posPista := Pos_Inicio(ptr_avion.pista);
		   endLoop := false;
		   loop
			  if(ptr_avion.pos.X = posPista.X) then
				 landingSecuence(ptr_avion.all);
				 runwayControler.freeRunway(ptr_avion.all);
				 endTask:=True;
				 endLoop:= True;
			  else
				 protectedArray(ptr_avion.aereovia).movePlane(ptr_avion);
				 delay RETARDO_MOVIMIENTO;
			  end if;
			  exit when endLoop;
		   end loop;
		end if;
		...
```


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

![[Práctica_6_Result.mkv]]

<center style="font-weight: bold; font-size: 20 ">Salida por Consola</center>

```
**************************************************************************
**************************************************************************
          PRACTICA STR: CONTROL DE TRAFICO AEREO
**************************************************************************
**************************************************************************
 
Inicializando Entorno Gráfico...
Entorno Gráfico Inicializado.
Start Delay of:
          5
TASK Avion:  0 -Aerovia:  1
Aerovia Inicial:  1 -Pista: SIN_PISTA
 -Color: BLUE
TASK Avion:  0 -Aerovia:  2
Aerovia Inicial:  2 -Pista: SIN_PISTA
 -Color: BLUE
Start Delay of:
          3
TASK Avion:  0 -Aerovia:  3
Aerovia Inicial:  3 -Pista: SIN_PISTA
 -Color: BLUE
Start Delay of:
          4
TASK Avion:  0 -Aerovia:  4
Aerovia Inicial:  4 -Pista: SIN_PISTA
 -Color: BLUE
Start Delay of:
          5
TASK Avion:  1 -Aerovia:  1
Aerovia Inicial:  1 -Pista: SIN_PISTA
 -Color: BLUE
Start Delay of:
          3
TASK Avion:  1 -Aerovia:  2
Aerovia Inicial:  2 -Pista: SIN_PISTA
 -Color: BLUE
Start Delay of:
          3
TASK Avion:  1 -Aerovia:  3
Start Delay of:
          2
Aerovia Inicial:  3 -Pista: SIN_PISTA
 -Color: BLUE
TASK Avion:  1 -Aerovia:  4
Aerovia Inicial:  4 -Pista: SIN_PISTA
 -Color: BLUE
Start Delay of:
          4
TASK Avion:  2 -Aerovia:  1
Aerovia Inicial:  1 -Pista: SIN_PISTA
 -Color: BLUE
Start Delay of:
          5
Start Delay of:
          2
TASK Avion:  2 -Aerovia:  2
Aerovia Inicial:  2 -Pista: SIN_PISTA
 -Color: BLUE
Start Delay of:
          2
TASK Avion:  2 -Aerovia:  3
Aerovia Inicial:  3 -Pista: SIN_PISTA
 -Color: BLUE
TASK Avion:  2 -Aerovia:  4
Aerovia Inicial:  4 -Pista: SIN_PISTA
 -Color: BLUE
Start Delay of:
          2
Stoped Plane Generation
Plane ( 0 , 4 ) Has Landed
Plane ( 1 , 4 ) Has Landed
Plane ( 2 , 4 ) Has Landed
Plane ( 0 , 3 ) Has Landed
Plane ( 1 , 3 ) Has Landed
Plane ( 2 , 3 ) Has Landed
Plane ( 0 , 2 ) Has Landed
Plane ( 1 , 1 ) Has Landed
Plane ( 2 , 2 ) Has Landed
Plane ( 2 , 1 ) Has Landed
Plane ( 1 , 2 ) Has Landed
Plane ( 0 , 1 ) Has Landed
[2022-11-12 16:37:45] process terminated successfully, elapsed time: 07:46.38s
```