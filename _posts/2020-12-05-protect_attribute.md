---
layout: post
title:  "Protéger ses attributs"
date:   2020-12-05 15:03:33 +0100
comment: true
---

Comment protéger les attributs d'une classe ?

<!--more-->

# Protéger un attribut

Immaginons qu'on ait une classe `Rectangle`

```python
class Rectangle:
    
    def __init__(self, largeur, longueur):
        
        self.largeur = largeur
        self.longueur = longueur
        self.area = self.largeur * self.longueur
    
    def __repr__(self):
        return """rectange:
            largeur:{} * longueur: {} -> area: {}""".format(self.largeur,
                                                            self.longueur,
                                                            self.area)
```

On peut créer un object rectangle avec une `largeur` et une `longueur`:

```python
>>> largeur, longueur = 2, 10
>>> r = Rectangle(largeur, longueur)
>>> r
rectange:
            largeur:2 * longueur:10 -> area: 20
```

Ok. Et si on veut accéder à un attribut on fait simplement:

```python
>>> r.largeur
2
>>> r.area
20
```

Le problème c'est qu'un utilisateur peut changer l'attribut `area` qui est un output de la classe.

```python
>>> r.area = 45
>>> r
rectange:
            largeur:2 * longueur:10 -> area: 45
```

Et du coup c'est faux !
Il faut interdire à l'utilisateur la possibilité d'attribuer une valeur à l'attribut `area`. Pour faire ça on utilise une variable ***privé***. Même si en Python ce n'est qu'une histoire de convention mettre un `_`, siginifie que l'attribut n'est pas sensé être utilisé en dehors de la classe. Il est toujours possible de le faire mais faire un:

```python
>>> MaClass._attribute = value
``` 

n'est pas normal et doit alerter l'utilisateur.

Donc pour **protéger** l'attribut `area`, on fait:

```python
class Rectangle:
    
    def __init__(self, largeur, longueur):
        
        self.largeur = largeur
        self.longueur = longueur
        # self.area = self.largeur * self.longueur
        self._area = self.largeur * self.longueur
    
    @property
    def area(self):
        return self._area
    
    def __repr__(self):
        return """rectange:
            largeur:{} * longueur:{} -> area: {}""".format(self.largeur,
                                                            self.longueur,
                                                            self.area)
```

Ainsi, on peut accéder à l'attribut *area* sans la possibilité de définir sa valeur, car nous n'avons pas définit de `setter` pour cet attribut.

```python
>>> largeur, longueur = 2, 10
>>> r = Rectangle(largeur, longueur)
>>> r
rectange:
            largeur:2 * longueur:10 -> area: 20
>>> r.area
20
>>> r.area = 45
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: can't set attribute
```

On peut s'arrêter là si l'idée c'est juste de protéger l'attribut et de donner la possibilité de le lire.

# Mise à jour d'attribut

Par contre, maintenant le problème c'est que si l'utilisateur met à jour sa valeur de largeur, ce qui est possible en faisant:

```python
r.largeur = 3
```

l'aire ne sera pas mise à jour:

```python
>>> r.largeur = 3
>>> r
rectange:
            largeur:3 * longueur:10 -> area: 20
```

C'est faux!

L'aire doit être mise à jour.

```python
class Rectangle:
    
    def __init__(self, largeur, longueur):
        
        self.largeur = largeur
        self.longueur = longueur
        # self.area = self.largeur * self.longueur
        # self._area = self.largeur * self.longueur
    
    @property
    def area(self):
        return self.largeur * self.longueur

    def __repr__(self):
        return """rectange:
            largeur:{} * longueur:{} -> area: {}""".format(self.largeur,
                                                            self.longueur,
                                                            self.area)    
```

Testons:

```python
>>> largeur, longueur = 2, 10
>>> r = Rectangle(largeur, longueur)
>>> r
rectange:
            largeur:2 * longueur:10 -> area: 20
>>> r.largeur = 3
>>> r
rectange:
            largeur:3 * longueur:10 -> area: 30
```

Et même si `area` n'est pas définit dans le constructeur mais via un `@property` il se comporte comme un attribut.

```python
>>> hasattr(r, 'largeur')
True
>>> hasattr(r, 'area')
True
>>> hasattr(r, 'volume')
False
```

Voilà. Peut être que ce n'est pas très *pythonic* mais c'est comme ça que je procède lorsque je veux protéger un attribut et aussi qu'un attribut soit mis à jour en fonction de la mise à jour d'autres attributs dont il dépend.
