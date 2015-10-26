<properties 
	pageTitle="Planifier un environnement d'analyse avancée Machine Learning | Microsoft Azure" 
	description="Planifier votre environnement d'analyse avancée en tenant compte des questions clés." 
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
	ms.date="07/22/2015" 
	ms.author="mohabib;bradsev" />


# Planifier votre environnement d'analyse avancée Azure Machine Learning

Quel scénario correspond à votre problème d'analyse lorsque vous vous apprêtez à configurer un environnement pour effectuer une analyse avancée avec Azure Machine Learning ? Les choix que vous effectuez, à propos des ressources requises, dépendent du type des données, de leur taille, de leur emplacement source et de leur destination dans le cloud. Cet article aborde ces questions qui vous permettront d’identifier votre scénario.

Une fois le scénario approprié identifié, le flux de travail du processus d'analyse avancée et technologie ADAPT (Advanced Analytics Process and Technology) qui est présenté dans le [Parcours d'apprentissage : création de solutions d’analyse avancées dans Azure](machine-learning-data-science-how-to-create-machine-learning-service.md) vous guide à travers une série de tâches, de l'obtention d'un jeu de données à la création et la publication d'un modèle en tant que service web Azure que les applications peuvent utiliser.

Cette rubrique énumère également quelques ressources et outils qui sont utilisés par ce processus d'analyse avancée.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Répondez à ces questions
Répondez à ces questions pour déterminer quel scénario utiliser avant de créer votre environnement d'analyse avancée.

1. **Où se trouvent vos données ?** (Cet emplacement est appelé la ***source des données***.) Par exemple :
	- Les données sont accessibles au public à une adresse HTTP.
	- Les données résident dans un fichier local ou sur le réseau.
	- Les données sont contenues dans une base de données SQL Server.
	- Les données sont stockées dans un conteneur de stockage Azure.
2. **Quel est le format de vos données ?** Par exemple :
    - Fichiers de valeurs séparées par une virgule ou une tabulation, non compressés.
    - Fichiers de valeurs séparées par une virgule ou une tabulation, compressés.
	- Blobs Azure compressés ou non.
	- Tables SQL Server.
3. **Quel volume vos données représentent-elles ?**
    - Petit : moins de 2 Go
    - Moyen : entre 2 Go et 10 Go
	- Grand : supérieur à 10 Go
	- Très grand : plusieurs centaines de Go
4. **Connaissez-vous bien les données ?**
    - Avez-vous besoin d’analyser les données pour connaître leur schéma, la distribution des variables, les valeurs manquantes, etc. ? 
	- Les données nécessitent-elles un prétraitement ou un nettoyage pour présenter un format tabulaire ? 
5. **Prévoyez-vous (ou êtes-vous en mesure) de placer toutes les données dans le stockage Azure ?**
    - Oui, il est prévu de copier l’ensemble des données dans le Cloud à des fins de traitement.
	- Non, seule une partie des données sera copiée dans Azure.
6. **Quelle destination privilégiez-vous dans le Cloud Azure ?** Par exemple :
	- Placer les données dans des blobs Azure Storage.
	- Stocker les données dans des disques durs virtuels Azure montables.
	- Charger les données dans une base de données SQL Server sur une machine virtuelle Azure.
	- Mapper les données à des tables Hive HDInsight Azure.

## Quel est votre scénario ?
Une fois que vous avez répondu aux questions de la section précédente, vous êtes prêt à déterminer le scénario qui vous convient le mieux. Les exemples de scénarios sont présentés dans la rubrique [Scénarios d’analyses avancées dans Azure Machine Learning](../machine-learning-data-science-plan-sample-scenarios.md).

## Ressources d'analyse avancée dans Azure
Selon votre scénario, vous aurez peut-être besoin de quelques outils et ressources.

1.  Outils Azure : 
	* 	[Kit de développement logiciel (SDK) Azure PowerShell](../install-configure-powershell.md) 
	* 	[Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/)
	* 	[AzCopy](../storage-use-azcopy.md)
2.  Machines virtuelles Azure exécutant SQL Server
3.  Azure HDInsight (Hadoop)
4.  Réseaux virtuels Azure sur site pour le partage de fichiers Azure
5.  Azure Data Factory pour les déplacements de données planifiés






 

<!---HONumber=Oct15_HO3-->