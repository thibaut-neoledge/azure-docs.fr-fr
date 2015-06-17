<properties 
	pageTitle="Prise en main d'Azure Storage" 
	description="Mise en route de l'utilisation du stockage de tables Azure dans un projet ASP.NET dans Visual Studio" 
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
> - [Mise en route](vs-storage-aspnet-getting-started-tables.md)
> - [Que s'est-il passé ?](vs-storage-aspnet-what-happened.md)

## Prise en main d'Azure Storage (projets ASP.NET)

> [AZURE.SELECTOR]
> - [Objets blob](vs-storage-aspnet-getting-started-blobs.md)
> - [Files d'attente](vs-storage-aspnet-getting-started-queues.md)
> - [Tables](vs-storage-aspnet-getting-started-tables.md)

Le service de stockage de tables Azure vous permet de stocker de grandes quantités de données structurées. Il s'agit d'une banque de données NoSQL qui accepte les appels authentifiés provenant de l'intérieur et de l'extérieur du cloud Azure. Les tables Azure sont idéales pour le stockage des données structurées non relationnelles.  Pour plus d'informations, consultez la page [Utilisation du stockage de tables à partir de .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "How to use Table Storage from .NET").

Ajoutez les déclarations d'espace de noms suivantes en haut de chaque fichier C# pour lequel vous souhaitez accéder à Azure Storage par programmation :

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Table;

#####Obtention de la chaîne de connexion de stockage
Pour pouvoir exploiter une table, vous devez avant tout obtenir la chaîne de connexion du compte de stockage dans lequel résideront les tables. Pour représenter les informations de votre compte de stockage, vous pouvez utiliser le type **CloudStorageAccount**. Pour les projets ASP.NET, vous pouvez utiliser le type **ConfigurationManager** afin d'obtenir votre chaîne de connexion de stockage et les informations sur votre compte de stockage à partir de la configuration du service Azure, comme illustré dans le code suivant :

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      ConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include](../../includes/vs-storage-getting-started-tables-include.md)]

Pour plus d'informations, consultez la page [ASP.NET](http://www.asp.net).

<!--HONumber=46--> 
 