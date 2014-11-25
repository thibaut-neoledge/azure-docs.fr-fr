<properties title="Azure Machine Learning Sample: Finding similar companies" pageTitle="Machine Learning Sample: Finding similar companies | Azure" description="A sample Azure Machine Learning experiment that uses a clustering model to find similar companies." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Exemple Azure Machine Learning : Recherche d'entreprises apparentées

*Vous pouvez retrouver l'exemple d'expérience associé à ce modèle dans la section **EXPÉRIENCE** de l'onglet **EXEMPLES**. Le nom de l'expérience est :*

    Sample Experiment - S & P 500 Company Clustering - Development

## Description du problème

Créer des clusters de sociétés S&P 500 en fonction des articles Wikipédia de chaque société. Ce modèle peut par exemple être utilisé pour identifier les entreprises prometteuses sur la base de similitudes constatées avec des entreprises du classement S&P 500 existantes.

## Données

Les données sont récupérées dans l'extraction XML de Wikipédia. Ces données ont été prétraitées en dehors de ML Studio afin d'extraire le contenu texte correspondant à chaque société, de supprimer la mise en forme wiki, de supprimer les caractères non alphanumériques et de convertir tout le texte en minuscules. De plus, d'autres catégories de sociétés connues ont été ajoutées. Notez qu'aucun article n'a été trouvé pour certaines sociétés. Le nombre d'enregistrements est donc inférieur à 500.

## Modèle

Tout d'abord, le contenu de l'article passe par le module de hachage de caractéristiques, qui transforme les données texte en un format numérique basé sur la valeur de hachage de chaque jeton. Ces données comportent trop de dimensions et sont trop fragmentées pour être utilisées directement en cluster de k-moyennes. PCA est donc utilisé avec le module d'exécution du script R pour réduire le nombre de dimensions à 10 variables. Notez qu'il est possible de visualiser les résultats de PCA en ouvrant le résultat à droite du module R.

Après tâtonnement, il a été découvert que la première variable de données PCA transformées, celle dont la variation est la plus élevée, semble avoir un effet négatif sur la création des clusters. Elle a donc été supprimée du jeu à l'aide du module Colonnes de projet.

Les données sont ensuite regroupées en trois clusters selon l'algorithme des k-moyennes. Après plusieurs essais, il s'est avéré que ce nombre donnait des résultats raisonnables. Les autres nombres de clusters testés étaient quatre et cinq.

Enfin, l'éditeur de métadonnées est utilisé pour convertir la variable d'étiquette du cluster en catégorie. Les résultats sont également convertis en fichier CSV pour le téléchargement.

## Résultats

Les résultats peuvent être consultés en visualisant la sortie de l'éditeur de métadonnées et en mettant en correspondance la colonne de catégorie (venant des données Wikipédia) et les colonnes d'affectation. Les trois clusters correspondent à peu près aux catégories suivantes :

Cluster 0 : Consumer Discretionary, Consumer Staples, Financials, Healthcare

Cluster 1 : Information Technology

Cluster 2 : Energy, Utilities, Telecommunication Services

**Remarque** Les clusters ne sont pas clairement définis. Le cluster 0 comporte plusieurs sociétés des secteurs informatique et énergétique, et les sociétés industrielles et les sociétés de matériaux sont réparties entre les clusters 0 et 1.

**Remarque** Les clusters peuvent varier d'une exécution à l'autre.

