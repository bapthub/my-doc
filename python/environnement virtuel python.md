# How To : virtuelenv python

## Créer l'environnement virtuelenv

```shell=
virtualenv -p python folder_to_save_virtualenv
```

## Activer l'environnement virtuelenv

```shell=
source folder_to_save_virtualenv/bin/activate
```

## Sauvergarder les installations faites avec pip dans l'environnement virtuelenv

Nécessite d'avoir aciver l'environnement virtuelenv

```shell=
pip freeze > requirements.txt
```

## Installer les packages

Nécessite d'avoir aciver l'environnement virtuelenv

```shell=
pip install -r requirements.txt
```

## Sortir de virtuelenv

```shell=
deactivate
```
