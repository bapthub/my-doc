# How To : Extraire des données d'un fichier XML avec Python

## Introduction :

Pour extraire des données d'un fichier ou d'une page XML on peut utiliser 2 méthodes différentes ou les combinées.
La première se base sur la librairie [xml.etree.ElementTree](https://docs.python.org/3/library/xml.etree.elementtree.html) et qui consiste à voir l'architecture XML comme un arbre dont la racine est la première balise.
La seconde consiste à utiliser la librairie [pandas](https://pandas.pydata.org/) très utilisé dans le domaine de la donnée et plus particulièrement la fonction [read_xml](https://pandas.pydata.org/docs/reference/api/pandas.read_xml.html) apparue depuis peu au moment de l'écriture de ce document(version 1.3.0).
Après extraction les données seront transférées dans un dataframe pandas pour faciliter le traitement et pour faciliter la tranformation en fichier csv.

## Prérequis :

```shell=
pip install elementpath
pip install pandas
```

```python=
import xml.etree.cElementTree as et
import pandas as pd
```

### Méhode 1: xml.etree.ElementTree

L'idée principale consiste à ouvrir le fichier xml via la fonction parse de la librairie puis de parcourir l'arborescence en itérant sur les différents parties.

#### Etape 1 : Récupérer la racine

```python=
tree = et.parse(path_to_file)
root = tree.getroot()
```

#### Etape 2 : Repérer les données récherchées

```xml=
<employees>
    <employee>
        <name>Dave</name>
        <role>Sale Assistant</role>
        <age>34</age>
    </employee>
    <employee>
        <name>Robert</name>
        <role>Product Expert</role>
        <age>38</age>
    </employee>
    <employee>
        <name>Jane</name>
        <role>Marketing Manager</role>
        <age>31</age>
    </employee>
</employees>
```

Dans cet exemple j'ai pour objectif de récupérer les informations sur des employés : nom, role et age. Je remarque que tous les employés sont au même niveau et que leurs informations sont aussi sur un niveau à l'intérieur de la balise employee. 

#### Etape 3 : parcourir l'arborescence en observant la contenu et la valeur des différents niveau

Premier niveau depuis la racine

```python=
for child in root:
    print("tag:", child.tag)
    print("text:", child.text)
    print("attrib:", child.attrib)
```

résultat :
![res2](https://i.imgur.com/1I33rbm.png)

Deuxième niveau depuis la racine

```python=
for child in root:
    for sub_child in child:
        print("tag:", sub_child.tag)
        print("text:", sub_child.text)
        print("attrib:", sub_child.attrib,"\n")
```

![res2](https://i.imgur.com/gJFKQTp.png)

On a tous les données recherché de trouvé, il ne reste plus qu'à les stockées lors du parcours et de les mettre dans un dataframe.

#### Etape 4 : Parcourir l'arborescence en récupérant les valeurs et construire le dataframe

```python=
name,age,role = [],[],[] #lists to store data while iterate
for child in root:
    for sub_child in child:
        if sub_child.tag == "name":
            name.append(sub_child.text)
        if sub_child.tag == "age":
            age.append(sub_child.text)
        if sub_child.tag == "role":
            role.append(sub_child.text)

cols = ["name","age","role"] #define columns name of dataframe
df = pd.DataFrame({"name":name,"age":age,"role":role},columns=cols) #build df
```

Résultat :
![res3](https://i.imgur.com/zemZGWS.png)

### Méhode 2: pandas.read_xml

Cette méthode n'est réellement applicable que dans le cas d'une aborescence assez simple où les informations sont au même niveau comme dans l'exemple choisit. En effet la fonction créer un dataframe à partir des informations disponible à la racine ou au niveau indiqué. Dans le cas d'informations à plusieurs niveau de profondeur les informations seront ignorées. De plus si des champs comme employee n'ont pas de valeur "text" le dataframe aura créera des colonnes remplis de Nan.

#### Etapes 1 et 2 : identique à la méthode 1

#### Etape 3 : utiliser read_xml au niveau adéquat

```python=
df = pd.read_xml("C:/Users/data01/Desktop/vecto/data/test/oui/test.xml",xpath=".//employee") 
#xpath est optionnel dans cette situation car les éléments sont des fils direct de la racine
```

On obtient le même résultat qu'avec la première méthode :
![res4](https://i.imgur.com/zemZGWS.png)

![end](https://i.imgur.com/ky0nqeN.png)
