---
layout: post
title:  "Input json"
date:   2021-04-04 19:09:00 +0100
categories: tips
comments: true 
---

J'essaye de prendre au maximum l'habitude de séparer la logique d'un script et les inputs ou les paramètres de configuration.

<!--more-->

Cela permet de rendre le code plus lisible et plus adaptable.
Le développement d'un petit jeu avec `pygame` est un bon exemple.
Si par exemple on a une classe pour créer l'interface graphique.
Au lieu de renseigner dans le script les inputs tels que la largeur de la fenêtre ou le nombre de FPS,
on peut utiliser un fichier d'input `json`

<script src="https://gist.github.com/pacourbet/78bafc285018022ea8ea51a149394d7f.js"></script>

Si on veut modifier le nombre de FPS par exemple ou la position initiale d'un joueur, pas besoin d'ouvrir le code, il suffit de modifier le fichier d'input. On peut aussi imaginer vouloir tester plusieurs configurations. Il suffira alors de préparer plisieurs fichiers d'inputs et importer le bon fichier.
