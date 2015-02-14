<properties 
	pageTitle="Prise en main d'Azure Storage" 
	description="" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Mise en route](/documentation/articles/vs-storage-aspnet-getting-started-blobs/)
> - [Que s'est-il passé](/documentation/articles/vs-storage-aspnet-what-happened/)

### <span id="whathappened">Qu'est-il arrivé à mon projet ?</span>

##### Références ajoutées

Le package NuGet Azure Storage a été ajouté à votre projet Visual Studio.  
Ce package ajoute les références .NET suivantes :

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.Configuration`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

##### Chaîne de connexion pour Azure Storage ajoutée 
Dans le fichier web.config de votre projet, un élément a été créé avec la clé et la chaîne de connexion du compte de stockage sélectionné.

Pour plus d'informations, consultez la page [ASP.NET](http://www.asp.net).<!--HONumber=42-->
