#flashcards/Parcial_1

==Presiona Ctrl+0 para revisar las cartas==

Dado el Siguiente Código y sabiendo que se pide que cada tarea se ejecute cada 5 segundos, ¿el código mostrado sería correcto?
``` ADA
task body tarea is
begin
	delay(5.0);
	function;
end tarea;
```
?
No, no sería correcto debido a que la instrucción delay esperará 5 segundos además de tener que esperar la ejecución de la función a la que llama. Debería usarse la instrucción _delay until_.
<!--SR:!2022-10-07,3,250-->



