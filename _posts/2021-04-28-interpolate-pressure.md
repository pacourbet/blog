---
layout: post
title:  "Interpolation 2D"
date:   2021-04-28 17:30:00 +0100
categories: matplotlib data
comments: true
---

Réaliser une interpolation 2D et l'animer

<!-- more -->

- [Références](#références)
- [Introduction](#introduction)
- [Modélisation](#modélisation)
  - [Plaque et capteurs](#plaque-et-capteurs)
- [Mesures de pression dans chacun des capteurs](#mesures-de-pression-dans-chacun-des-capteurs)
- [Interpolation](#interpolation)
  - [Effort sur une cellule du maillage de la plaque:](#effort-sur-une-cellule-du-maillage-de-la-plaque)
  - [Maillage](#maillage)
- [Test sur un pas de temps](#test-sur-un-pas-de-temps)
- [Animation pour voir l'évolution du champ de pression dans le temps](#animation-pour-voir-lévolution-du-champ-de-pression-dans-le-temps)
- [Conclusion](#conclusion)

## Références

1 | [article blog ](https://courspython.com/animation-matplotlib.html) | David Cassagne
2 | [Doc officielle scipy](https://scipython.com/blog/animated-contour-plots-with-matplotlib/) | scipy

## Introduction

Dans le domaine des essais en bassin on peut avoir besoin de faire des interpolations bi dimensionnelles. Imaginons une plaque qu'on immerge à quelques mètres de profondeur. On fait passer une vague.

On veut calculer l'effort au cours du temps que génére cette vague sur cette plaque. Pour ce faire, on peut intégrer la pression sur la plaque. Pour mesurer la pression, on peut placer des capteurs de pression à différent endroits sur la plaque. Pour calculer l'effort on aura besoin du champ de pression sur toute la surface.

## Modélisation

### Plaque et capteurs

Commençons par modéliser la plaque et ses capteurs.


```python
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd

# plate properties
width = 20
height = 10

# sensor location
sensor_loc = {"sensor01":(0.0, 0.0),
              "sensor02":(width/2, 0.0),
              "sensor03":(width/2, height/2),
              "sensor04": (0, height/2),
              "sensor05":(-width/2, height/2),
              "sensor06": (-width/2, 0),
              "sensor07":(-width/2, -height/2),
              "sensor08":(0, -height/2),
              "sensor09": (width/2, -height/2),
              "sensor10": (width/4, height/4),
              "sensor11": (-width/4, height/4),
              "sensor12": (-width/4, -height/4),
              "sensor13": (width/4, -height/4),
              }

# plot the plate
plt.plot([width/2, width/2, 0, -width/2, -width/2, -width/2, -width/2, width/2, width/2, width/2],
         [0, height/2, height/2, height/2, 0, -height/2, -height/2, -height/2, -height/2, 0],
         label="plate contour")

# plot the sensor
for sensor_name, location in sensor_loc.items():
    plt.scatter(location[0], location[1], color='red')
    plt.text(location[0], location[1], sensor_name)
plt.xlabel("x")
plt.ylabel("y")
```

<figure>
<img src="{{site.baseurl}}/assets/images/posts/interpolate-pressure/output_2_1.png" alt="position capteur">
<figcaption><i>legend: Position des capteurs sur la plaque</i></figcaption>
</figure>
    


## Mesures de pression dans chacun des capteurs

Simulons les mesures de capteurs avec la formulation de la propagation d'une onde dans l'espace et dans le temps (qui suit l'équation de propagation de D'Alembert):

$$s(x, t) = A . cos(k.x - w.t)$$



```python
def pressure(x, t):
    return np.cos(x - 2 * np.pi * t)

tt = np.linspace(0, 20, 200)
sensor_data = {}
for sensor_name, location in sensor_loc.items():
    sensor_data[sensor_name] = pressure(location[0], tt)
df_pressure = pd.DataFrame(sensor_data).T
```

Pour chaque sensor on a donc une série temporelle qui représente la mesure de pression


```python
df_pressure.loc[:,0:10] # sur les 10 premiers pas de temps
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>10</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>sensor01</th>
      <td>1.000000</td>
      <td>0.807157</td>
      <td>0.303005</td>
      <td>-0.318012</td>
      <td>-0.816376</td>
      <td>-0.999875</td>
      <td>-0.797737</td>
      <td>-0.287923</td>
      <td>0.332939</td>
      <td>0.825391</td>
      <td>0.999502</td>
    </tr>
    <tr>
      <th>sensor02</th>
      <td>-0.839072</td>
      <td>-0.998418</td>
      <td>-0.772689</td>
      <td>-0.248945</td>
      <td>0.370814</td>
      <td>0.847555</td>
      <td>0.997406</td>
      <td>0.762572</td>
      <td>0.233625</td>
      <td>-0.385429</td>
      <td>-0.855827</td>
    </tr>
    <tr>
      <th>sensor03</th>
      <td>-0.839072</td>
      <td>-0.998418</td>
      <td>-0.772689</td>
      <td>-0.248945</td>
      <td>0.370814</td>
      <td>0.847555</td>
      <td>0.997406</td>
      <td>0.762572</td>
      <td>0.233625</td>
      <td>-0.385429</td>
      <td>-0.855827</td>
    </tr>
    <tr>
      <th>sensor04</th>
      <td>1.000000</td>
      <td>0.807157</td>
      <td>0.303005</td>
      <td>-0.318012</td>
      <td>-0.816376</td>
      <td>-0.999875</td>
      <td>-0.797737</td>
      <td>-0.287923</td>
      <td>0.332939</td>
      <td>0.825391</td>
      <td>0.999502</td>
    </tr>
    <tr>
      <th>sensor05</th>
      <td>-0.839072</td>
      <td>-0.356107</td>
      <td>0.264203</td>
      <td>0.782614</td>
      <td>0.999181</td>
      <td>0.830379</td>
      <td>0.341311</td>
      <td>-0.279395</td>
      <td>-0.792343</td>
      <td>-0.999695</td>
      <td>-0.821479</td>
    </tr>
    <tr>
      <th>sensor06</th>
      <td>-0.839072</td>
      <td>-0.356107</td>
      <td>0.264203</td>
      <td>0.782614</td>
      <td>0.999181</td>
      <td>0.830379</td>
      <td>0.341311</td>
      <td>-0.279395</td>
      <td>-0.792343</td>
      <td>-0.999695</td>
      <td>-0.821479</td>
    </tr>
    <tr>
      <th>sensor07</th>
      <td>-0.839072</td>
      <td>-0.356107</td>
      <td>0.264203</td>
      <td>0.782614</td>
      <td>0.999181</td>
      <td>0.830379</td>
      <td>0.341311</td>
      <td>-0.279395</td>
      <td>-0.792343</td>
      <td>-0.999695</td>
      <td>-0.821479</td>
    </tr>
    <tr>
      <th>sensor08</th>
      <td>1.000000</td>
      <td>0.807157</td>
      <td>0.303005</td>
      <td>-0.318012</td>
      <td>-0.816376</td>
      <td>-0.999875</td>
      <td>-0.797737</td>
      <td>-0.287923</td>
      <td>0.332939</td>
      <td>0.825391</td>
      <td>0.999502</td>
    </tr>
    <tr>
      <th>sensor09</th>
      <td>-0.839072</td>
      <td>-0.998418</td>
      <td>-0.772689</td>
      <td>-0.248945</td>
      <td>0.370814</td>
      <td>0.847555</td>
      <td>0.997406</td>
      <td>0.762572</td>
      <td>0.233625</td>
      <td>-0.385429</td>
      <td>-0.855827</td>
    </tr>
    <tr>
      <th>sensor10</th>
      <td>0.283662</td>
      <td>-0.337128</td>
      <td>-0.827893</td>
      <td>-0.999351</td>
      <td>-0.785374</td>
      <td>-0.268489</td>
      <td>0.351948</td>
      <td>0.836644</td>
      <td>0.998658</td>
      <td>0.775504</td>
      <td>0.253249</td>
    </tr>
    <tr>
      <th>sensor11</th>
      <td>0.283662</td>
      <td>0.795048</td>
      <td>0.999795</td>
      <td>0.818936</td>
      <td>0.322224</td>
      <td>-0.298765</td>
      <td>-0.804524</td>
      <td>-0.999990</td>
      <td>-0.809774</td>
      <td>-0.307240</td>
      <td>0.313793</td>
    </tr>
    <tr>
      <th>sensor12</th>
      <td>0.283662</td>
      <td>0.795048</td>
      <td>0.999795</td>
      <td>0.818936</td>
      <td>0.322224</td>
      <td>-0.298765</td>
      <td>-0.804524</td>
      <td>-0.999990</td>
      <td>-0.809774</td>
      <td>-0.307240</td>
      <td>0.313793</td>
    </tr>
    <tr>
      <th>sensor13</th>
      <td>0.283662</td>
      <td>-0.337128</td>
      <td>-0.827893</td>
      <td>-0.999351</td>
      <td>-0.785374</td>
      <td>-0.268489</td>
      <td>0.351948</td>
      <td>0.836644</td>
      <td>0.998658</td>
      <td>0.775504</td>
      <td>0.253249</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_sensor = pd.DataFrame(sensor_loc).T
df_sensor.columns = ["x", "y"]
```


```python
df_sensor
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>x</th>
      <th>y</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>sensor01</th>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>sensor02</th>
      <td>10.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>sensor03</th>
      <td>10.0</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>sensor04</th>
      <td>0.0</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>sensor05</th>
      <td>-10.0</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>sensor06</th>
      <td>-10.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>sensor07</th>
      <td>-10.0</td>
      <td>-5.0</td>
    </tr>
    <tr>
      <th>sensor08</th>
      <td>0.0</td>
      <td>-5.0</td>
    </tr>
    <tr>
      <th>sensor09</th>
      <td>10.0</td>
      <td>-5.0</td>
    </tr>
    <tr>
      <th>sensor10</th>
      <td>5.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>sensor11</th>
      <td>-5.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>sensor12</th>
      <td>-5.0</td>
      <td>-2.5</td>
    </tr>
    <tr>
      <th>sensor13</th>
      <td>5.0</td>
      <td>-2.5</td>
    </tr>
  </tbody>
</table>
</div>



```python
df_data = df_sensor.join(df_pressure)
df_data.loc[:,["x", "y", 0, 1, 2, 3]]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>x</th>
      <th>y</th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>sensor01</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.000000</td>
      <td>0.807157</td>
      <td>0.303005</td>
      <td>-0.318012</td>
    </tr>
    <tr>
      <th>sensor02</th>
      <td>10.0</td>
      <td>0.0</td>
      <td>-0.839072</td>
      <td>-0.998418</td>
      <td>-0.772689</td>
      <td>-0.248945</td>
    </tr>
    <tr>
      <th>sensor03</th>
      <td>10.0</td>
      <td>5.0</td>
      <td>-0.839072</td>
      <td>-0.998418</td>
      <td>-0.772689</td>
      <td>-0.248945</td>
    </tr>
    <tr>
      <th>sensor04</th>
      <td>0.0</td>
      <td>5.0</td>
      <td>1.000000</td>
      <td>0.807157</td>
      <td>0.303005</td>
      <td>-0.318012</td>
    </tr>
    <tr>
      <th>sensor05</th>
      <td>-10.0</td>
      <td>5.0</td>
      <td>-0.839072</td>
      <td>-0.356107</td>
      <td>0.264203</td>
      <td>0.782614</td>
    </tr>
    <tr>
      <th>sensor06</th>
      <td>-10.0</td>
      <td>0.0</td>
      <td>-0.839072</td>
      <td>-0.356107</td>
      <td>0.264203</td>
      <td>0.782614</td>
    </tr>
    <tr>
      <th>sensor07</th>
      <td>-10.0</td>
      <td>-5.0</td>
      <td>-0.839072</td>
      <td>-0.356107</td>
      <td>0.264203</td>
      <td>0.782614</td>
    </tr>
    <tr>
      <th>sensor08</th>
      <td>0.0</td>
      <td>-5.0</td>
      <td>1.000000</td>
      <td>0.807157</td>
      <td>0.303005</td>
      <td>-0.318012</td>
    </tr>
    <tr>
      <th>sensor09</th>
      <td>10.0</td>
      <td>-5.0</td>
      <td>-0.839072</td>
      <td>-0.998418</td>
      <td>-0.772689</td>
      <td>-0.248945</td>
    </tr>
    <tr>
      <th>sensor10</th>
      <td>5.0</td>
      <td>2.5</td>
      <td>0.283662</td>
      <td>-0.337128</td>
      <td>-0.827893</td>
      <td>-0.999351</td>
    </tr>
    <tr>
      <th>sensor11</th>
      <td>-5.0</td>
      <td>2.5</td>
      <td>0.283662</td>
      <td>0.795048</td>
      <td>0.999795</td>
      <td>0.818936</td>
    </tr>
    <tr>
      <th>sensor12</th>
      <td>-5.0</td>
      <td>-2.5</td>
      <td>0.283662</td>
      <td>0.795048</td>
      <td>0.999795</td>
      <td>0.818936</td>
    </tr>
    <tr>
      <th>sensor13</th>
      <td>5.0</td>
      <td>-2.5</td>
      <td>0.283662</td>
      <td>-0.337128</td>
      <td>-0.827893</td>
      <td>-0.999351</td>
    </tr>
  </tbody>
</table>
</div>




## Interpolation

On a donc une plaque pour laquelle on a la mesure de pression au cours du temps pour chaque capteur.
Pour obtenir l'effort global sur cette plaque on a besoin de connaitre la pression en chaque point de la plaque (le champ de pression). Connaissant la pression en chaque point ainsi que la surface de chaque cellule du maillage, on pourrait obtenir la force globale:

### Effort sur une cellule du maillage de la plaque:

$$df = p(x) dS$$

$$F = \sum df$$

### Maillage


```python
from scipy import interpolate

# define the contour of the plate
x = np.linspace(-width/2, width/2, 10)
y = np.linspace(-height/2 , height/2, 10)

# mesh the plate
xx, yy = np.mgrid[-10:10:10*1j, -5:5:10*1j]
```


```python
plt.scatter(xx, yy)
for sensor_name, location in sensor_loc.items():
    plt.scatter(location[0], location[1], color='red')
    plt.text(location[0] + 0.1, location[1] + 0.1, sensor_name)
```


<figure>
<img src="{{site.baseurl}}/assets/images/posts/interpolate-pressure/output_14_0.png" alt="maillage">
<figcaption><i>legend: Maillage de la plaque</i></figcaption>
</figure>
    


L'interpolation sera donc en chacun de ces points en bleu. La position des capteurs est rappelée en rouge

Maintenant on peut faire l'interpolation des pressions connues en chaque point rouge aux points bleus.

## Test sur un pas de temps


```python
# test sur le premier pas de temps
pp = interpolate.griddata((df_data["x"], df_data["y"]), df_data[0], (xx, yy))
```


```python
plt.contourf(xx, yy, pp)
for sensor_name, location in sensor_loc.items():
    plt.scatter(location[0], location[1], color='red')
    plt.text(location[0], location[1], sensor_name)
cb = plt.colorbar(orientation = "horizontal")
cb.set_label('Pressure (unitary)', rotation = 0)
plt.show()
```


<figure>
<img src="{{site.baseurl}}/assets/images/posts/interpolate-pressure/output_19_0.png" alt="interpolation_single_step">
<figcaption><i>legend: Interpolation de la pression sur un pas de temps</i></figcaption>
</figure>
    


## Animation pour voir l'évolution du champ de pression dans le temps

Pour faire l'animation, je me suis inspiré d'un exemple très clair et bien commenté de ce blog: [https://courspython.com/animation-matplotlib.html](https://courspython.com/animation-matplotlib.html) de **David Cassagne** et de la doc officielle [https://scipython.com/blog/animated-contour-plots-with-matplotlib/](https://scipython.com/blog/animated-contour-plots-with-matplotlib/)


```python
import matplotlib.animation as animation

# initialize the figure
fig, ax = plt.subplots()

def animate(i):
    ppi = interpolate.griddata((df_data["x"], df_data["y"]), df_data[i], (xx, yy), method='cubic')
    return ax.contourf(xx, yy, ppi)

for sensor_name, location in sensor_loc.items():
    ax.scatter(location[0], location[1], color='red')
    ax.text(location[0], location[1], sensor_name)

anim = animation.FuncAnimation(fig, animate, frames=50, repeat=False)
anim.save('pressure.gif', writer='pillow')
```

<figure>
<img src="{{site.baseurl}}/assets/images/posts/interpolate-pressure/pressure.gif" alt="pressure_time">
<figcaption><i>legend: Evolution du champ de pression</i></figcaption>
</figure>

>Note: vu le type d'évolution dans le temps et l'espace que j'ai considéré pour modéliser la propagation de l'onde de pression, il n'y a pas de raison de ne pas voir des bandes verticales se déplacer. La distorsion de ces bandes, qui sont donc issues de l'interpolation, vient tout simplement du fait que les capteurs 10, 11, 12 et 13 ne sont pas positionnés aux même y que les autres capteurs. On n'a pas des capteurs toujours là où on veut 😏

## Conclusion

Quand on suit les exemples d'interpolation 2D, ça parait assez simple.
Ce que j'ai trouvé intéressant dans la réalisation de cet article, outre les interpolations et les animations, c'était de réfléchir à comment générer des "fausses" données et à les assembler avec les dataframes.
