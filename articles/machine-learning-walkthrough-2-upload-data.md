<properties 
	pageTitle="Étape 2 : Téléchargement de données dans une expérience Machine Learning | Azure" 
	description="Étape de procédure pas à pas de solution 2 : téléchargement des données publiques existantes dans Azure Machine Learning Studio" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/06/2015" 
	ms.author="garye"/>

Voici la seconde étape du didacticiel pas à pas [Développement d'une solution de prévision avec Azure ML][develop] :

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/


1.	[Création d'un espace de travail ML][create-workspace]
2.	**Téléchargement de données existantes**
3.	[Création d'une expérience][create-new]
4.	[Formation et évaluation des modèles][train-models]
5.	[Publication du service web][publish]
6.	[Accès au service web][access-ws]

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# Étape 2 : Téléchargement de données existantes dans une expérience Azure Machine Learning  

Pour développer un modèle de prévision pour un risque de crédit, nous allons utiliser le modèle " UCI Statlog (German Credit Data) Data Set " qui se trouve dans le référentiel de UCI Machine Learning. Vous le trouverez ici :  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Nous allons utiliser le fichier nommé **german.data**. Téléchargez ce fichier sur votre disque dur.  

Ce jeu de données contient des lignes de 20 variables pour 1 000 candidats à un crédit. Ces 20 variables représentent le vecteur de fonctionnalité du jeu de données, qui fournit des caractéristiques permettant d'identifier chaque candidat à un crédit. Une colonne supplémentaire pour chaque ligne représente le risque de crédit de chaque candidat : 700 candidats constituent un faible risque de crédit et 300 un risque élevé.   

Le site web UCI fournit une description des attributs du vecteur de fonctionnalité incluant des informations financières, un historique de crédit, un statut professionnel et des informations personnelles. Une évaluation binaire a été appliquée à chaque candidat, afin d'indiquer s'il constitue un risque de crédit faible ou élevé.  

Nous allons utiliser ces données pour former un modèle d'analyse prédictive. Lorsque nous aurons terminé, notre modèle pourra saisir des informations concernant de nouveaux candidats et prévoir s'ils constituent un risque de crédit faible ou élevé.  

Voici une évolution intéressante. La description du jeu de données précise que classer par erreur une personne comme risque de crédit faible alors qu'elle constitue un risque de crédit élevé est 5 fois plus coûteux pour l'institution financière que commettre l'erreur inverse. Pour intégrer facilement cet avertissement dans notre expérience, il suffit de multiplier par 5 la valeur des entrées représentant une personne constituant un risque de crédit élevé. Ensuite, si le modèle classe par erreur une personne dans la catégorie des risques de crédit faibles, cette erreur sera commise 5 fois, une fois pour chaque doublon. Cela augmentera le coût de cette erreur dans les résultats de formation.  

## Conversion du format du jeu de données
Le jeu de données d'origine utilise un format séparé par des espaces. ML Studio fonctionne mieux avec un fichier séparé par des virgules (CSV). Nous allons donc convertir le jeu de données en remplaçant les espaces par des virgules.  

Pour ce faire, utilisez la commande Windows PowerShell suivante :   

	cat german.data | %{$_ -replace " ",","} | sc german.csv  

Vous pouvez aussi le faire en utilisant la commande Unix sed :  

	sed 's/ /,/g' german.data > german.csv  

## Téléchargement du jeu de données vers ML Studio

Une fois les données converties au format CSV, nous devons les télécharger vers ML Studio.  

1.	Dans ML Studio, cliquez sur **+NOUVEAU** en bas de la fenêtre.
2.	Sélectionnez **JEU DE DONNÉES**.
3.	Sélectionnez **DEPUIS UN FICHIER LOCAL**.
4.	Dans la boîte de dialogue **Télécharger un nouveau jeu de données**, cliquez sur **Parcourir** puis recherchez le fichier **german.csv** que vous avez créé.
5.	Entrez le nom du jeu de données. Pour les besoins de cet exemple, nous allons l'appeler " Données de carte de crédit allemande UCI ".
6.	Pour le type de données, sélectionnez " Fichier CSV générique sans en-tête (.nh.csv) ".
7.	Ajoutez la description de votre choix.
8.	Cliquez sur **OK**.  

![Upload the dataset][1]  

 
Les données sont téléchargées dans un module Jeu de données utilisable dans une expérience.

**Suivant : [Création d'une expérience][create-new]**

[1]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png

<!--HONumber=46--> 
