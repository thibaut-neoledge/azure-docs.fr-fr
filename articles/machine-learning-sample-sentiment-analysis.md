<properties title="Azure Machine Learning Sample: Sentiment analysis" pageTitle="Machine Learning Sample: Sentiment analysis | Azure" description="A sample Azure Machine Learning experiment that uses sentiment classification to predict product reviews." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Exemple Azure Machine Learning : analyse de sentiments

*Vous pouvez retrouver l'exemple d'expérience associé à ce modèle dans la section **EXPÉRIENCE** de l'onglet **EXEMPLES**. Le nom de l'expérience est :*

    Sample Experiment - Sentiment Classification - Development

## Description du problème

Prédire une notation d'analyse de produit. Les notes sont 1, 2, 3, 4, 5. Il s'agit d'un problème de régression ordinale, qui peut être résolu en tant que problème de régression et problème de classification multiclasse.

## Données

Analyse de livres sur Amazon, choisis de façon aléatoire sur le site Amazon par les chercheurs UPenn ([][]<http://www.cs.jhu.edu/~mdredze/datasets/sentiment/></a>). Le jeu de données d'origine comporte 975 000 analyses avec les notes 1, 2, 3, 4, 5. Pour accélérer l'expérience, nous avons ramené le nombre d'exemples du jeu de données à 10 000 analyses. Toutes les analyses sont en anglais. Elles ont été écrites entre 1997 et 2007.

## Modèle

Nous avons utilisé le module de hachage de caractéristiques pour transformer le texte en anglais en caractéristiques en valeurs entières. Nous avons comparé trois modèles :

1.  Régression linéaire
2.  Régression ordinale en utilisant la régression logistique à deux classes comme classifieur de base
3.  Classification multiclasse en utilisant le classifieur de régression logistique multiclasse

## Résultats

Algorithme| Erreur d'absolue moyenne| Erreur quadratique moyenne
---------|
Régression ordinale | 0.82| 1.41|
Régression linéaire | 1.04| 1.36|
Classification multiclasse | 0.85 | 1.57

En nous basant sur ces trois résultats, nous avons choisi le modèle de régression ordinale pour créer un service web.

<!-- Removed until this part is fixed ##API We have built a web service that takes a plain text review and predicts its rating. -->

  []: http://www.cs.jhu.edu/~mdredze/datasets/sentiment/
