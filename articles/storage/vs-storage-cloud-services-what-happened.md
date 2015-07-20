<properties 
	pageTitle="Prise en main d'Azure Storage" 
	description="Décrit ce qui s'est produit lors de la création d'un stockage Azure dans un projet de service cloud Visual Studio" 
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
	ms.date="04/20/2015" 
	ms.author="patshea123"/>

# Qu'est-il arrivé à mon projet ?

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-cloud-services-getting-started-blobs.md)
> - [What Happened](vs-storage-cloud-services-what-happened.md)

###<span id="whathappened">Qu’est-il arrivé à mon projet ?</span>

###### Références ajoutées

Le package NuGet Azure Storage a été ajouté à votre projet Visual Studio. Ce package ajoute les références .NET suivantes :

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.Configuration`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

######Chaîne de connexion pour Azure Storage ajoutée 
Les éléments ont été créés avec la clé et la chaîne de connexion du compte de stockage sélectionné. Des modifications ont été apportées aux fichiers suivants :

- `ServiceDefinition.csdef`
- `ServiceConfiguration.Cloud.cscfg`
- `ServiceConfiguration.Local.cscfg`

 

<!---HONumber=July15_HO2-->