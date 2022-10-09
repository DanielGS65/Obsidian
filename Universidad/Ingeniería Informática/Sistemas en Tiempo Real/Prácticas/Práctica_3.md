<center style="font-weight: bold; font-size: 25 ">Práctica 3</center>

```toc
title: "## Índice"
style: number 
min_depth: 2 
max_depth: 3
varied_style: true
```

<p style="font-weight: bold; font-size: 20 ">Progreso</p>

- [x] Diferencias entre _Calendar_ y _RealTime_
- [x] Diferencias entre _Delay_ y _Delay Until_

## Diferencias entre _Calendar_ y _RealTime_

Se nos pide realizar dos paquetes, cada paquete debe realizar la misma función, simular un cronometro,  no obstante cada paquete contendrá una librería de tiempos diferente:

### Calendar

``` ADA
-- pkg_cronometro_calendar.ads

with PKG_graficos; use PKG_graficos;
with Ada.Calendar; use Ada.Calendar;

package pkg_Cronometro_Calendar is
     
   task cronometerCalendar;

end pkg_Cronometro_Calendar;


-- pkg_cronometro_calendar.adb

package body pkg_Cronometro_Calendar is

   task body cronometerCalendar is
      timeStart: Time;
      timeEnd: Time;
      diference : duration;
      waitingTime: Time;
      delayTime : duration := 1.0;
   begin
      timeStart := clock;
      waitingTime := timeStart + delayTime;
      loop
         delay until waitingTime;
         timeEnd := clock;
         diference := timeEnd - timeStart;
         waitingTime:= waitingTime + delayTime;
         Actualiza_Cronometro(diference);
      end loop;
 
   end cronometerCalendar;
     
end pkg_Cronometro_Calendar;
```

### RealTime

``` ADA
-- pkg_cronometro_realTime.ads

with PKG_graficos; use PKG_graficos;
with Ada.Real_Time; use Ada.Real_Time;
package pkg_Cronometro_realTime is
     
   task cronometerRealTime;

end pkg_Cronometro_realTime;


-- pkg_cronometro_realTime.adb

package body pkg_Cronometro_realTime is

   task body cronometerRealTime is
      timeStart: Time;
      timeEnd: Time;
      diference: Time_Span;
      waitingTime: Time;
      delayTime : Time_Span := To_Time_Span(1.0);
   begin
      timeStart := clock;
      waitingTime := timeStart + delayTime;
      loop
            delay until waitingTime;
            timeEnd := clock;
            diference := timeEnd - timeStart;
            waitingTime:= waitingTime + delayTime;
            Actualiza_Cronometro(To_Duration(diference));
      end loop;
 
   end cronometerRealTime;

end pkg_Cronometro_realTime;
```

### Diferencias

A simple vista no se encuentra ninguna diferencia en su ejecución, en los dos casos el cronometro esta en la mayor parte de su tiempo siempre con los decimales a 0, en caso de que salgan decimales estos no se acarrean.

Probablemente sea mas efectivo Calendar debido a que este no necesita realizar conversiones durante la ejecución.

## Diferencias entre _Delay_ y _Delay Until_

Se ha modificado el código de Calendar para introducir un delay.

``` ADA
-- pkg_cronometro_calendar.adb

package body pkg_Cronometro_Calendar is

   task body cronometerCalendar is
      timeStart: Time;
      timeEnd: Time;
      diference : duration;
      waitingTime: Time;
      delayTime : duration := 1.0;
   begin
      timeStart := clock;
      waitingTime := timeStart + delayTime;
      loop
         -- delay until waitingTime;
         delay 1.0;
         timeEnd := clock;
         diference := timeEnd - timeStart;
         waitingTime:= waitingTime + delayTime;
         Actualiza_Cronometro(diference);
      end loop;
 
   end cronometerCalendar;
     
end pkg_Cronometro_Calendar;
```

En esta situación se acarrea siempre unos milisegundos al cronometro, acarreando el error con cada adición. 