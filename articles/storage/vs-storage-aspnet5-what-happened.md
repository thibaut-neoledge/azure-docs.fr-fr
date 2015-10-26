<properties
	pageTitle="Qu’est-il arrivé à mon projet ASP.NET 5 (services connectés de Visual Studio) ? | Microsoft Azure Storage"
	description="Décrit ce qui se produit une fois que vous vous connectez à un compte de stockage Azure dans un projet Visual Studio ASP.NET 5 à l’aide des services connectés de Visual Studio"
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
	ms.date="09/03/2015"
	ms.author="patshea"/>

# Qu’est-il arrivé à mon projet ASP.NET 5 (services connectés Azure Storage de Visual Studio) ?

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-blobs.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

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

De plus, le package NuGet nommé **Microsoft.Framework.Configuration.Json** a été ajouté.

## Chaîne de connexion pour Azure Storage ajoutée
Dans le fichier config.json de votre projet, un élément a été créé avec la clé et la chaîne de connexion du compte de stockage sélectionné.

Pour plus d’informations, consultez [ASP.NET 5](http://www.asp.net/vnext).

<!---HONumber=Oct15_HO3-->