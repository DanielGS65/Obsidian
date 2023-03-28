<center style="font-weight: bold; font-size: 25 ">Memoria Entrega 2</center>

<center style="font-weight: bold; font-size: 18 ">Daniel Gozálvez Sánchez</center>

```toc
title: "## Índice"
style: number 
min_depth: 2
max_depth: 2
```

## Ejercicio 1

```ad-statement
Persistir los datos en una Base de Datos usando Docker

- Conectar a NodeRed una Base de Datos en otro contenedor.
- Almacenar el historico de datos.
- Componer ambos servicios en un docker compose
```

```ad-warning
title:Aviso
Todo lo explicado estara adjunto en la entrega.
```

El primer paso que realizaremos será crear un docker-compose.yml para indicar todo lo que queremos incluir:

```ad-code
```yml
version: "3.7"

services:
  nodered:
    build: ./nodered
    environment: 
      - TZ=Europe/Madrid
    ports: 
      - "1880:1880"
    networks:
      networkConnection:
        ipv4_address: 10.3.0.2
    volumes: 
      - data:/noderedData
  db:
    image: mysql
    environment: 
      - TZ=Europe/Madrid
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_USER=nodered
      - MYSQL_PASSWORD=nodered
      - MYSQL_DATABASE=db
      - MYSQL_ROOT_HOST="%"
    ports:
      - '3306:3306'
    expose:
      - '3306'
    networks:
      networkConnection:
        ipv4_address: 10.3.0.3
    volumes:
      - data:/dbData
```

Esto hará dos contenedores:
- Un contenedor que escucha por el puerto 3036 con la ip 10.3.0.3 en el cual se situará la base de datos de MYSQL.
- Un contenedor escuchando por el puerto 1880 con la ip 10.3.0.2 en el cual se situará Node-Red.

No obstante, aunque la base de datos se descargue una imagen de internet, NodeRed no se la descarga, sino que construye una imagen a partir de un Dockerfile situado en ./nodered.

Dentro de la carpeta 'nodered' encontraremos ente Dockerfile:

```ad-code
```yml
FROM nodered/node-red:latest

USER root
RUN npm install node-red-dashboard
RUN npm install node-red-node-mysql
USER node-red

CMD ["npm" , "start"]
```

Esto nos instalará los paquetes necesarios en Node-Red para poder ejecutar el flow con el programa.

Una vez se ha realizado estos dos archivos ejecutamos en la direccion del docker-compose.yml este comando:

`sudo docker-compose up -d`

Este comando ejecutará el compose (en segundo plano por el -d) y nos formará dos contenedores conectados por la misma red.

Ahora solo nos queda entrar en NodeRed y importar el flow. Si no estan las credeciales de la base de datos: `User: nodered | Password: nodered`

Ejemplo de ejecución:

Primero pulsaríamos el timestamp de CreateTable:

![[Pasted image 20230114164629.png|center]]

<center>Figura 1: Nodos del Ejercicio 1</center>

<center></center>

Después entramos en el Dashboard y rellenamos el formulario y le damos a submit:

![[Pasted image 20230114124343.png]]


Por último le damos al timestamp de Read DB y observamos la salida del Debug

<center>Figura 2: Formulario en el Dashboard</center>

![[Pasted image 20230114124433.png|center]]


## Ejercicio 2: Encuesta Enfermería

Adjuntado en la Entrega

## Ejercicio 3: Proyecto de Enfermería

Se ha adjuntado:
- PDF con el Proyecto
- Zip con el Proyecto
- Link a la presentación del Proyecto: https://drive.google.com/file/d/1YpL21151_dxVQTD03yevrZ2BSAiY2-QS/view?usp=sharing
- Github del Proyecto: https://github.com/DanielGS65/ThilaGrama