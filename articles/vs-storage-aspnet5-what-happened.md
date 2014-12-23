<properties title="Getting Started with Azure Storage" pageTitle="Prise en main d'Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-what-happened" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Mise en route](/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
> - [Que s'est-il passé ?](/documentation/articles/vs-storage-aspnet5-what-happened/)

###<span id="whathappened">Qu'est-il arrivé à mon projet ?</span>

##### Références ajoutées

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

De plus, le package NuGet nommé **Microsoft.Framework.ConfigurationModel.Json** a été ajouté.

#####Chaîne de connexion pour Azure Storage ajoutée 
Dans le fichier config.json de votre projet, un élément a été créé avec la clé et la chaîne de connexion du compte de stockage sélectionné.

Pour plus d'informations, consultez la page [ASP.NET 5](http://www.asp.net/vnext).
