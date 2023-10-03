# Docker commannds

## Images

Liste les images disponibles

```shell
docker images ls
```

Télécharger une image docker depuis le dockerhub

```shell
docker pull <imagename>
```

Lance l’image docker en mode interactif

```shell
docker run -it <imagename>
```

options :
-d : lancer l’image en arrière-plan
-p : défini l’utilisation des ports

Arrête le conteneur

```shell
docker stop <imagename>
```

## Conteneurs

Liste les conteneurs disponibles

```shell
docker ps
```

Supprime tous les conteneurs qui ne sont pas en cours d'utilisation, tous les réseaux non utilisés par au moins un conteneur, supprime le cache

```shell
docker system prune
```
