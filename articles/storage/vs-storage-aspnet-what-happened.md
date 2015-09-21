<properties
	pageTitle="Qu’est-il arrivé à mon projet ASP.NET ? | Microsoft Azure | Services connectés de Visual Studio"
	description="Décrit ce qui se produit quand Azure Storage est ajouté à un projet ASP.NET à l’aide des services connectés de Visual Studio"
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

# Qu’est-il arrivé à mon projet ASP.NET (service connecté Azure Storage de Visual Studio) ?

> [AZURE.SELECTOR]
> - [Getting started](vs-storage-aspnet-getting-started-blobs.md)
> - [What happened](vs-storage-aspnet-what-happened.md)

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

##Chaîne de connexion pour Azure Storage ajoutée
Dans le fichier web.config de votre projet, un élément a été créé avec la clé et la chaîne de connexion du compte de stockage sélectionné.

Pour plus d'informations, consultez la page [ASP.NET](http://www.asp.net).

<!---HONumber=Sept15_HO2-->