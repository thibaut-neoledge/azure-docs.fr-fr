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
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Getting Started](/documentation/articles/vs-storage-aspnet-getting-started-blobs/)
> - [What Happened](/documentation/articles/vs-storage-aspnet-what-happened/)

## Prise en main d'Azure Storage (projets ASP.NET)

> [AZURE.SELECTOR]
> - [Blobs](/documentation/articles/vs-storage-aspnet-getting-started-blobs/)
> - [Queues](/documentation/articles/vs-storage-aspnet-getting-started-queues/)
> - [Tables](/documentation/articles/vs-storage-aspnet-getting-started-tables/)

Azure Blob storage is a service for storing large amounts of unstructured data that can be accessed from anywhere in the world via HTTP or HTTPS. Les objets blob peuvent être de toutes tailles. Il peut s'agir d'images, de fichiers audio ou vidéo, de données brutes ou de fichiers de documents.

Pour commencer, vous devez créer un compte de stockage Azure, puis un ou plusieurs conteneurs pour le stockage. Par exemple, vous pouvez créer un compte de stockage appelé " Scrapbook ", puis créer des conteneurs pour ce compte de stockage, un que vous appellerez " images " pour stocker les images et un autre nommé " audio " pour vos fichiers audio. Une fois que vous avez créé les conteneurs, vous pouvez y charger des fichiers blob. See [How to use Blob Storage from .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/ "How to use Blob Storage from .NET") for more information on programmatically manipulating blobs.

Ajoutez les déclarations d'espace de noms suivantes en haut de chaque fichier C# pour lequel vous souhaitez accéder à Azure Storage par programmation :
	
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

##### Obtention de la chaîne de connexion de stockage
Before you can do anything with a blob, you need to get the connection string for the storage account the blobs will live in. You can use the **CloudStorageAccount** type to represent your storage account information. For ASP.NET projects, you can you use the **ConfigurationManager** type to retrieve your storage connection string and storage account information from the Azure service configuration, as shown in the following code.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[AZURE.INCLUDE [vs-storage-getting-started-blobs-include](../includes/vs-storage-getting-started-blobs-include.md)]

For more information, see [ASP.NET](http://www.asp.net).
<!--HONumber=42-->
