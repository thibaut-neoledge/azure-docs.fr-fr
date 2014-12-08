<properties title="Azure Machine Learning Sample: Prediction of the number of bike rentals" pageTitle="Exemple sous Machine Learning : prédiction des locations de vélos | Azure" description="A sample Azure Machine Learning experiment to develop a regression model that predicts the number of bike rentals hourly." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Exemple sous Azure Machine Learning : prédiction du nombre de locations de vélos

>[AZURE.NOTE]
>L'[exemple d'expérience] et l'[exemple de jeu de données] associés à ce modèle sont disponibles dans ML Studio. Pour plus d'informations, consultez la section ci-dessous.
[Exemple d'expérience]: #exemple-expérience
[Exemple de jeu de données]: #exemple-jeudedonnées


## Description du problème ##
Prédiction du nombre de vélos qui seront loués heure par heure aujourd'hui, en fonction de l'historique de location, de la météo heure par heure, si aujourd'hui est un jour de semaine, un jour férié, un week-end, etc. Les prédictions sont différentes pour chaque heure (par exemple,
 les locations sont nombreuses le matin mais très rares la nuit). 

## Données ##
Le jeu de données de location de vélo de l'UCI est basé sur les données réelles de la société Capital Bikeshare qui assure l'entretien du réseau de location de vélos à Washington DC. Ce jeu de données comporte une ligne par heure pour les années 2011 et 2012, soit au total 17 379 lignes. Le nombre de locations de vélos par heure varie entre 1 et 977.

## Modèle ##
Nous avons utilisé les données de 2011 comme jeu d'apprentissage et celles de 2012 comme jeu de test. Nous avons comparé 4 jeux de caractéristiques :

1. météo + vacances + jour de semaine + week-end pour le jour prévu
1. nombre de vélos loués au cours des 12 dernières heures
1. nombre de vélos loués au cours des dernières 24 heures
1. nombre de vélos loués au cours des 12 dernières semaines le même jour à la même heure

Les caractéristiques B capturent les toutes dernières demandes de vélos. Les caractéristiques C capturent la demande de vélos pour une heure en particulier. Les caractéristiques D capturent la demande de vélos pour une heure et un jour en particulier.

Comme l'étiquette (nombre de locations) est une valeur réelle, nous sommes en présence d'un paramètre de régression. En outre, comme le nombre de caractéristiques est relativement restreint (moins de 100) et qu'elles ne sont pas fragmentées, la limite de décision est probablement non linéaire. Sur cette base, nous avons décidé d'utiliser un algorithme de régression en arbre de décision optimisé.

## Résultats ##

<table border="1">
<tr><th>Caractéristiques</th><th>Erreur absolue moyenne</th> <th>Erreur moyenne quadratique</th> </tr>
<tr style="background-color: #fff"><td>A</td> <td> 89,7</td> <td>124,9 </td> </tr>
<tr style="background-color: #fff"><td>A+B</td><td>51,2 </td> <td>86,7 </td></tr>
<tr style="background-color: #fff"><td>A+B+C</td><td> 48,5</td> <td> 83,7 </td></tr>
<tr style="background-color: #fff"><td>A+B+C+D</td><td> 48,8 </td> <td>83,2 </td></tr>
</table>

</table>

Les meilleurs résultats sont offerts par A+B+C et A+B+C+D. Étonnamment, D n'offre pas vraiment d'améliorations par rapport à A+B+C. 

## Exemple d'expérience

L'exemple d'expérience suivant associé à ce modèle est disponible dans ML Studio dans la rubrique **EXPÉRIENCES** de l'onglet **EXEMPLES**.

> **Exemple d'expérience - Prévision de la demande de vélos**


## Exemple de jeu de données

L'exemple de jeu de données suivant utilisé dans cette expérience est disponible dans ML Studio dans la palette de module sous **Jeux de données enregistrés**.

###Jeu de données de location de vélos de l'UCI
[AZURE.INCLUDE [machine-learning-sample-dataset-bike-rental-uci-dataset](../includes/machine-learning-sample-dataset-bike-rental-uci-dataset.md)]


