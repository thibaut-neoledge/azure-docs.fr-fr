<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-cloud-services-getting-started-intro](../includes/vs-storage-cloud-services-getting-started-intro.md)]

# Prise en main d'Azure Storage

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/vs-storage-cloud-services-getting-started-blobs" title="Objets blob" class="current">Objets blob</a><a href="/fr-fr/documentation/articles/vs-storage-cloud-services-getting-started-queues" title="Files d'attente">Files d'attente</a><a href="/fr-fr/documentation/articles/vs-storage-cloud-services-getting-started-tables" title="Tables">Tables</a></div>

Le stockage d’objets blob Azure est un service permettant de stocker de grandes quantités de données accessibles depuis n’importe où dans le monde via HTTP ou HTTPS. Les objets blob peuvent être de toutes tailles. Il peut s'agir d'images, de fichiers audio ou vidéo, de données brutes ou de fichiers de documents.

Pour commencer, vous devez créer un compte de stockage Azure, puis un ou plusieurs conteneurs pour le stockage. Par exemple, vous pouvez créer un compte de stockage appelé « Scrapbook », puis créer des conteneurs pour ce compte de stockage, un que vous appellerez « images » pour stocker les images et un autre nommé « audio » pour vos fichiers audio. Une fois que vous avez créé les conteneurs, vous pouvez y charger des fichiers blob. Pour plus d'informations sur la manipulation d'objets blob par programmation, consultez la page [Utilisation du stockage d'objets blob à partir de .NET][Utilisation du stockage d'objets blob à partir de .NET].

Pour utiliser la programmation afin d'accéder aux objets blob d'un projet de service cloud, procédez comme suit :

1.  Accédez à l'assembly Microsoft.WindowsAzure.Storage.dll. Pour ce faire, vous pouvez utiliser la commande NuGet. Cliquez avec le bouton droit de la souris sur votre projet dans l'Explorateur de solutions, puis sélectionnez Gérer les packages NuGet. Effectuez une recherche en ligne sur « WindowsAzure.Storage », puis cliquez sur Installer pour lancer l'installation du package Azure Storage et de ses dépendances. Ajoutez une référence à cet assembly dans votre projet.
2.  Ajoutez les déclarations d'espace de noms suivantes en haut de chaque fichier C# pour lequel vous souhaitez accéder à Azure Storage par programmation :

    using Microsoft.WindowsAzure.Storage ;
    using Microsoft.WindowsAzure.Storage.Auth ;
    using Microsoft.WindowsAzure.Storage.Queue ;

### Obtention de la chaîne de connexion de stockage

Pour pouvoir exploiter un objet blob, vous devez avant tout obtenir la chaîne de connexion du compte de stockage dans lequel résideront les objets blob. Pour représenter les informations de votre compte de stockage, vous pouvez utiliser le type **CloudStorageAccount**. Pour les projets de service cloud, vous pouvez utiliser le type **ConfigurationManager** afin d'extraire votre chaîne de connexion de stockage et les informations de votre compte de stockage à partir de la configuration du service Azure, comme illustré dans le code suivant :

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-blobs-include](../includes/vs-storage-getting-started-blobs-include.md)]

  [vs-storage-cloud-services-getting-started-intro]: ../includes/vs-storage-cloud-services-getting-started-intro.md
  [Objets blob]: /fr-fr/documentation/articles/vs-storage-cloud-services-getting-started-blobs "Objets blob"
  [Files d'attente]: /fr-fr/documentation/articles/vs-storage-cloud-services-getting-started-queues "Files d'attente"
  [Tables]: /fr-fr/documentation/articles/vs-storage-cloud-services-getting-started-tables "Tables"
  [Utilisation du stockage d'objets blob à partir de .NET]: http://azure.microsoft.com/fr-fr/documentation/articles/storage-dotnet-how-to-use-blobs/ "Utilisation du stockage d'objets blob à partir de .NET"
  [vs-storage-getting-started-blobs-include]: ../includes/vs-storage-getting-started-blobs-include.md
