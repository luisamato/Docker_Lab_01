# Docker_Lab_01 - Creamos una imagen y la corremos

Este es un laboratorio introductorio crearemos una imagen y luego la ejecutaremos
La finalidad es que el estudiante comprenda como armar una imgaen y ejecutarla

## 1) Iniciando el laboratorio

Ingresamos en la VM que contiene instalado Docker. La IP a sido brindada por su instructor. 

```bash
ssh ip_virtual@alumno
```
----
### Datos para ingresar 

username  **alumno \
passwword 
```bash
sudo -i 
cd Documentos
git clone https://github.com/luisamato/Docker_Lab_01
```

---

## 2 Dockerfile

Este es un archivo que define como se crea una imagen

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
## Archivos
Ademas del archivo recién creado agregaremos 2 archivos
**requirement.txt** que contiene libreriás que instalará python cuando corra el script Dockerfile

### Archivo requierement.txt
````bash
Flask
Redis
````

### Archivo app.py

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
#docker build -t primer_imagen_luis:01 .
````
## 4. Ejecutamos
### Ejecutamos el container
```bash
#docker run -p 8000:80 --name primer_container_luis primer_imagen_luis:01
````

### Probamos
Con cualquier browser nos dirigimos a 
http://IP_DEL_HOST:8000


---
## 5. Subimos a docker hub
### Subimos el container a docker hub
```bash
#docker login
````
Le damos un tag a la imagen recien creada 
```
#docker tag  bb38976d03cf yourhubusername/verse_gapminder:firsttry
```
**bb38976d03cf** es el id de la imagen\
**yourhubusername** de dockerhub\
**verse_gapminder:firsttry** el nombre de la imagen

Referencia : https://ropenscilabs.github.io/r-docker-tutorial/04-Dockerhub.html

----