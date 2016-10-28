<properties
	pageTitle="Qu’est-il arrivé à mon projet de tâche web (service connecté Azure Storage de Visual Studio) ? | Microsoft Azure"
	description="Décrit ce qui s’est produit dans un projet de tâche web une fois que vous vous connectez à un compte de stockage à l’aide des services connectés de Visual Studio"
	services="storage"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/15/2016"
	ms.author="tarcher"/>

# Qu’est-il arrivé à mon projet de tâche web (service connecté Azure Storage de Visual Studio) ?

## Références ajoutées

Le package NuGet Azure Storage a été ajouté ou mis à jour dans votre projet Visual Studio. Ce package ajoute les références .NET suivantes :

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.ConfigurationManager**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

## Chaîne de connexion pour Azure Storage ajoutée
Dans le fichier App.config de votre projet, les entrées **AzureWebJobsStorage** et **AzureWebJobsDashboard** ont été mises à jour avec la chaîne de connexion et la clé du compte de stockage sélectionné.

Pour plus d’informations, consultez [Ressources de documentation relatives à Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).

<!---HONumber=AcomDC_0817_2016-->