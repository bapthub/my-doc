# Créer son Dockerfile de A à Z

## Préambules

Les noms des variables entre "[ ]" sont données à titre indicatif et doivent être remplacées.
Les sections énoncées dans ce tutoriel peuvent être utilisées plusieurs fois dans l'odre que vous voulez. Exception pour FROM qui doit être la première sections et ENTRYPOINT/CMD la dernière,tout les trois ne peuvent être appelées qu'une seule fois.

## Partie 1 : Créer son Dockerfile

### Section 1 : FROM

Définit l’image utilisée comme base et récupérée depuis de le dockerhub.

```Dockerfile
FROM [image_name]
#ex : 
FROM python:3.10.7
```

### Section 2 : RUN

Permet de lancer une commande dans le conteneur.

```Dockerfile
RUN [command]
#ex : 
RUN pip install --upgrade pip
```

### Section 3 : COPY / ADD

Permet de copier des fichiers et ou dossiers depuis la machine hôte dans le conteneur.

```Dockerfile
COPY [path_in_host] [path_in_container]
ADD [path_in_host] [path_in_container]
#ex :
COPY ./requirements.txt /data/requirements.txt
```

différence entre add et copy :

<https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#:~:text=ADD%20or%20COPY&text=COPY%20only%20supports%20the%20basic,rootfs.tar.xz%20%2F%20.>

### Section 4 : Workdir

Permet de définir le chemin par défaut dans le conteneur, toutes les autres commandes seront effectuées à partir de ce point ensuite.

```Dockerfile
WORKDIR [path]
#ex :
WORKDIR /data
```

### Section 5 : EXPOSE

Permet d'exposer un port de conteneur.

```Dockerfile
EXPOSE [port_number]
#ex :
EXPOSE 8000
```

### Section 6 : Volume

Permet de définir le chemin d'un volume pour le conteneur.

```Dockerfile
EXPOSE [path_to_volume]
#ex :
VOLUME /app/data
```

### Section 7 : ENTRYPOINT / CMD

Permet de lancer une commande au lancement du conteneur.

```Dockerfile
ENTRYPOINT ["[command]", "[command_option]"]
CMD ["[command]", "[command_option]"]

#ex :
ENTRYPOINT ["python", "/src/script.py"]
CMD ["python", "/src/script.py"]
```

différence entre cmd et entrypoint :

<https://aws.amazon.com/fr/blogs/france/demystifier-entrypoint-et-cmd-dans-docker/>

#### Note :

Changer la langue du conteneur :

```Dockerfile
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y locales

RUN sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen && \
    dpkg-reconfigure --frontend=noninteractive locales && \
    update-locale LANG=en_US.UTF-8

ENV LANG en_US.UTF-8 
ENV LANGUAGE en_US.UTF-8 
ENV LC_ALL en_US.UTF-8 
```

### Exemple complet

Permet de créer  un conteneur python, d'installer des libraires, de copier du code dans le conteneur et de lancer un script python.

```Dockerfile
FROM python:3.10.7 #va chercher une image python 3.10.7
RUN pip install --upgrade pip #install pip

RUN useradd -m myuser
USER myuser
WORKDIR /data

COPY ./requirements.txt /data/requirements.txt
RUN pip install -r requirements.txt
COPY . .

ENTRYPOINT ["python", "/src/script.py"]
```

## Partie 2 : Build le conteneur

```shell
docker build -t test-example:latest .
```

## Partie 3 : Lancer le conteneur

```shell
docker run --name test --rm -d -p 80:8080 test-example:latest
```
