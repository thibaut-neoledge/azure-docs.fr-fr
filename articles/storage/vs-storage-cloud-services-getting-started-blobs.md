<properties 
	pageTitle="Prise en main d'Azure Storage" 
	description="Mise en route de l'utilisation du stockage d'objets blob Azure dans un projet de service cloud dans Visual Studio" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Mise en route](vs-storage-cloud-services-getting-started-blobs.md)
> - [Que s'est-il passé ?](vs-storage-cloud-services-what-happened.md)

## Prise en main d'Azure Storage (Projets de service cloud)

> [AZURE.SELECTOR]
> - [Objets blob](vs-storage-cloud-services-getting-started-blobs.md)
> - [Files d'attente](vs-storage-cloud-services-getting-started-queues.md)
> - [Tables](vs-storage-cloud-services-getting-started-tables.md)

Le stockage d'objets blob Azure est un service permettant de stocker de grandes quantités de données non structurées accessibles depuis n'importe où dans le monde via HTTP ou HTTPS. Les objets blob peuvent être de toutes tailles. Il peut s'agir d'images, de fichiers audio ou vidéo, de données brutes ou de fichiers de documents.

Pour commencer, vous devez créer un compte de stockage Azure, puis un ou plusieurs conteneurs pour le stockage. Par exemple, vous pouvez créer un compte de stockage appelé " Scrapbook ", puis créer des conteneurs pour ce compte de stockage, un que vous appellerez " images " pour stocker les images et un autre nommé " audio " pour vos fichiers audio. Une fois que vous avez créé les conteneurs, vous pouvez y charger des fichiers blob. Pour plus d'informations sur la manipulation par programme des objets blob, consultez la page [Utilisation du stockage d'objets blob à partir de .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/ "How to use Blob Storage from .NET").

Ajoutez les déclarations d'espace de noms suivantes en haut de chaque fichier C# pour lequel vous souhaitez accéder à Azure Storage par programmation :

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

#####Obtention de la chaîne de connexion de stockage
Pour pouvoir exploiter un objet blob, vous devez avant tout obtenir la chaîne de connexion du compte de stockage dans lequel résideront les objets blob. Pour représenter les informations de votre compte de stockage, vous pouvez utiliser le type **CloudStorageAccount**. Pour les projets de service cloud, vous pouvez utiliser le type **CloudConfigurationManager** afin d'extraire votre chaîne de connexion de stockage et les informations de votre compte de stockage à partir de la configuration du service Azure, comme illustré dans le code suivant :

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-blobs-include](../../includes/vs-storage-getting-started-blobs-include.md)]

<!--HONumber=46--> 
 