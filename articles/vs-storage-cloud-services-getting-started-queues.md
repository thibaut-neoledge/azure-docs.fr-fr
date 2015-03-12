<properties 
	pageTitle="Prise en main d'Azure Storage" 
	description="Mise en route de l'utilisation du stockage de files d'attente Azure dans un projet de service cloud dans Visual Studio" 
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
> - [Mise en route](/documentation/articles/vs-storage-cloud-services-getting-started-queues/)
> - [Que s'est-il passé ?](/documentation/articles/vs-storage-cloud-services-what-happened/)

## Prise en main d'Azure Storage (Projets de service cloud)

> [AZURE.SELECTOR]
> - [Objets blob](/documentation/articles/vs-storage-cloud-services-getting-started-blobs)
> - [Queues](/documentation/articles/vs-storage-cloud-services-getting-started-queues/)
> - [Tables](/documentation/articles/vs-storage-cloud-services-getting-started-tables/)

Le service de stockage de files d'attente Azure permet de stocker un grand nombre de messages accessibles partout dans le monde via des appels authentifiés avec HTTP ou HTTPS. Un simple message de file d'attente peut avoir une taille de 64 Ko et une file d'attente peut contenir des millions de messages, jusqu'à la limite de capacité totale d'un compte de stockage. Pour plus d'informations, consultez la page [Utilisation du service de stockage de files d'attente à partir de .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-queues/ "How to use Queue Storage from .NET").

Ajoutez les déclarations d'espace de noms suivantes en haut de chaque fichier C# pour lequel vous souhaitez accéder à Azure Storage par programmation :

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

#####Obtention de la chaîne de connexion de stockage
Pour pouvoir exploiter une file d'attente, vous devez avant tout obtenir la chaîne de connexion du compte de stockage dans lequel résidera la file d'attente. Pour représenter les informations de votre compte de stockage, vous pouvez utiliser le type **CloudStorageAccount**. Pour les projets de service cloud, vous pouvez utiliser le type **CloudConfigurationManager** afin d'extraire votre chaîne de connexion de stockage et les informations de votre compte de stockage à partir de la configuration du service Azure, comme illustré dans le code suivant :

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-queues-include](../includes/vs-storage-getting-started-queues-include.md)]


<!--HONumber=46--> 
