<properties title="Getting Started with Azure Storage" pageTitle="Prise en main d'Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-what-happened" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Mise en route](/documentation/articles/vs-storage-cloud-services-getting-started-blobs/)
> - [Que s'est-il passé ?](/documentation/articles/vs-storage-cloud-services-what-happened/)

###<span id="whathappened">Qu'est-il arrivé à mon projet ?</span>

###### Références ajoutées

Le package NuGet Azure Storage a été ajouté à votre projet Visual Studio.  
Ce package ajoute les références .NET suivantes :

- Microsoft.Data.Edm
- Microsoft.Data.OData
- Microsoft.Data.Services.Client
- Microsoft.WindowsAzure.Configuration
- Microsoft.WindowsAzure.Storage
- Newtonsoft.Json
- System.Data
- System.Spatial

######Chaîne de connexion pour Azure Storage ajoutée 
Les éléments ont été créés avec la clé et la chaîne de connexion du compte de stockage sélectionné. Des modifications ont été apportées aux fichiers suivants :

- ServiceDefinition.csdef
- ServiceConfiguration.Cloud.cscfg
- ServiceConfiguration.Local.cscfg

