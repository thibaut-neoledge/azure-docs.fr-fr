<properties title="Azure Machine Learning Sample: CRM task" pageTitle="Exemple sous Machine Learning : tâche CRM | Azure" description="A sample Azure Machine Learning experiment to develop multiple models that predict customer churn, upsell, and propensity to buy a new product." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Exemple sous Azure Machine Learning : tâche CRM

>[AZURE.NOTE]
>L'[exemple d'expérience] et l'[exemple de jeu de données] associés à ce modèle sont disponibles dans ML Studio. Pour plus d'informations, consultez la section ci-dessous.
[Exemple d'expérience]: #sample-experiment
[Exemples de jeux de données]: #sample-datasets

<!--
- [Problem Description]
- [Data]
- [Model]
- [Results]
- [API]
- [Sample Experiment]
-->
[Description du problème]: #problem-description
[Données]: #data
[Modèle]: #model
[Résultats]: #results
[API]: #api

## Description du problème ##

Prédiction du taux d'attrition (changement de fournisseur), de la vente incitative (achat de mises à niveau ou de modules complémentaires), ainsi que de la propension à acheter un nouveau produit (appétence). 

## Données ##

Jeu de données de 50 000 clients de la compagnie de télécommunications française Orange. Chaque client comporte 230 caractéristiques anonymes distinctes. Ces données proviennent de KDD Cup 2009. 

Les caractéristiques sont à la fois sous forme numérique et sous forme de chaînes. Elles sont très fragmentées.
 
## Modèle ##

Le seul prétraitement appliqué aux données a été celui concernant les valeurs manquantes. Nous avons remplacé les valeurs manquantes des caractéristiques de type chaîne par " 0 ". Presque toutes les caractéristiques numériques comportent des valeurs positives. Nous avons ajouté 1 aux valeurs existantes des caractéristiques numériques et ajouté 0 dans les entrées présentant une valeur manquante. Ainsi, nous faisons la distinction entre les 0 d'origine et ceux indiquant une valeur manquante. Nous avons pour cela d'abord appliqué une opération mathématique (+1) uniquement aux caractéristiques numériques. Ensuite, nous avons remplacé toutes les valeurs manquantes par 0 (ou "0" pour les chaînes). 

Comme nous étions en présence de caractéristiques numériques et de caractéristiques de catégorie, nous avons utilisé un classifieur d'arbre de décision optimisé. Les trois problèmes étaient déséquilibrés, les exemples positifs étant en minorité. Dans certains cas, le classifieur a donc ignoré des régions de l'espace des caractéristiques comportant un petit nombre d'exemples positifs. Pour tester cela, nous avons formé deux modèles pour chaque problème : l'un à l'aide des données brutes et l'autre en répliquant les exemples positifs afin que le nouveau nombre d'exemples positifs soit à peu près égal au nombre d'exemples de test négatifs. Nous y sommes parvenus à l'aide d'un simple script R séparant les exemples positifs et négatifs, puis ajoutant 13 copies du jeu positif au jeu négatif. 

## Résultats ##

Les modèles ont généré des résultats liés à la probabilité d'un résultat positif pour chacune des tâches. Comme il est possible de sélectionner un seuil arbitraire pour définir une observation comme étant positive, nous avons mesuré les performances selon l'intégration, la zone se trouvant sous la courbe ROC. À noter que pour les cas d'attrition et de vente incitative, la réplication d'exemples positifs pour créer un jeu de données plus équilibré a légèrement amélioré les performances du modèle.  

<table border="1">
<tr><td>Attrition</td><td>ASC</td></tr>
<tr style="background-color: #fff"><td>no replication</td><td>0.711</td></tr>
<tr><td>Positifs répliqués</td><td>0,728</td></tr>
</table>


<table border="0">
<tr><td>Vente incitative</td><td>ASC</td></tr>
<tr style="background-color: #fff"><td>no replication</td><td>0.853</td></tr>
<tr><td>Positifs répliqués</td><td>0,865</td></tr>
</table>


<table border="0">
<tr><td>Appétence</td><td>ASC</td></tr>
<tr style="background-color: #fff"><td>no replication</td><td>0.805</td></tr>
<tr><td>Positifs répliqués</td><td>0,8</td></tr>
</table>

## API ##

Ce modèle n'a pas été mis en production, car il contient des caractéristiques anonymes.



## Exemple d'expérience

L'exemple d'expérience suivant associé à ce modèle est disponible dans ML Studio dans la rubrique **EXPÉRIENCES** de l'onglet **EXEMPLES**.

> **Exemples d'expériences - CRM - Développement**

## Exemples de jeux de données

Les exemples de jeu de données suivants utilisés dans le cadre de cette expérience sont disponibles dans ML Studio dans la palette de module sous **Jeux de données enregistrés**.

###Jeu de données CRM partagé
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-dataset-shared](../includes/machine-learning-sample-dataset-crm-dataset-shared.md)]

###Étiquettes de l'appétence CRM partagées
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-appetency-labels-shared](../includes/machine-learning-sample-dataset-crm-appetency-labels-shared.md)]

###Étiquettes de l'attrition CRM partagées
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-churn-labels-shared](../includes/machine-learning-sample-dataset-crm-churn-labels-shared.md)]

###Étiquettes de vente incitative CRM partagées
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-upselling-labels-shared](../includes/machine-learning-sample-dataset-crm-upselling-labels-shared.md)]
