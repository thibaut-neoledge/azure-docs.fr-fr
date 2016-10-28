<properties
	pageTitle="Module PowerShell pour Machine Learning | Microsoft Azure"
	description="Le module PowerShell pour Azure Machine Learning est disponible en version préliminaire publique. Utilisez PowerShell pour créer et gérer des espaces de travail, des expériences, des services web, etc."
	keywords="expérience,régression linéaire,algorithmes d’apprentissage automatique,didacticiel d’apprentissage automatique,techniques de modélisation prédictive,expérience de science de données"
	services="machine-learning"
	documentationCenter=""
	authors="hning86"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/05/2016"
	ms.author="garye;haining"/>

# Module PowerShell pour Microsoft Azure Machine Learning

Le module PowerShell pour Azure Machine Learning est un outil puissant qui vous permet d’utiliser Windows PowerShell pour gérer des espaces de travail, des expériences, des jeux de données, des services web, etc.

Vous pouvez consulter la documentation et télécharger le module, ainsi que le code source complet, à l’adresse [https://aka.ms/amlps](https://aka.ms/amlps).

## Qu’est-ce que le module Machine Learning PowerShell ?

Le module Machine Learning PowerShell est un module DLL basé sur .NET qui vous permet de gérer entièrement les espaces de travail, les expériences, les jeux de données, les services web et les points de terminaison de services web Azure Machine Learning depuis Windows PowerShell. En plus du module, vous pouvez télécharger le code source complet, qui inclut une [couche d’API C#](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs) bien distincte. Cela signifie que vous pouvez référencer cette DLL depuis votre propre projet .NET et gérer Azure Machine Learning via le code .NET. En outre, la DLL repose sur des API REST sous-jacentes que vous pouvez exploiter directement depuis le client de votre choix.

## Quelles sont les possibilités offertes par le module PowerShell ?

Voici quelques-unes des tâches que vous pouvez effectuer avec ce module PowerShell. Pour plus d’informations sur celles-ci et les nombreuses autres fonctions disponibles, consultez la [documentation complète](https://aka.ms/amlps).

- Approvisionner un nouvel espace de travail à l’aide d’un certificat de gestion ([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
- Exporter et importer un fichier JSON représentant un graphique d’expérience ([Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) et [Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
- Exécuter une expérience ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
- Créer un service web à partir d’une expérimentation prédictive ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
- Créer un point de terminaison sur un service web publié ([Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
- Appeler un point de terminaison de service web RRS ou BES ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) et [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

Voici un exemple rapide d’utilisation de PowerShell pour exécuter une expérience existante :

		#Find the first Experiment named “xyz”
		$exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
		#Run the Experiment
		Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Pour un cas d’utilisation plus approfondi, consultez l’article suivant sur l’utilisation du module PowerShell pour automatiser une tâche souvent demandée : [Créer de nombreux modèles Machine Learning et points de terminaison de service web à partir d’une expérience à l’aide de PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md).

## Comment faire pour démarrer ?

Pour commencer à utiliser Machine Learning PowerShell, téléchargez le [package de la version](https://github.com/hning86/azuremlps/releases) dans GitHub et suivez les [instructions d’installation](https://github.com/hning86/azuremlps/blob/master/README.md). Vous devez débloquer la DLL téléchargée/décompressée et l’importer dans votre environnement PowerShell. La plupart des applets de commande nécessitent que vous fournissiez l’ID de l’espace de travail, le jeton d’autorisation de l’espace de travail et la région Azure dans laquelle l’espace de travail se trouve. Le moyen le plus simple pour fournir ces éléments, détaillé dans les instructions d’installation, consiste à utiliser un fichier config.json par défaut. Bien entendu, vous pouvez également cloner l’arborescence Git et modifier/compiler le code localement à l’aide de Visual Studio.

## Étapes suivantes

Le module PowerShell continuera à être amélioré et développé pendant toute la durée de la version préliminaire. Pour vous tenir informé, consultez régulièrement le blog [Cortana Intelligence and Machine Learning](https://blogs.technet.microsoft.com/machinelearning/) (en anglais).

<!---HONumber=AcomDC_0914_2016-->