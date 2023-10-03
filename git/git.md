# Git

## Commandes

### Créer un repository git en ligne de commande

```shell
git init
git remote add origin repo_link
git branch -M main
```

### Branches

```shell
git branch <nomdebranche> 
# créer une branche
```

```shell
git checkout <nomdebranche> 
# changer de branche
```

```shell
git merge <nom de branche> 
# fusionner la branche courante avec la branche nommée
```

```shell
git pull 
# mettre à jour le code
```

```shell
git stash 
# mettre de côté des modifications
```

```shell
git stat list 
# liste les éléments mis de côtés avec la commande stash
```

```shell
git stash apply <nom stash> 
# ajoute les éléments mis de côté dans le stash
```

### Commit et ajout de fichier

```shell
git commit --amend -m <nouveau message de commit> 
# change le message du dernier commit
```

Ajouter des fichiers dans un commit ou les modifier :

1) git add le fichier
2) git commit --amend --no-edit #ajoute le fichier au commit

### Revenir en arrière

```shell
git reset --hard HEAD^ 
# reviens au commit précédent
```

```shell
git reset <hash du commit> <style de reset> 
# reviens au commit du hash (à récupérer avec git log)
```

#### Styles :

--hard : supprime tout ce qui a été fait après le commit cible

--mixed : ne supprime pas les modifications en cours, enlève les fichiers git, supprime les commit suivant le commit cible

--soft : ne supprime aucun fichier, aucun commit après celui ciblé

```shell
git revert HEAD^ 
# annule le dernier push et en créer un nouveau
```

```shell
git revert <hash commit> 
# annule le commit et en fait un nouveau
```

```shell
git reset <hash du commit> <style de reset> 
# reviens au commit du hash (à récupérer avec git log)
```

### Autres

```shell
git blame <nom de fichier> 
# donne le nom de tous ceux qui ont modifiés le fichier 
```

```shell
git cherry cherry-pick <hash du commit> 
# merge un commit donné avec la branche courante
```
