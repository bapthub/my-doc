# Librairie dotenv pour la gestion de secret

## Installation

```shell=
pip install dotenv
```

## Création d'un fichier .env

Le fichier .env est à créer dans le dossier où les informations sont requises ou dans son dossier parent.
Contenu du fichier .env :

```.env=
user = "myuser"
password = "mypassword"
```

⚠️Lorsque qu'un fichier .env est présent dans le dossier parent et un autre dans le dossier courant, seul les informations du .env du dossier courant sont conservées.

⚠️Il ne faut pas push le fichier .env dans le repertoire git sinon la préservation des secrets est nulle. Ajouter le fichier .env dans le .gitignore pour éviter cela.

## Utilisation

```python=
from dotenv import load_dotenv

load_dotenv()
user = os.getenv("user")
password = os.getenv("password")
```
