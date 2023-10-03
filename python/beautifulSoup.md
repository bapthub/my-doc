# How To : BeautifulSoup

## Récupérer toutes les zones de textes sur une page

```python=
import requests
from bs4 import BeautifulSoup

response = requests.get(url)
page = response.text
soup = BeautifulSoup(page, 'html.parser')
text = soup.find_all(text=True)   
```

## Récupérer tous les liens d'une page

```python=
url = "https://test.com"
response = requests.get(url)
page = response.text
soup = BeautifulSoup(page, 'html.parser')
for elm in soup.find_all('a'):
    link = str(elm.get('href'))
    #if re.match("^.*7z$",link):
```

Il peut être intéressant d'utiliser un regex pour récupérer un lien spécifique

## Récupérer une classe via son nom

```python
class = soup.find(class_="classe_name")
```

## Récupérer toutes les balises d'un certain type

```python
class = soup.find(class_="classe_name")
td = class.find_all('class_type') #ex : td
```

## Récupérer la classe suivante d'un certain type

```python
class = soup.find(class_="classe_name")
for elm in class:
    if condition:
        tmp = elm.findNext('class_type') #ex : td
```
