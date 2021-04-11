---
layout: post
title:  "Scatter et Histogramme"
date:   2021-04-10 12:05:00 +0100
categories: tips
comments: true
---

Tracer un nuage de points avec histogramme

<!-- more -->

- [Références](#références)
- [Introduction](#introduction)
- [Import des données](#import-des-données)
- [Traitement et statistiques](#traitement-et-statistiques)
- [Graphes et layout](#graphes-et-layout)
- [Nuage de point avec histogramme](#nuage-de-point-avec-histogramme)
- [Conclusion](#conclusion)

# Références

1 | [The art of statistics Learning from Data](https://www.penguin.co.uk/books/294/294857/learning-from-data/9780241258767.html) | David Spiegelhalter
2 | [Données météo Australie](https://www.kaggle.com/jsphyg/weather-dataset-rattle-package?select=weatherAUS.csv) | [site web kaggle](https://www.kaggle.com/)
3 | [La documentation de matplotlib sur les layout des subplot](https://matplotlib.org/stable/tutorials/intermediate/gridspec.html) | Documentation `matplotlib` officielle 
4 | [article oreilly](https://www.oreilly.com/library/view/python-data-science/9781491912126/ch04.html) | O'REILLY

# Introduction

Dans [The art of statistics](#référence) je suis tombé sur un graphe très parlant que je trouvais intéressant de savoir reproduire.

<figure>
<img src="{{site.baseurl}}/assets/images/posts/scatter-distrib/inspiration.jpg" alt="inspiration">
<figcaption><i>legend: Graphe d'inspiration</i></figcaption>
</figure>

Tout d'abord importons des données. J'ai utilisé la plateforme **Kaggle** qui met à disposition un grand nombre de data. J'ai opté pour les [données météorologiques d'une station météo en Australie](#référence).  

# Import des données

```python
import pandas as pd
data_csv = "./weatherAUS.csv"
df_data = pd.read_csv(data_csv, sep=",").dropna()
```

Décrivons un peu le data


```python
df_data.columns
```




    Index(['Date', 'Location', 'MinTemp', 'MaxTemp', 'Rainfall', 'Evaporation',
           'Sunshine', 'WindGustDir', 'WindGustSpeed', 'WindDir9am', 'WindDir3pm',
           'WindSpeed9am', 'WindSpeed3pm', 'Humidity9am', 'Humidity3pm',
           'Pressure9am', 'Pressure3pm', 'Cloud9am', 'Cloud3pm', 'Temp9am',
           'Temp3pm', 'RainToday', 'RainTomorrow'],
          dtype='object')




```python
df_data.describe()[["MaxTemp", "Rainfall", "Sunshine"]]
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
      <th>MaxTemp</th>
      <th>Rainfall</th>
      <th>Sunshine</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>56420.000000</td>
      <td>56420.000000</td>
      <td>56420.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>24.219206</td>
      <td>2.130397</td>
      <td>7.735626</td>
    </tr>
    <tr>
      <th>std</th>
      <td>6.970676</td>
      <td>7.014822</td>
      <td>3.758153</td>
    </tr>
    <tr>
      <th>min</th>
      <td>4.100000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>18.700000</td>
      <td>0.000000</td>
      <td>5.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>23.900000</td>
      <td>0.000000</td>
      <td>8.600000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>29.700000</td>
      <td>0.600000</td>
      <td>10.700000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>48.100000</td>
      <td>206.200000</td>
      <td>14.500000</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_data["Date"]
```




    6049      2009-01-01
    6050      2009-01-02
    6052      2009-01-04
    6053      2009-01-05
    6054      2009-01-06
                 ...    
    142298    2017-06-20
    142299    2017-06-21
    142300    2017-06-22
    142301    2017-06-23
    142302    2017-06-24
    Name: Date, Length: 56420, dtype: object

# Traitement et statistiques

On a donc des données de 2009 à 2917. Immagninons qu'on veuille passer des vacances en Australie à Darwin et qu'on veuille éviter les pluies. Pour choisir le meilleur mois, on va faire la moyenne des tombées de pluies sur chaque mois pendant ces 18 années de data.


```python
# first create a new column with only month
df_data["Month"] = df_data["Date"].apply(lambda date: date[5:-3])
# use group by function from pandas to apply mean for each month
location_filter = df_data["Location"] == "Darwin"
df_data[location_filter]["Rainfall"].groupby(df_data["Month"]).mean()
```

    Month
    01    12.751111
    02    10.286538
    03     7.600000
    04     2.375000
    05     0.856000
    06     0.003831
    07     0.000722
    08     0.010145
    09     0.618727
    10     2.669925
    11     4.693548
    12     8.898655
    Name: Rainfall, dtype: float64



A priori il vaut mieux éviter début et fin d'années et Juillet semble être le mois où il pleut le moins.

```python
plt.style.use('ggplot')
fig = plt.Figure()

# to show only Darwin data and ignore too small rainf fall
data_filter = (df_data["Location"] == "Darwin") & (df_data["Rainfall"] > 5)
x = df_data[data_filter]["Month"]
y = df_data[data_filter]["Rainfall"]

plt.scatter(x, y)
plt.xlabel('Month #')
plt.ylabel("The amount of rainfall recorded for the day in mm")
```

<figure>
<img src="{{site.baseurl}}/assets/images/posts/scatter-distrib/output_11_1.png" alt="scatter">
<figcaption><i>legend: Histogramme</i></figcaption>
</figure>
    

# Graphes et layout

La documentation de matplotlib sur [les layout des subplot](#référence)

```python
# Set up the axes with gridspec
fig = plt.figure(figsize=(6, 6))
grid = plt.GridSpec(4, 4, hspace=1, wspace=1)
main_ax = fig.add_subplot(grid[:-1, 1:])
y_hist = fig.add_subplot(grid[:-1, 0], xticklabels=[], sharey=main_ax)
x_hist = fig.add_subplot(grid[-1, 1:], yticklabels=[], sharex=main_ax)
```
    
<figure>
<img src="{{site.baseurl}}/assets/images/posts/scatter-distrib/output_13_0.png" alt="layout">
<figcaption><i>legend: matplotlib layout</i></figcaption>
</figure>


# Nuage de point avec histogramme

Et donc pour le graphe avec la distribution sur les axes, on peut faire:

```python
fig = plt.figure(figsize=(6, 6))
grid = plt.GridSpec(4, 4, hspace=1, wspace=1)
main_ax = fig.add_subplot(grid[:-1, 1:])
y_hist = fig.add_subplot(grid[:-1, 0], xticklabels=[], sharey=main_ax)
x_hist = fig.add_subplot(grid[-1, 1:], yticklabels=[], sharex=main_ax)
# scatter points on the main axes
main_ax.plot(x, y, 'ok', markersize=3, alpha=0.2)

# histogram on the attached axes
x_hist.hist(x, 40, histtype='stepfilled',
            orientation='vertical', color='gray')
x_hist.invert_yaxis()

y_hist.hist(y, 100, histtype='stepfilled',
            orientation='horizontal', color='gray')
y_hist.invert_xaxis()
```


    
<figure>
<img src="{{site.baseurl}}/assets/images/posts/scatter-distrib/output_14_0.png" alt="scatter and distrib">
<figcaption><i>legend: Nuage de points + histogramme</i></figcaption>
</figure>


[source: article O'REILLY](#référence)

Le graphe confirme bien qu'il faut éviter la fin et début d'année, là où les distributions sont élevées (cf. ditrib horizontale). On remarque aussi que les pluies sont majoritairement des pluies < 10 mm par jour.

# Conclusion

Il y a toujours plusieurs façons de représenter la données. L'important c'est d'en trouver une qui fait parler la data de la manière la plus simple possible.