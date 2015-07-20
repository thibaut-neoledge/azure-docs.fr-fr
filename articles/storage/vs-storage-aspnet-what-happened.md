<properties 
	pageTitle="Prise en main d'Azure Storage" 
	description="Décrit ce qui s'est produit lors de la création d'un stockage Azure dans un projet d'ASP.NET Visual Studio" 
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
> - [Getting Started](vs-storage-aspnet-getting-started-blobs.md)
> - [What Happened](vs-storage-aspnet-what-happened.md)

###<span id="whathappened">Qu’est-il arrivé à mon projet ?</span>

##### Références ajoutées

Le package NuGet Azure Storage a été ajouté à votre projet Visual Studio. Ce package ajoute les références .NET suivantes :

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.Configuration`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

#####Chaîne de connexion pour Azure Storage ajoutée 
Dans le fichier web.config de votre projet, un élément a été créé avec la clé et la chaîne de connexion du compte de stockage sélectionné.

Pour plus d'informations, consultez la page [ASP.NET](http://www.asp.net).

<!---HONumber=July15_HO2-->