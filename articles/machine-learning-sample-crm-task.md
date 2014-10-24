<properties title="Azure Machine Learning Sample: CRM task" pageTitle="Machine Learning Sample: CRM task | Azure" description="A sample Azure Machine Learning experiment to develop multiple models that predict customer churn, upsell, and propensity to buy a new product." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Exemple Azure Machine Learning : Tâche CRM (gestion de la relation client)

*Vous pouvez retrouver l'exemple d'expérience associé à ce modèle dans la section **EXPÉRIENCE** de l'onglet **EXEMPLES**. Le nom de l'expérience est :*

    Sample Experiments - CRM - Development

## Description du problème

Prédiction du taux d'attrition (changement de fournisseur), de la vente incitative (achat de mises à niveau ou de modules complémentaires), ainsi que de la propension à acheter un nouveau produit (appétence).

## Données

Jeu de données de 50 000 clients de la compagnie de télécommunications française Orange. Chaque client comporte 230 caractéristiques anonymes distinctes. Ces données proviennent de KDD Cup 2009.

Les caractéristiques sont à la fois sous forme numérique et sous forme de chaînes. Elles sont très fragmentées.

## Modèle

Le seul prétraitement appliqué aux données était celui concernant les valeurs manquantes. Nous avons remplacé les valeurs manquantes des caractéristiques de type chaîne par « 0 ». Presque toutes les caractéristiques numériques comportent des valeurs positives. Nous avons ajouté 1 aux valeurs existantes des caractéristiques numériques et ajouté 0 dans les entrées présentant une valeur manquante. Ainsi, nous faisons la distinction entre les 0 d'origine et ceux indiquant une valeur manquante. Nous avons pour cela d'abord appliqué une opération mathématique (+1) uniquement aux caractéristiques numériques. Ensuite, nous avons remplacé toutes les valeurs manquantes par 0 (ou "0" pour les chaînes).

Comme nous étions en présence de caractéristiques numériques et de caractéristiques de catégorie, nous avons utilisé un classifieur d'arbre de décision optimisé. Les trois problèmes étaient déséquilibrés, les exemples positifs étant en minorité. Dans certains cas, le classifieur a donc ignoré des régions de l'espace des caractéristiques comportant un petit nombre d'exemples positifs. Pour tester cette situation, nous avons créé deux modèles pour chaque problème : un qui utilise les données brutes et l'autre qui réplique les exemples positifs afin que le nouveau nombre d'exemples positifs soit à peu près égal au nombre d'exemples de tests négatifs. Nous y sommes parvenus à l'aide d'un simple script R séparant les exemples positifs et négatifs, puis ajoutant 13 copies du jeu positif au jeu négatif.

## Résultats

Les modèles généraient des scores liés à la probabilité d'un résultat positif pour chacune des tâches. Comme il est possible de sélectionner un seuil arbitraire pour définir une observation comme étant positive, nous avons mesuré les performances selon l'intégration, la zone se trouvant sous la courbe ROC. À noter que pour les cas d'attrition et de vente incitative, la réplication d'exemples positifs pour créer un jeu de données plus équilibré a légèrement amélioré les performances du modèle.

| Attrition          | Intégration |
|--------------------|-------------|
| aucune réplication | 0,711       |
| positifs répliqués | 0,728       |

| Vente incitative   | Intégration |
|--------------------|-------------|
| aucune réplication | 0,853       |
| positifs répliqués | 0,865       |

| Appétence          | Intégration |
|--------------------|-------------|
| aucune réplication | 0,805       |
| positifs répliqués | 0,8         |

## API

Ce modèle n'a pas été mis en production, car il contient des caractéristiques anonymes.

