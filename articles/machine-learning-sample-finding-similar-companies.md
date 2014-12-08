<properties title="Azure Machine Learning Sample: Finding similar companies" pageTitle="Exemple sous Machine Learning : recherche d'entreprises apparentées | Azure" description="A sample Azure Machine Learning experiment that uses a clustering model to find similar companies." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Exemple sous Azure Machine Learning : recherche d'entreprises apparentées

>[AZURE.NOTE]
>L'[exemple d'expérience] et l'[exemple de jeu de données] associés à ce modèle sont disponibles dans ML Studio. Pour plus d'informations, consultez la section ci-dessous.
[Exemple d'expérience]: #exemple-expérience
[Exemple de jeu de données]: #exemple-jeudedonnées


## Description du problème ##
Créer des clusters de sociétés S&P 500 en fonction des articles Wikipédia de chaque société. Ce modèle peut par exemple être utilisé pour identifier les entreprises prometteuses sur la base de similitudes constatées avec des entreprises du classement S&P 500 existantes.

## Données ##
Les données sont récupérées dans l'extraction XML de Wikipédia. Ces données ont été prétraitées en dehors de ML Studio afin d'extraire le contenu texte correspondant à chaque société, de supprimer la mise en forme wiki, de supprimer les caractères non alphanumériques et de convertir tout le texte en minuscules. De plus, d'autres catégories de sociétés connues ont été ajoutées. Notez qu'aucun article n'a été trouvé pour certaines sociétés. Le nombre d'enregistrements est donc inférieur à 500.

## Modèle ##
Tout d'abord, le contenu de l'article passe par le module de hachage de caractéristiques, qui transforme les données texte en un format numérique basé sur la valeur de hachage de chaque jeton. Ces données comportent trop de dimensions et sont trop fragmentées pour être utilisées directement en cluster de k-moyennes. PCA est donc utilisé avec le module d'exécution du script R pour réduire le nombre de dimensions à 10 variables. Notez qu'il est possible de visualiser les résultats de PCA en ouvrant le résultat à droite du module R. 

Après tâtonnement, il a été découvert que la première variable de données PCA transformée, celle dont la variation est la plus élevée, semble avoir un effet négatif sur la création des clusters. Elle a donc été supprimée du jeu à l'aide du module Colonnes de projet.

Les données sont ensuite regroupées en trois clusters selon l'algorithme des k-moyennes. Après plusieurs essais, il s'est avéré que ce nombre donnait des résultats raisonnables. Les autres nombres de clusters testés étaient quatre et cinq.

Enfin, l'éditeur de métadonnées est utilisé pour convertir la variable d'étiquette du cluster en catégorie. Les résultats sont également convertis en fichier CSV pour le téléchargement.

## Résultats ##
Les résultats peuvent être consultés en visualisant la sortie de l'éditeur de métadonnées et en mettant en correspondance la colonne de catégorie (venant des données Wikipédia) et les colonnes d'affectation. Les trois clusters correspondent à peu près aux catégories suivantes :

Cluster 0 : consommation discrétionnaire, consommation de base, finance, santé

Cluster 1 : technologie de l'information

Cluster 2 : énergie, services publics, services de télécommunication

**Remarque** Les clusters ne sont pas clairement définis. Le cluster 0 comporte plusieurs sociétés des secteurs informatique et énergétique, et les sociétés industrielles et les sociétés de matériaux sont réparties entre les clusters 0 et 1.

**Remarque** Les clusters peuvent varier d'une exécution à l'autre. 



## Exemple d'expérience

L'exemple d'expérience suivant associé à ce modèle est disponible dans ML Studio dans la rubrique **EXPÉRIENCES** de l'onglet **EXEMPLES**.

> **Exemple d'expérience - Création de clusters pour les sociétés du S&P 500 - Développement**


## Exemple de jeu de données

L'exemple de jeu de données suivant utilisé dans cette expérience est disponible dans ML Studio dans la palette de module sous **Jeux de données enregistrés**.

###Jeu de données Wikipedia concernant le SP 500
[AZURE.INCLUDE [machine-learning-sample-dataset-wikipedia-sp-500](../includes/machine-learning-sample-dataset-wikipedia-sp-500.md)]
