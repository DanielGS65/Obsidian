<center style="font-weight: bold; font-size: 25 ">Desarrollo de Software en Arquitecturas Paralelas</center>

La asignatura estará dada en 4 horas seguidas pero no están separadas realmente en teoría y practica.

Objetivos:

- Mostrar la importancia de la computación de altas prestaciones
- Conocimiento general de programación paralela y conocer modelos y evaluación de algoritmos paralelos.

Las prácticas no serán complicadas por sus algoritmos, Lo que se propone se puede paralelizar.

La asignatura contiene 6 Temas.

Trabajaremos con MPI.

Tenemos un setUp especial en el laboratorio que nos permitirá trabajar en paralelo (no como antes), si lo he entendido bien es un servidor virtual, que también guardará los archivos que guardemos en los PC's.

Las practicas se entregan dentro de nuestra cuenta en un directorio concreto, indicado por el profesor. Cuando el proyecto esta finalizado se creara un TXT en la carpeta llamado "Entrega" y que dentro contengan nombres y apellidos (para indicar al profesor que esta terminado).

Se programará en C, se pueden usar cosas de C++, pero hay que intentar mantener el código simple (no usar clases). No usaremos OPENMPI porque da problemas con las direcciones IP (aja... ya sabia yo eso..., yo ya esta ahí...) usaremos MPICH (creo que es así)

4 Practicas:
- Anillos ==20%== -> 13 Marzo
	- Hacer una suma en forma de anillo (pasando un numero a cada unidad y sumarle uno)
- PRIMOS o TCOM (elegir una) ==20%== -> 3 de Abril
	- PRIMOS: Calculo del numero de enteros primos menores que un dado
	- TCOM: Coste de comunicaciones (no me ha dado tiempo a mirar más)
- Aristas ==30%== -> 8 de mayo
	- LaPlus, sacar numero de aristas en una imagen (creo)
- MMmalla ==30%== -> 22 de mayo
	- Producto Matriz-Matriz con algoritmo desarrollado para trabajar en paralelo (Cannon)
- FW (optativa, en plan, de verdad) ==??== -> 22 de mayo
	- Floyd-Warshall, algoritmo de búsqueda de caminos


