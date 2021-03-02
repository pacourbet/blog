---
layout: post
title:  "Vitesse d'algorithme"
date:   2021-02-07 16:03:33 +0100
categories: algorithme
comment: true
---

**Table des matières**
- [Description](#description)
- [Références](#références)
- [Introduction](#introduction)
- [Logarithme et notation « O »](#logarithme-et-notation-o)
  - [Logarithme](#logarithme)
  - [La notation « Big O »](#la-notation--bigo)
    - [Algorithme](#algorithme)
    - [Première approche](#première-approche)
    - [Seconde approche](#seconde-approche)
- [Conclusion](#conclusion)

# Description

Comment mesurer la vitesse d’un algorithme ?

# Références

**grokking algorithms** | Aditya Y.Bhargava


# Introduction

> La vitesse d’un algorithme ne se mesure pas en secondes mais en croissance du nombre d’opérations. On cherche plutôt à savoir comment le temps d’exécution augmente avec l’augmentation du nombre d’input.
> 
> --- <cite>[Aditya Y.Bhargava](#références)</cite>

Jamais je n’avais bien saisi les notions de mesure de vitesse d’algorithmes jusqu’à ce que je lise [grokking algorithms](#références)

# Logarithme et notation « O »

## Logarithme

Comprendre le logarithme est important car la croissance logarithmique apparaît souvent dans les algorithmes (en particulier le $$Log_2$$).

L’exemple du logarithme permet aussi d’illustrer la mesure de la croissance en comparaison avec celle d’un logarithme dont le temps d’exécution augmente linéairement avec le nombre d’input.

$$Log_n(x) = ln(x)/ln(n)$$

La fonction $$Log$$ donne la puissance à laquelle a été élevé un nombre.

$$Log_2(8)= {ln(8)}/{ln(2)}$$

$$= {ln(2^3)}/{ln(2)}$$

$$= {3*ln(2)}/{ln(2)} = 3$$

## La notation « Big O »

Toujours dans [grokking algorithms](#références) on trouve un exemple parlant:

### Algorithme

Imaginons un algorithme qui vise à séparer une feuille de papier en 16 cases.

### Première approche

La première approche consiste à dessiner chaque case. Dessiner une case correspond à une action (soit une opération dans l’équivalent machine).

Devoir dessiner $$n$$  case nécessite donc de faire $$n$$  opérations. Avec la notation **"Big 0"** la vitesse de cet algorithme est noté $$ O(n) $$

Le nombre d’opérations à faire augmente linéairement avec le nombre d’inputs (ici un input est le nombre d’opérations à réaliser).

### Seconde approche

Une seconde approche plus fine pour dessiner les cases est de plier la feuille. Si on plie une fois la feuille, on a 2 cases. Si on la plie une deuxième fois on obtient 4 cases. On replie une troisième fois notre feuille déjà pliée deux fois et obtient 8 cases. On voit bien la croissance en $$Log_2$$ apparaître:

- Combien de fois j’ai dû plier ma feuille en 2 déjà pliée pour obtenir 8 cases ?
  - → 3 fois
- Pour 16 cases ?
  - → 4 fois.

Si je veux $$n$$ cases je dois réaliser $$Log_2(n)$$ opérations. La croissance de cet algorithme est donc en $$O(Log(n))$$

Avec nos processerus actuels, l’efficacité du second algorithme ne se verra qu’à partir d’un $$n$$ suffisamment élevé.

<img src="{{site.baseurl}}/assets/images/posts/vitesseAlgorithme/increase.png" alt="increase comparison">

# Conclusion

La mesure de la vitesse d’un algorithme ne se fait pas de manière absolue en mesurant la vitesse d’exécution de ce dernier. Il faut voir comment évolue le nombre d’opérations avec le nombre d’opérations  à réaliser.

Avec nos deux exemples, Si je veux 16 cases je compare 16 opérations Vs 4 opérations.
