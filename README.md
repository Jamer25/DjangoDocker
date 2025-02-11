# DjangoDocker
Agregando docker a un proyecto inicial de django

# Primero necesitamos crear el entorno virtual
    python -m virtualenv venv 

# Ahora vamos activarlo:
    source venv/Scripts/activate

# Ahora si instalamos Django:
    pip install django
# Ahora me pedirá que actualicemos el pip
    python.exe -m pip install --upgrade pip

# Ahora iniciamos el proyecto de Django
    django-admin startproject drf .
El punto es para que se cree una carpeta en el mismo directorio

# Ahora creamos una aplicación:
(Recordamos que django la estructura que trabaja es proyecto y luego dentro de ese lo que queramos se llamarán app)

    django-admin startapp api

En el archivo settings.py agregamos:
    'api'
En:
    INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'api',//aquí agregamos api es la carpeta que se creó cuando creamos una app llamada api
    ]
# Ahora ya podemos comenzar a ejecutar nuestro proyecto de django:
    python manage.py runserver

# Ahora vamos a dockerizar
 
 Creamos un archivo Dockerfile en la raíz del proyecto
    Dockerfile
 Tenemos que colocar primero nuestra imagen base
 Podemos irnos al navegador a dockerhub
 y ahí seleccionar que imagen queremos

 # En el archivo Dockerfile:

    FROM 3.13.2-alpine3.21  
Sería nuestra imagen base alpine es una distribución ligera

# Mi directorio de trabajo donde estará ubicado

    WORKDIR /app

# Ahora ejecutar comando dentro del contenedor con:
    RUN apk update \
        && apk add --no-cache gcc musl-dev postgresql-dev python3-dev libffi-dev \
        && pip install --upgrade pip

# Ahora copio lo de mi directorio lo que se instaló a mi directorio de trabajo /app
    COPY ./requirements.txt ./

# Luego instalamos los requirements.txt

    RUN pip install -r requirements.txt

# Copiar todo de mi directorio a mi directorio de trabajo
    COPY ./ ./

# Ahora el comando de ejecutar el proyecto en Dockerfile
    Esto de 0.0.0.0:8000 abre una puerta de nuestra red por el puerto 8000 para que otros dispositivos se puedan conectar

    CMD ["python", "manage.py", "runserver" , "0.0.0.0:8000"]

# Instalamos los requirements.txt
    en la terminal:
    pip freeze > requirements.txt
# Todo completo sería:
    FROM python:3.13.2-alpine3.21
    WORKDIR /app



    RUN apk update \
            && apk add --no-cache gcc musl-dev postgresql-dev python3-dev libffi-dev \
            && pip install --upgrade pip

    COPY ./requirements.txt ./

    RUN pip install -r requirements.txt

    COPY ./ ./

    CMD ["python", "manage.py", "runserver" , "0.0.0.0:8000"]

# Ahora para construir el contenedor colocamos:
    docker build -t devjamer/docker-django .

# Ahora para ejecutar el contenedor colocamos:
    docker run devjamer/docker-django (no funcionará porque está compartiendo el puerto)
    docker run -p 8000:8000 devjamer/docker-django (este si funciona)

# Agregamos en dockerfile
    Para que se pueda ejecutar y mostrar logs así como con : python manage.py runserver

    En el archivo Dockerfile añadimos:
    ENV PYTHONUNBUFFERED=1

# Sería:
    FROM python:3.13.2-alpine3.21
    ENV PYTHONUNBUFFERED=1
    WORKDIR /app



    RUN apk update \
            && apk add --no-cache gcc musl-dev postgresql-dev python3-dev libffi-dev \
            && pip install --upgrade pip

    COPY ./requirements.txt ./

    RUN pip install -r requirements.txt

    COPY ./ ./

    CMD ["python", "manage.py", "runserver" , "0.0.0.0:8000"]

# Ahora por si quiero hacer un cambio y no correr de nuevo build

    docker run -v /c/Users/jamer/Desktop/Django/DjangoDocker:/app -p 8000:8000 devjamer/docker-django

# AHORA SI QUEREMOS QUE HAGA EL RUN EN SEGUNDO PLANO 
    Para que no te muestre los logs:

    docker run -d -v /c/Users/jamer/Desktop/Django/DjangoDocker:/app -p 8000:8000 devjamer/docker-django

# Y SI QUEREMOS VER EL LOG:
    docker logs --flow (el código)

# Para saber que contenedores se está ejecutando
    docker ps

# PARA PARAR EL PROCESO 
    docker stop (colocar el id del contenedor)

# PARA AGREGAR COMANDOS DENTRO DEL CONTENEDOR:
 docker exec -it (Colocas el id del contenedor) /bin/sh