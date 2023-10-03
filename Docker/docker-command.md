# Docker commannds

## Images

```shell
docker images ls # liste les images disponibles
```

```shell
docker pull <imagename> # télécharger une image docker
```

```shell
docker run -it <imagename> # lance l’image docker en mode interactif
```

options :
-d : lancer l’image en arrière-plan
-p : défini l’utilisation des ports

```shell
docker stop <imagename> # stop le conteneur
```

## Conteneurs

```shell
docker ps # liste les conteneurs disponibles
```

```shell
docker system prune # supprime tous les conteneurs qui ne sont pas en cours d'utilisation, tous les réseaux non utilisés par au moins un conteneur, supprime le cache
```
