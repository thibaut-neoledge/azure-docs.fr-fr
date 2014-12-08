<properties title="Azure Machine Learning Sample: Sentiment analysis" pageTitle="Exemple sous Machine Learning : analyse de sentiment | Azure" description="A sample Azure Machine Learning experiment that uses sentiment classification to predict product reviews." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />
 

# Exemple sous Azure Machine Learning : analyse de sentiment

>[AZURE.NOTE]
>L'[exemple d'expérience] et l'[exemple de jeu de données] associés à ce modèle sont disponibles dans ML Studio. Pour plus d'informations, consultez la section ci-dessous.
[Exemple d'expérience]: #exemple-expérience
[Exemple de jeu de données]: #exemple-jeudedonnées


##Description du problème
Prédire la notation d'une analyse de produit. Les notes sont 1, 2, 3, 4, 5. Il s'agit d'un problème de régression ordinale, qui peut être résolu en tant que problème de régression et problème de classification multiclasse.
 
##Données
Analyses de livres sur Amazon, choisis de façon aléatoire sur le site Amazon par les chercheurs UPenn ([http://www.cs.jhu.edu/~mdredze/datasets/sentiment/](http://www.cs.jhu.edu/~mdredze/datasets/sentiment/)). Le jeu de données d'origine comporte 975 000 analyses avec les notes 1, 2, 3, 4, 5. Pour accélérer l'expérience, nous avons ramené le nombre d'exemples du jeu de données à 10 000 analyses. Toutes les analyses sont en anglais. Elles ont été écrites entre 1997 et 2007. 
 
##Modèle
Nous avons utilisé le module de hachage de caractéristiques pour transformer le texte en anglais en caractéristiques à valeurs entières. Nous avons comparé trois modèles :  
 
1. Régression linéaire   
2. Régression ordinale en utilisant la régression logistique à deux classes comme classifieur de base
3. Classification multiclasse en utilisant le classifieur de régression logistique multiclasse
 
##Résultats

Algorithme                 | Erreur absolue moyenne | Erreur moyenne quadratique
:---------                | :-----------------: | :--------------------:
Régression ordinale        | 0,82                | 1,41
Régression linéaire         | 1,04                | 1,36
Classification multiclasse | 0,85                | 1,57
 
En nous basant sur ces trois résultats, nous avons choisi le modèle de régression ordinale pour créer un service web.
 
<!-- Removed until this part is fixed
##API
We have built a web service that takes a plain text review and predicts its rating.
-->


## Exemple d'expérience

L'exemple d'expérience suivant associé à ce modèle est disponible dans ML Studio dans la rubrique **EXPÉRIENCES** de l'onglet **EXEMPLES**.

> **Exemple d'expérience - Classification de sentiment - Développement**


## Exemple de jeu de données

L'exemple de jeu de données suivant utilisé dans cette expérience est disponible dans ML Studio dans la palette de module sous **Jeux de données enregistrés**.

###Critiques de livres d'Amazon
[AZURE.INCLUDE [machine-learning-sample-dataset-book-reviews-from-amazon](../includes/machine-learning-sample-dataset-book-reviews-from-amazon.md)]
