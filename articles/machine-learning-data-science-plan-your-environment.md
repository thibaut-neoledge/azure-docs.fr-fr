<properties 
	pageTitle="Planifier votre environnement de science des données Cloud | Azure" 
	description="Planifier votre environnement de science des données Cloud" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab"
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="mohabib;bradsev" /> 


# Planifier votre environnement de science des données Azure Machine Learning

Lorsque vous créez un environnement de science des données Azure Machine Learning, vous devez prendre certaines décisions. Ces choix sont fonction du type des données, de leur taille, de leur emplacement source et de leur destination dans le Cloud. Le processus de science des données Cloud est une suite complète de tâches, qui part d'une source de données pour créer un modèle et le publier sous la forme d'un service Web Azure proposé dans des applications.

Ces étapes sont présentées dans le **[diagramme du processus de science des données](machine-learning-data-science-how-to-create-machine-learning-service.md)**. Pour en savoir plus sur chaque étape de ce processus, cliquez sur les éléments concernés dans le [diagramme](machine-learning-data-science-how-to-create-machine-learning-service.md).

Cet article analyse les questions à se poser lors de la création de votre environnement de science des données Cloud, répertorie les ressources et outils utiles, et fournit des conseils sur l'utilisation du diagramme du processus de science des données Cloud.

## Questions à se poser

Avant de créer votre environnement de science des données Cloud, posez-vous les questions suivantes :

1. **Où se trouvent vos données ?** (Cet emplacement est appelé ***source des données***.) Par exemple :
	- Les données sont accessibles au public à une adresse HTTP.
	- Les données résident dans un fichier local ou sur le réseau.
	- Les données sont contenues dans une base de données SQL Server.
	- Les données sont stockées dans un conteneur Azure Storage.
2. **Quel est le format de vos données ?** Par exemple :
    - Fichiers de valeurs séparées par une virgule ou une tabulation, non compressés.
    - Fichiers de valeurs séparées par une virgule ou une tabulation, compressés.
	- Blobs Azure compressés ou non.
	- Tables SQL Server.
3. **Quel volume vos données représentent-elles ?**
    - Petit : moins de 2 Go.
    - Moyen : entre 2 Go et 10 Go.
	- Grand : supérieur à 10 Go.
	- Très grand : plusieurs centaines de Go.
4. **Connaissez-vous bien les données ?**
    - Avez-vous besoin d'analyser les données pour connaître leur schéma, la distribution des variables, les valeurs manquantes, etc. ? 
	- Les données nécessitent-elles un prétraitement ou un nettoyage pour présenter un format tabulaire ? 
5. **Prévoyez-vous (ou êtes-vous en mesure) de placer toutes les données dans Azure ?**
    - Oui, il est prévu de copier l'ensemble des données dans le Cloud à des fins de traitement.
	- Non, seule une partie des données sera copiée dans Azure.
6. **Quelle destination privilégiez-vous dans le Cloud Azure ?** Par exemple :
	- Placer les données dans des blobs Azure Storage.
	- Stocker les données dans des disques durs virtuels Azure montables.
	- Charger les données dans une base de données SQL Server sur une machine virtuelle Azure.
	- Mapper les données à des tables Hive HDInsight Azure.

## Ressources de science des données Cloud dans Azure

Selon votre scénario, il se peut que vous ayez besoin des éléments suivants :

1.  Outils Azure : [Azure PowerShell SDK](install-configure-powershell.md), [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/), [AzCopy](storage/storage-use-azcopy.md), entre autres
2.  Machines virtuelles Azure exécutant SQL Server
3.  Azure HDInsight (Hadoop)
4.  Réseaux virtuels Azure sur site pour le partage de fichiers Azure
5.  Azure Data Factory pour les déplacements de données planifiés


## Comment utiliser le diagramme du processus de science des données Cloud

Le [diagramme du processus de science des données Cloud](machine-learning-data-science-how-to-create-machine-learning-service.md) présente divers exercices de science des données. Il détaille les principales étapes d'un processus type de science des données. Selon le cas, certaines peuvent être facultatives. De plus, le processus est itératif et la séquence des étapes peut varier dans un exercice donné. Vos réponses aux questions ci-dessus vous aideront à identifier les étapes nécessaires dans votre situation et les conditions dans lesquelles les itérations sont incontournables.

Pour obtenir des exemples basés sur le volume de données, l'emplacement source et le référentiel cible dans Azure, consultez l'article [Processus de science des données Cloud dans Azure Machine Learning](hmachine-learning-data-science-plan-sample-scenarios.md).



<!--HONumber=49-->