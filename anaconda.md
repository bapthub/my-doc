# How To : Anaconda

## Installer Miniconda

Je recommande d'installer miniconda plutôt qu'Anaconda, miniconda est une version légère d'Anaconda qui ne contient que le gestionnaire de dépendance et l'interpréteur Python sans le lot de librairies préinstallées.

Télécharger l'installeur pour l'OS désiré : <https://docs.conda.io/en/latest/miniconda.html>

Lancer la commande :

```shell=
bash Miniconda3-latest-Linux-x86_64.sh
```

Suivre les instructions et relancer son terminal pour terminer l'installation.
Votre terminal devrait ressembler à ça si l'installation à réussi :

```shell=
(base) baptiste@LAPTOP:~$
```

## Créer un environnement virtuel Conda

```shell=
conda create --name <environment_name>
```

Variante pour créer l'environment à partir d'un fichier requirements

```shell=
conda create --name <environment_name> --file requirements.txt
```

## Activer un environnement Conda

```shell=
conda activate <environment_name>
```

## Lister les environnements Conda

```shell=
conda env list
```

## Supprimer un environnement Conda

```shell=
conda remove -n <environment_name> --all
```

## Désactiver un environnement Conda

```shell=
conda deactivate
```

## Mettre à jour tous les packets d'un environnement

```shell=
conda update --all
```

## Importer un environnement pip sur Conda

```shell=
conda create --name myenv
conda activate myenv
pip install -r requirements.txt
```

## Exporter un environnement anaconda pour pip

Il faut avoir pip d'installé dans l'environnement anaconda.

```shell=
conda install pip
```

```shell=
pip list --format=freeze > requirements.txt
```

💡Il est possible que la commande pip soit par défaut liée au pip par défaut de Python même si l'environment conda est activé. Si c'est le cas les librairies affichées par la commande pip list ne correspondent pas à celle de l'environment conda.

Pour vérifier cela :

```shell=
conda activate <environment_name>
which pip 
```

Lorsque la commande which pip ne pointe pas sur le pip de l'environment comme ci dessous :

```shell=
~/.local/bin/pip
```

Il peut être utile d'ajouter la fonction et l'alias ci-dessous dans son bashrc afin d'avoir une commande pip pour les environnements conda

```bash=
#do the pip inside the conda environment which is first argument
conda_pip()
{
    local env_name="$1"
    shift
    ~/miniconda3/envs/"$env_name"/bin/pip "$@"
}

alias condap=conda_pip
```

Remarque : cette fonction marche si vous avez choisi votre home directory comme emplacement pour miniconda.

## Exporter un environnement Conda

1)

```shell=
conda env export > env.yml
```

2)

```shell=
conda env create --file env.yml
```

OU

1)

```shell=
conda list -e > requirements.txt
```

2)

```shell=
conda create --name <env> --file requirements.txt
```
