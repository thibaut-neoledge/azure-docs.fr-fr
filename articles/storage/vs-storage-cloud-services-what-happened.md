<properties
    pageTitle="Qu’est-il arrivé à mon projet service cloud ? | Microsoft Azure | Services connectés de Visual Studio"
	description="Décrit ce qui se produit dans un projet services cloud une fois que vous vous connectez à un compte de stockage Azure à l’aide des services connectés de Visual Studio"
    services="storage"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/03/2015"
	ms.author="tarcher"/>

# Qu’est-il arrivé à mon projet services cloud (service connecté Azure Storage de Visual Studio) ?

> [AZURE.SELECTOR]
> - [Getting started](vs-storage-cloud-services-getting-started-blobs.md)
> - [What happened](vs-storage-cloud-services-what-happened.md)


## Références ajoutées

Le package NuGet Azure Storage a été ajouté à votre projet Visual Studio. Ce package ajoute les références .NET suivantes :

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.Configuration**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

## Chaîne de connexion pour Azure Storage ajoutée
Les éléments ont été créés avec la clé et la chaîne de connexion du compte de stockage sélectionné. Des modifications ont été apportées aux fichiers suivants :

- **ServiceDefinition.csdef**
- **ServiceConfiguration.Cloud.cscfg**
- **ServiceConfiguration.Local.cscfg**

<!---HONumber=Nov15_HO3-->