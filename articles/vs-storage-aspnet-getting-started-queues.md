<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-aspnet-getting-started-intro](../includes/vs-storage-aspnet-getting-started-intro.md)]

### Prise en main d'Azure Storage

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/vs-storage-aspnet-getting-started-blobs" title="Objets blob" class="current">Objets blob</a><a href="/fr-fr/documentation/articles/vs-storage-aspnet-getting-started-queues" title="Files d'attente">Files d'attente</a><a href="/fr-fr/documentation/articles/vs-storage-aspnet-getting-started-tables" title="Tables">Tables</a></div>

Le service de stockage de files d'attente Azure permet de stocker un grand nombre de messages accessibles partout dans le monde via des appels authentifiés avec HTTP ou HTTPS. Un simple message de file d’attente peut avoir une taille de 64 Ko et une file d’attente peut contenir des millions de messages, jusqu’à la limite de capacité totale d’un compte de stockage. Pour plus d'informations, consultez la page [Utilisation du service de stockage de files d'attente à partir de .NET][Utilisation du service de stockage de files d'attente à partir de .NET].

Pour accéder par programmation aux files d'attente des projets ASP.NET, procédez comme suit :

1.  Accédez à l'assembly Microsoft.WindowsAzure.Storage.dll. Pour ce faire, vous pouvez utiliser la commande NuGet. Cliquez avec le bouton droit de la souris sur votre projet dans l'Explorateur de solutions, puis sélectionnez Gérer les packages NuGet. Effectuez une recherche en ligne sur « WindowsAzure.Storage », puis cliquez sur Installer pour lancer l'installation du package Azure Storage et de ses dépendances. Ajoutez une référence à cet assembly dans votre projet.
2.  Ajoutez les déclarations d'espace de noms suivantes en haut de chaque fichier C# pour lequel vous souhaitez accéder à Azure Storage par programmation :

    using Microsoft.WindowsAzure.Storage ;
    using Microsoft.WindowsAzure.Storage.Auth ;
    using Microsoft.WindowsAzure.Storage.Queue ;

### Obtention de la chaîne de connexion de stockage

Pour pouvoir exploiter une file d'attente, vous devez avant tout obtenir la chaîne de connexion du compte de stockage dans lequel résideront les files d'attente. Pour représenter les informations de votre compte de stockage, vous pouvez utiliser le type **CloudStorageAccount**. Pour les projets ASP.NET, vous pouvez utiliser le type **ConfigurationManager** afin d'obtenir votre chaîne de connexion de stockage et les informations sur votre compte de stockage à partir de la configuration du service Azure, comme illustré dans le code suivant :

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      ConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-queues-include](../includes/vs-storage-getting-started-queues-include.md)]

  [vs-storage-aspnet-getting-started-intro]: ../includes/vs-storage-aspnet-getting-started-intro.md
  [Objets blob]: /fr-fr/documentation/articles/vs-storage-aspnet-getting-started-blobs "Objets blob"
  [Files d'attente]: /fr-fr/documentation/articles/vs-storage-aspnet-getting-started-queues "Files d'attente"
  [Tables]: /fr-fr/documentation/articles/vs-storage-aspnet-getting-started-tables "Tables"
  [Utilisation du service de stockage de files d'attente à partir de .NET]: http://azure.microsoft.com/fr-fr/documentation/articles/storage-dotnet-how-to-use-queues/
  [vs-storage-getting-started-queues-include]: ../includes/vs-storage-getting-started-queues-include.md
