# Docker_Lab_01 - Creamos una imagen y la corremos

Este es un laboratorio introductorio crearemos una imagen y luego la ejecutaremos
La finalidad es que el estudiante comprenda como armar una imgaen y ejecutarla

## 1 Iniciando el laboratorio

Ingresamos en la VM que contiene instalado Docker. La IP a sido brindada por su instructor. 

```bash
ssh ip_virtual@alumno
o
también podemos ingresar dentro del escritorio y abrimos un terminal
```
----
### Datos para ingresar 

username  **alumno \
passwword 
```bash
cd Documentos
mkdir Clase_12_Contenedores
cd  Clase_12_Contenedores

git clone https://github.com/luisamato/Docker_Lab_01

cd Docker_Lab_01/image

more Dockerfile
```

---

## 2 Archivos

Archivo **DockerFile** [Este es un archivo que define como se crea una imagen]

```docker
# Use an official Python runtime as a parent image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install --trusted-host pypi.python.org -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]

````

### Archivo **requierement.txt**
````bash
Flask
Redis
````

### Archivo **app.py**

```python
from flask import Flask
from redis import Redis, RedisError
import os
import socket

# Connect to Redis
redis = Redis(host="redis", db=0, socket_connect_timeout=2, socket_timeout=2)

app = Flask(__name__)

@app.route("/")
def hello():
    try:
        visits = redis.incr("counter")
    except RedisError:
        visits = "<i>cannot connect to Redis, counter disabled ...  esta la base ... </i>"

    html = "<h3>Hello {name}!</h3>" \
           "<b>Hostname:</b> {hostname}<br/>" \
           "<b>Visits:</b> {visits}"
    return html.format(name=os.getenv("NAME", "world"), hostname=socket.gethostname(), visits=visits)

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
````

## 3. Construimos la imagen
### Miramos los archivos del directorio
```bash
#ls
Dockerfile                app.py           requirements.txt
````
Ejecutamos el siguiente comando para armar la imagen
```bash
#sudo docker build -t primer_imagen_alumno:01 .
````
## 4. Ejecutamos
### Ejecutamos el container
```bash
#sudo docker run -p 8000:80 --name primer_container_luis primer_imagen_alumno:01
````

### Probamos
```
Con cualquier browser nos dirigimos a 
http://IP_DEL_HOST:8000
```

---
## 5. Subimos la imagen a docker hub
### Subimos la imagen a docker hub
*Es importante mencionar qe previo a este paso se debería crear una cuenta en DockerHub
```bash
#sudo docker login

#sudo docker image
````

Le damos un tag a la imagen recien creada 
```docker
#sudo docker tag primer_imagen_alumno:01 luisamato/primer_imagen_alumno:01

#sudo docker push luisamato/primer_imagen_alumno:01

```

Referencia : https://ropenscilabs.github.io/r-docker-tutorial/04-Dockerhub.html

----