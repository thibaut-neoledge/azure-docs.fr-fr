<properties 
	pageTitle="Utilisation du stockage Azure dans les applications Windows Store | Microsoft Azure" 
	description="Apprenez à utiliser les objets blob, les files d'attente et les tables Azure pour stocker les données d'une application Windows Store." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="tamram"/>





# Utilisation d'Azure Storage dans les applications Windows Store

## Vue d'ensemble

Ce guide montre comment commencer le développement d'une application Windows Store utilisant Azure Storage.

## Téléchargement des outils nécessaires ##

- [Visual Studio 2012](http://msdn.microsoft.com/library/windows/apps/br211384) permet de générer, de déboguer, de localiser, de mettre en package et de déployer des applications Windows Store en toute simplicité.
- [Azure Storage Client Library pour Windows Runtime](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/05/windows-azure-storage-client-library-for-windows-runtime.aspx) fournit une bibliothèque de classes qui permet d’utiliser Azure Storage.
- [WCF Data Services Tools for Windows Store Apps](http://www.microsoft.com/download/details.aspx?id=30714) complète l'expérience Ajouter une référence de service avec la prise en charge OData côté client pour les applications Windows Store dans Visual Studio 2012 et versions ultérieures.

## Développement d'applications ##

### Préparation

Créez un projet d'application Windows Store dans Visual Studio 2012 ou version ultérieure :

![store-apps-storage-vs-project][store-apps-storage-vs-project]

Ensuite, ajoutez une référence à la bibliothèque cliente Azure Storage. Pour cela, cliquez avec le bouton droit sur **Références**, puis choisissez **Ajouter une référence** et recherchez la bibliothèque cliente Storage pour Windows Runtime que vous avez téléchargée :

![store-apps-storage-choose-library][store-apps-storage-choose-library]

### Utilisation de la bibliothèque avec les services blob et de file d'attente

À ce stade, votre application est prête à appeler les services Blob et de file d'attente. Ajoutez les instructions **using** suivantes afin que les types Azure Storage soient référencés directement :

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    
Ensuite, ajoutez un bouton sur votre page. Ajoutez le code suivant dans son événement **Cliquer** et modifiez votre méthode de gestionnaire d'événements grâce au [mot clé async](http://msdn.microsoft.com/library/vstudio/hh156513.aspx) :
    
    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();
    
Ce code part du principe que vous avez deux variables de chaîne, *accountName* et *accountKey*, qui représentent le nom de votre compte de stockage et la clé de compte qui lui est associée.

Générez et exécutez l'application. Si vous cliquez sur le bouton, l'application vérifiera tout d'abord si un conteneur nommé *container1* existe dans votre compte et le créera si ce n'est pas le cas.

### Utilisation de la bibliothèque avec le service de Table

Les types utilisés pour communiquer avec le service de Table dépendent des services de données WCF pour la bibliothèque d'applications Windows Store. Ensuite, ajoutez une référence aux bibliothèques WCF requises en utilisant la console du Gestionnaire de package :

![store-apps-storage-package-manager][store-apps-storage-package-manager]

Utilisez la commande suivante pour faire pointer le Gestionnaire de package sur l'emplacement de votre ordinateur :
    
    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

Cette commande ajoutera automatiquement toutes les références requises à votre projet. Si vous ne souhaitez pas utiliser la console du Gestionnaire de package, vous pouvez également ajouter le dossier NuGet de services de données WCF sur votre ordinateur local dans la liste des sources de package, puis ajouter la référence via l'interface utilisateur comme décrit dans [Gestion des packages NuGet à l'aide de la boîte de dialogue](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog).

Une fois le package NuGet de services de données WCF référencé, modifiez le code dans l'événement **Cliquer** de votre bouton :
    
    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();
    
Ce code vérifie qu'une table nommée *table1* existe dans votre compte et la crée si ce n'est pas le cas.

Vous pouvez également ajouter une référence dans Microsoft.WindowsAzure.Storage.Table.dll, disponible dans le même package que vous avez téléchargé. Cette bibliothèque contient des fonctionnalités supplémentaires, telles qu'une sérialisation basée sur la réflexion et des requêtes génériques. Notez que cette bibliothèque ne prend pas en charge le code JavaScript.



[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png
 

<!---HONumber=August15_HO7-->