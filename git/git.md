# Git

## Commandes

### Créer un repository git en ligne de commande

```shell
git init
git remote add origin repo_link
git branch -M main
```

### Branches

Créer une branche

```shell
git branch <nomdebranche> 
```

Changer de branche

```shell
git checkout <nomdebranche> 
```

Fusionner la branche courante avec la branche nommée

```shell
git merge <nom de branche> 
```

Mettre à jour le code

```shell
git pull 
```

Mettre de côté des modifications

```shell
git stash 
```

Liste les éléments mis de côtés avec la commande stash

```shell
git stat list 
```

Ajoute les éléments mis de côté dans le stash

```shell
git stash apply <nom stash>
```

### Commit et ajout de fichier

Change le message du dernier commit

```shell
git commit --amend -m <nouveau message de commit> 
```

Ajouter des fichiers dans un commit ou les modifier :

1) git add le fichier
2) git commit --amend --no-edit #ajoute le fichier au commit

### Revenir en arrière

Reviens au commit précédent

```shell
git reset --hard HEAD^ 
```

Reviens au commit du hash (à récupérer avec git log)

```shell
git reset <hash du commit> <style de reset> 
```

#### Styles :

--hard : supprime tout ce qui a été fait après le commit cible

--mixed : ne supprime pas les modifications en cours, enlève les fichiers git, supprime les commit suivant le commit cible

--soft : ne supprime aucun fichier, aucun commit après celui ciblé

Annule le dernier push et en créer un nouveau

```shell
git revert HEAD^ 
```

Annule le commit et en fait un nouveau

```shell
git revert <hash commit> 
```

Reviens au commit du hash (à récupérer avec git log)

```shell
git reset <hash du commit> <style de reset> 
```

### Autres

Donne le nom de tous ceux qui ont modifiés le fichier

```shell
git blame <nom de fichier>  
```

Merge un commit donné avec la branche courante

```shell
git cherry cherry-pick <hash du commit> 
```
