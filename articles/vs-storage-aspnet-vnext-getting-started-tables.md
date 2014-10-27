<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-aspnet-vnext-getting-started-intro](../includes/vs-storage-aspnet-vnext-getting-started-intro.md)]

# Prise en main d'Azure Storage

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs" title="Objets blob" class="current">Objets blob</a><a href="/fr-fr/documentation/articles/vs-storage-aspnet-vnext-getting-started-queues" title="Files d'attente">Files d'attente</a><a href="/fr-fr/documentation/articles/vs-storage-aspnet-vnext-getting-started-tables" title="Tables">Tables</a></div>

Le service de stockage de tables Azure vous permet de stocker de grandes quantités de données structurées. Il s'agit d'une banque de données NoSQL qui accepte les appels authentifiés provenant de l'intérieur et de l'extérieur du cloud Azure. Les tables Azure sont idéales pour le stockage des données structurées non relationnelles. Pour plus d'informations, consultez la page [Utilisation du stockage de tables à partir de .NET][Utilisation du stockage de tables à partir de .NET].

Pour accéder par programmation aux tables des projets ASP.NET vNext, procédez comme suit :

1.  Accédez à l'assembly Microsoft.WindowsAzure.Storage.dll. Pour ce faire, vous pouvez utiliser la commande NuGet. Cliquez avec le bouton droit de la souris sur votre projet dans l'Explorateur de solutions, puis sélectionnez Gérer les packages NuGet. Effectuez une recherche en ligne sur « WindowsAzure.Storage », puis cliquez sur Installer pour lancer l'installation du package Azure Storage et de ses dépendances. Ajoutez une référence à cet assembly dans votre projet.
2.  Installez le package NuGet Microsoft.Framework.ConfigurationModel.Json.
3.  Ajoutez la déclaration d'espace de noms suivante en haut de chaque fichier C# pour lequel vous souhaitez accéder à Azure Storage par programmation :

        using Microsoft.Framework.ConfigurationModel;

4.  Utilisez le code ci-dessous pour obtenir le paramètre de configuration.

        var config = new Configuration();
        config.AddJsonFile("config.json");

### Obtention de la chaîne de connexion de stockage

Pour pouvoir exploiter une table, vous devez avant tout obtenir la chaîne de connexion du compte de stockage dans lequel résideront les tables. Pour représenter les informations de votre compte de stockage, vous pouvez utiliser le type **CloudStorageAccount**. Si vous travaillez dans un projet ASP.NET vNext, vous pouvez appeler la méthode get de l'objet Configuration pour obtenir votre chaîne de connexion de stockage et les informations de votre compte de stockage à partir de la configuration du service Azure, comme illustré dans le code suivant :

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]

  [vs-storage-aspnet-vnext-getting-started-intro]: ../includes/vs-storage-aspnet-vnext-getting-started-intro.md
  [Objets blob]: /fr-fr/documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs "Objets blob"
  [Files d'attente]: /fr-fr/documentation/articles/vs-storage-aspnet-vnext-getting-started-queues "Files d'attente"
  [Tables]: /fr-fr/documentation/articles/vs-storage-aspnet-vnext-getting-started-tables "Tables"
  [Utilisation du stockage de tables à partir de .NET]: http://azure.microsoft.com/fr-fr/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "Utilisation du stockage de tables à partir de .NET"
  [vs-storage-getting-started-tables-include]: ../includes/vs-storage-getting-started-tables-include.md
