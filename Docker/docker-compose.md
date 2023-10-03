# Créer un docker compose de A à Z

## Préambules

Les noms des variables entre "[ ]" sont données à titre indicatif et doivent être remplacés.

## Section 1 : Version

Permet de spécifier la version de docker compose utilisée.

```Dockercompose
version: "3.7"
```

## Section 2 : Services

Permet de créer plusieurs conteneurs en lançant une seule commande. Il est possible de les configurer dans le détail et de les faire communiquer entre eux.

```Dockercompose
services:
  [service1_name]:
    [...]
  [service2_name]: 
    [...]
  #ex :
  postgres:
    [...]
```

### Partie 1 : Container_name

Permet de spécifier le nom du conteneur du service.

```Dockercompose
services:
  service1_name:
    container_name: [container_name] 
    #ex :
    container_name: python
```

### Partie 2 : Image

Permet de séléctionner l'image à récupérer sur le docker hub, celle-ci sert de base pour créer le conteneur. Partie facultative dans le cas ou celle-ci est spécifiée dans un dockerfile dans la partie build.

```Dockercompose
service_name:
  image: [image_name] 
  #ex :
  image: postgis/postgis:13-3.3
```

### Partie 3 : Build

Permet de spécifier le chemin d'un dossier contenant les éléments constitutifs d'un conteneur (dockerfile, fichier environment ...)

```Dockercompose
service_name:
  build: [path_to_build_foler] 
  #ex :
  build: app
```

### Partie 4 : Environment

Permet de définir des varibales d'environnements dans le conteneur.

```Dockercompose
service_name:
  [...]
  environment:
    [environment_var_name1]: [environment_var_value1]
    [environment_var_name2]: [environment_var_value2]
    #ex : 
    POSTGRES_USER: postgres
  [...]
```

### Partie 5 : Port

Permet de définir les ports d'entrés et de sorties du conteneur.

```Dockercompose
service_name:
  [...]
  ports:
    - [host_port]:[container_port]
    #ex : 
    - 5433:5432
    [...]
```

### Partie 6 : Volume

Permet de spécifier un espace de stockage dans le conteneur qui sera également conservé dans la machine hote.

```Dockercompose
service_name:
  [...]
  volumes:
    #option 1:
    - [volume_name]:[volume_path_container]
    #ex : 
    - data-pipeline:/var/lib/postgresql/data

    #option 2:
    - [volume_path_host]:[volume_path_container]
    #ex : 
    ./schema.sql:/docker-entrypoint-initdb.d/schema.sql
  [...]
```

#### Note :

Il peut y avoir plusieurs volumes dans cette section.
./ est le répertoire dans lequel la commande docker compose est lancée.

### Partie 7 : Networks

Permet de spécifier l'adresse IP du conteneur du service dans le réseau créé.

```Dockercompose
service_name:
  [...]
  networks:
    [network_name]:
      ipv4_address: [ipv4_adress_service]
      #ex : 
      ipv4_address: 171.10.0.3
  [...]
```

### Partie 8 : Depends_on

Permet de lancer le conteneur après la création d'un autre conteneur. Très pratique pour lancer une application après que la base de donnée soit rendue disponible.

```Dockercompose
services:
  [postgresql]:
    [...]
  [python]: 
    [...]
    depends_on:
      #option 1:
      - [service_name] 
      #ex : 
      - postgresql

      #option 2: effectue plus de vérification (cf section 8)
      [service_name]:
        condition: service_healthy
      #ex : 
      postgresql:
        condition: service_healthy 
    [...]
```

#### Note :

depends_on ne suffit pas toujours à attendre qu'une base soit opérationnelle. En effet, le conteneur de la base de donnée peut être lancé mais la base peut ne pas être encore prête à recevoir des connexions.

### Partie 8 : Healthcheck (docker compose version>=2.10.2)

Permet de vérifier le statut d'un conteneur afin d'organiser le lancement de différents conteneurs.

```Dockercompose
service_name:
  [...]
  healthcheck:  
    test: ["CMD", "command"]
    interval: [nb_seconds]
    timeout: [nb_seconds]
    retries: [nb_retries]

    #ex :
    test: [ "CMD", "pg_isready", "-q", "-d", "data", "-U", "postgres" ]
    interval: 10s
    timeout: 5s
    retries: 12
  [...]
```

### Partie 9 : Restart

Permet de définir pour le comportement au conteneur en cas d'arrêt. Si la condition est satisfaite le conteneur sera relancer.
liste des types: always, on-failure[:nb_retries], unless-stopped

<https://docs.docker.com/config/containers/start-containers-automatically/>

```Dockercompose
[service_name]:
  [...]
  restart: [restart_type]
  #ex :
  restart: always
  [...]
```

## Section 3 : Networks

Permet de créer un réseau interne pour faire communiquer plusieurs conteneurs entre eux.

```Dockercompose
[...]
networks:
  network_name_compose: #this name is for the docker compose
  #ex : 
  network:
    name: [network_name_portainer] 
    #ex :
    name : pipeline-network
    driver: [network_type] #ex bridge
    ipam:
      config:
        # ipv4/ipv6
        - subnet: [adresse_ip/masque_sous_reseau ] #ex : 171.10.0.1/24
[...]   
```

### Connecter un conteneur à un réseau existant

```Dockercompose
[...]
networks:
  [network name]:
    external: true
[...]   
```

### Note :

Il existe différents types de driver, il est même possible de faire des driver personnalisés.

```Dockercompose
[...]
networks:
  [network_name]:
    driver: [custom-driver-2]
    driver_opts:
      [option1]: [option_val]
[...]
```

Pour se connecter a un réseau externe préexistant:

```Dockercompose
[...]
networks:
  default:
    name: [my-pre-existing-network]
    external: true
[...]
```

## Section 4 : Volumes

Permet de stocker une partie ou la totalité du conteneur en local sur la machine hote.

```Dockercompose
[...]
volumes: 
  [volume_name]:
[...]
```

Il est possible de connecter un volume déja créé auparavant :

```Dockercompose
[...]
volumes: 
  [volume_name]:
    external: true
[...]
```

Il est possible de spécifier le chemin du volume dans le machine hote ainsi que d'autres options.

```Dockercompose
[...]
volumes:
  [volume_name]:
    driver_opts:
      type: none
      device: [path_in_host] #chemin absolu uniquement
      #ex :
      device : /home/full/path
      o: bind
[...]
```

## Section 5 : Ressources

Permet de spécifier des limites et/ou de réserver des ressources pour le conteneur sur la machine hote.

```Dockercompose
[...]
deploy:
  resources:
    limits: 
      cpus: [cpus_limit_val] 
      #ex : 
      cpus: '0.001'
      memory: [ram_limit_val]
      #ex : 
      memory: 50M
    reservations:
      cpus: [cpu_reservation_value]
      #ex : 
      cpus: '0.0001'
      memory: [memor_reservation_value]
      #ex : 
      memory: 20M
[...]
```

### Note :
La valeur pour l'utilisation du cpu peut être correspond au % utilisation d'un seul coeur de processeur.  
Exemple :  
0,5 correspond à 50% d'un coeur de processeur  

La valeur pour l'utilisation de la ram peut être spécifiée dans plusiurs unités : 

* 2b : octets
* 1024kb : Kilooctets
* 300m : Megaoctets
* 1gb : Gigaoctets

source : <https://docs.docker.com/compose/compose-file/compose-file-v3/#resources>

## Exemple complet

Permet de créer une pipeline de données entre un conteneur python qui execute du code et une base postgresql qui stocke les données.

```Dockercompose
version: "3.7"

networks:
  pipeline:
    name: pipeline-network
    driver: bridge
    ipam:
      config:
        - subnet: 171.10.0.1/24

services:
  postgresql:
    container_name: postgres
    image: postgis/postgis:13-3.3
    environment:
      PGDATA: /data/postgres
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: XXXXX
      POSTGRES_DB: DB
    ports:
      - '5433:5432'
    volumes:
      - data-pipeline:/var/lib/postgresql/data
      - ./schema.sql:/docker-entrypoint-initdb.d/schema.sql
    healthcheck:  
      test: [ "CMD", "pg_isready", "-q", "-d", "data", "-U", "postgres" ]
      interval: 10s
      timeout: 5s
      retries: 12
    networks:
      pipeline:
        ipv4_address: 171.10.0.3
    restart: always

  python:
    container_name: python
    build:
      context: .
      dockerfile: Dockerfile.python
    depends_on:
      postgresql:
        condition: service_healthy
    environment:
      POSTGRESQL_HOST: 171.10.0.3
      POSTGRESQL_USER: postgres
      POSTGRESQL_PASSWORD: XXXXX
      POSTGRESQL_DB: DB
    networks:
      pipeline:
        ipv4_address: 171.10.0.2
    deploy:
  resources:
    limits:  
      cpus: '0.001'
      memory: 50M
    reservations:
      cpus: '0.0001' 
      memory: 20M
  
volumes: 
  data-pipeline:
```
