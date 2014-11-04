<properties title="Prise en main d'Azure Storage" pageTitle="Prise en main d'Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Mise en route][Mise en route]
> -   [Que s'est-il passé ?][Que s'est-il passé ?]

## Prise en main d'Azure Storage (Projets ASP.NET vNext)

> [AZURE.SELECTOR]
>
> -   [Objets blob][Objets blob]
> -   [Files d’attente][Mise en route]
> -   [Tables][Tables]

Le service de stockage de files d'attente Azure permet de stocker un grand nombre de messages accessibles partout dans le monde via des appels authentifiés avec HTTP ou HTTPS. Un simple message de file d’attente peut avoir une taille de 64 Ko et une file d’attente peut contenir des millions de messages, jusqu’à la limite de capacité totale d’un compte de stockage. Pour plus d'informations, consultez la page [Utilisation du service de stockage de files d'attente à partir de .NET][Utilisation du service de stockage de files d'attente à partir de .NET].

Pour accéder par programmation aux files d'attente des projets ASP.NET vNext, procédez comme suit :

1.  Ajoutez la déclaration d'espace de noms suivante en haut de chaque fichier C# pour lequel vous souhaitez accéder à Azure Storage par programmation :

        using Microsoft.Framework.ConfigurationModel;

2.  Utilisez le code ci-dessous pour obtenir le paramètre de configuration.

        var config = new Configuration();
        config.AddJsonFile("config.json");

##### Obtention de la chaîne de connexion de stockage

Pour pouvoir exploiter une file d'attente, vous devez avant tout obtenir la chaîne de connexion du compte de stockage dans lequel résideront les files d'attente. Pour représenter les informations de votre compte de stockage, vous pouvez utiliser le type **CloudStorageAccount**. Si vous travaillez dans un projet ASP.NET vNext, vous pouvez appeler la méthode get de l'objet Configuration pour obtenir votre chaîne de connexion de stockage et les informations de votre compte de stockage à partir de la configuration du service Azure, comme illustré dans le code suivant :

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-queues-include](../includes/vs-storage-getting-started-queues-include.md)]

Pour plus d'informations, consultez la page [ASP.NET vNext][ASP.NET vNext].

  [Mise en route]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-queues/
  [Que s'est-il passé ?]: /documentation/articles/vs-storage-aspnet-vnext-what-happened/
  [Objets blob]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs/
  [Tables]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-tables/
  [Utilisation du service de stockage de files d'attente à partir de .NET]: http://azure.microsoft.com/fr-fr/documentation/articles/storage-dotnet-how-to-use-queues/ "Utilisation du service de stockage de files d'attente à partir de .NET"
  [vs-storage-getting-started-queues-include]: ../includes/vs-storage-getting-started-queues-include.md
  [ASP.NET vNext]: http://www.asp.net/vnext
