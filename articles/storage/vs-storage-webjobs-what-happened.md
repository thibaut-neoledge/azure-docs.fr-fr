<properties 
	pageTitle="Prise en main d'Azure Storage" 
	description="Décrit ce qui s’est produit lors de la création d’un stockage Azure dans un projet de tâche web Azure Visual Studio" 
	services="storage" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="patshea123"/>

# Qu'est-il arrivé à mon projet ?

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-webjobs-getting-started-blobs.md)
> - [What Happened](vs-storage-webjobs-what-happened.md)

###<span id="whathappened">Qu’est-il arrivé à mon projet ?</span>

##### Références ajoutées

Le package NuGet Azure Storage a été ajouté ou mis à jour dans votre projet Visual Studio. Ce package ajoute les références .NET suivantes :

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.ConfigurationManager`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

#####Chaîne de connexion pour Azure Storage ajoutée 
Dans le fichier App.config de votre projet, les éléments `AzureWebJobsStorage` et `AzureWebJobsDashboard` ont été mis à jour avec la chaîne de connexion et la clé du compte de stockage sélectionné.

Pour plus d’informations, consultez [Ressources Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).

<!---HONumber=August15_HO6-->