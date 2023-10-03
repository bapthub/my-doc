# How To : astuces_python

## **String**

### **Combler un code de N chiffres avec des 0**

```python=
x = "1"
x = x.zfill(2) #remplie de 0 la chaine jusqu'à avoir une longueur de 2 
```

### Séparer un bloc de texte avec des retour à la ligne en liste de lignes

```python
texte = "blabla\nblab\nblabla
texte_en_ligne = texte.split("\n")
```

### Transformer des coordonnées heures minutes secondes en longitude/latitude (WGS84)

```python=
from dms2dec.dms_convert import dms2dec

string = """45°58'30"N"""
long_lag = dms2dec(long) #résultat : 45.975
```

## Listes

### Supprimer une liste d'index dans une liste : 

```python=
l_pop = [0,3,5,6]
    for elm in sorted(l_pop,reverse=True):
        l.pop(elm)
```

## Erreur management

### Faire une boucle sur une erreur ou une excpetion jusqu'à la résoudre

```python 
for i in range(10):
    try:
        #something
    except Exception:
        continue
    break
```

Dans cet exemple on essaye 10 fois de résoudre l'exception puis on abandonne, mais il est possible de faire une boucle infini pour être sur de résoudre le problème

### Afficher le code erreur d'un try catch

```python
try:
    #something
except Exception as e:
    print(str(e))
```

### Tester si une variable est instantiée

```python=
try:
    nomdevariable
except Exception as e:
    #do something else
```

## Téléchargement sur Internet : 

### Télécharger un fichier simple :

```python=
import requests

path = "file/save/path"
url = "https/file_to_download"
reponse = requests.get(url)
if reponse.status_code == 200:
    open(path,'wb').write(reponse.content)
```

### Télécharger un dossier (compressé ou non) : 

```python=
import wget
import requests

path = "path/to/save/file"
url = "https/file_to_download"
reponse = requests.get(url)
    if reponse.status_code == 200:
        wget.download(url, path)
```

### Télécharger un dossier compressé et le dézipper au téléchargement

```python=
from io import BytesIO
from zipfile import ZipFile
from urllib.request import urlopen

with urlopen(url) as zipresp:
    with ZipFile(BytesIO(zipresp.read())) as zfile:
        zfile.extractall(self.save_path)
```

### Télécharger sans certificat de connexion: 

```python=
import ssl
import requests

ssl._create_default_https_context = ssl._create_unverified_context
reponse = requests.get(url,verify=False)
```

### Découper un URL sur 2 lignes sans le briser

```python=
url = "https://google" \
                    ".com"
```

## Librairies

### Masquer les warnings des librairies :

```python=
import warnings

warnings.filterwarnings("ignore")
```

Spécifier le type de warnings a filtrer

```python=
import warnings
from shapely.errors import ShapelyDeprecationWarning

warnings.filterwarnings("ignore", category=ShapelyDeprecationWarning)
```


## Gestion de fichier 

### Créer un fichier vide en une ligne :

```python=
with open('/tmp/test', 'w'): pass
```

### Créer un dossier

```python=
os.makedirs("/directory")
```

### Tester si un fichier ou un dossier existe :

```python=
import os

if not os.path.exists('/tmp/test'):
```

### Dézipper un fichier :

```python=
import zipfile

if zipfile.is_zipfile("path/to/file"):
    with zipfile.ZipFile("path/to/file","r") as item:
        item.extractall(new_save_path)
```

### Dézipper un fichier 7zip :

```python=
import py7zr

file = "/save/path/file.7z"
save path = "/save/path"
if file.endswith(".7z"):
    with py7zr.SevenZipFile(file,"r") as item:
        item.extractall(save_path)
```

### Dézipper un fichier gzip :

```python=
import gzip
import shutil

with gzip.open(file_path, 'rb') as f_in:
    with open(unzip_file_path, 'wb') as f_out:
        shutil.copyfileobj(f_in, f_out)
```

### Déplacer un fichier/dossier : 

```python=
shutil.move(old_path, new_path)
```

### Supprimer un fichier / un dossier vide

```python=
import os

os.remove(path_to_file_or_directory)
```

### Supprimer une arborescence de dossiers/fichiers :

```python=
import shutil

shutil.rmtree("directory/to/delete")
```

### Supprimer des fichiers avec l'aide d'un regex :

```python=
import os
import re

for file in os.listdir(save_path):
    if not re.match("presentation.*",file):
        os.remove(save_path + "/" + file)
```
