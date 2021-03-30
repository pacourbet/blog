---
layout: post
title:  "Quick read"
date:   2021-03-07 14:00:00 +0100
categories: tips
comments: true 
---

J'ai souvent besoin de lire de gros fichiers de données. Un gros fichier Excel par exemple.

<!--more-->

C'est très simple:

```python
import pandas as pd
  
df = pd.read_excel("./my_excel_file.xlsx")

```

Pas de souci. L'ennui c'est que quand le fichier est lourd, ça prend une plombe à ouvrir.
Du coup j'utilise souvent le stockage en  binaire, c'est pas forcément un gain de place (en tout cas de la manière dont je le fais) mais un gain de temps de lecture énorme.
Voilà le genre de fonction que j'utilise pour ce genre d'optimisation


```python
def read_data(path_file):

    try:

        df = pd.read_pickle(path_file[:-5] + ".pkl")
        print("[+] read from pickle")

    except FileNotFoundError:

        print("[*] read from excel and creating pkl. Next time will be much faster")
        df = pd.read_excel(path_file)
        df.to_pickle(path_file[:-5] + ".pkl")

    return df
```
