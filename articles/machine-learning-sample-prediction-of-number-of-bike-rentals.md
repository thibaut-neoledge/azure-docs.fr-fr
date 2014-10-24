<properties title="Azure Machine Learning Sample: Prediction of the number of bike rentals" pageTitle="Machine Learning Sample: Prediction of bike rentals | Azure" description="A sample Azure Machine Learning experiment to develop a regression model that predicts the number of bike rentals hourly." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Exemple Azure Machine Learning : Prédiction du nombre de vélos loués

*Vous pouvez retrouver l'exemple d'expérience associé à ce modèle dans la section **EXPÉRIENCE** de l'onglet **EXEMPLES**. Le nom de l'expérience est :*

    Sample Experiment - Demand Forecasting of Bikes

## Description du problème

Prédiction du nombre de vélos loués heure par heure aujourd'hui, en fonction de l'historique de location, de la météo heure par heure, si aujourd'hui est un jour de semaine, un jour férié, un week-end, etc. Les prédictions sont différentes selon les heures (par exemple,
 de nombreuses locations ont lieu le matin et presque aucune n'a lieu la nuit).

## Données

Le jeu de données d'UCI Bike Rental est basé sur les données réelles de Capital Bikeshare qui assure la gestion du réseau de location de vélos à Washington DC. Ce jeu de données comporte une ligne par heure pour les années 2011 et 2012, soit au total 17 379 lignes. Le nombre de locations de vélos par heure varie entre 1 et 977.

## Modèle

Nous avons utilisé les données de 2011 comme jeu d'apprentissage et celles de 2012 comme jeu de test. Nous avons comparé 4 jeux de caractéristiques :

1.  météo + vacances + jour de semaine + week-end pour le jour prévu
2.  nombre de vélos loués au cours des 12 dernières heures
3.  nombre de vélos loués au cours des dernières 24 heures
4.  nombre de vélos loués au cours des 12 dernières semaines le même jour à la même heure

Les caractéristiques B capturent les toutes dernières demandes de vélos. Les caractéristiques C capturent la demande de vélos pour une heure en particulier. Les caractéristiques D capturent la demande de vélos pour une heure et un jour en particulier.

Comme l'étiquette (nombre de locations) est une valeur réelle, nous sommes en présence d'un paramètre de régression. En outre, comme le nombre de caractéristiques est relativement restreint (moins de 100) et qu'elles ne sont pas fragmentées, la limite de décision est probablement non linéaire. Sur cette base, nous avons décidé d'utiliser un algorithme de régression en arbre de décision optimisé.

## Résultats

| Caractéristiques | Erreur d'absolue moyenne | Erreur quadratique moyenne |
|------------------|--------------------------|----------------------------|
| A                | 89,7                     | 124,9                      |
| A+B              | 51,2                     | 86,7                       |
| A+B+C            | 48,5                     | 83,7                       |
| A+B+C+D          | 48,8                     | 83,2                       |

</table>
Les meilleurs résultats sont offerts par A+B+C et A+B+C+D. Étonnamment, D n'offre pas vraiment d'améliorations par rapport à A+B+C.

