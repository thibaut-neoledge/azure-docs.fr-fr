<properties title="Prise en main d'Azure Storage" pageTitle="Prise en main d'Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
>
> -   [Mise en route][Mise en route]
> -   [Que s'est-il passé ?][Que s'est-il passé ?]

## Prise en main d'Azure Storage (Projets de service cloud)

> [AZURE.SELECTOR]
>
> -   [Objets blob][Objets blob]
> -   [Files d’attente][Files d’attente]
> -   [Tables][Mise en route]

Le service de stockage de tables Azure vous permet de stocker de grandes quantités de données structurées. Il s'agit d'une banque de données NoSQL qui accepte les appels authentifiés provenant de l'intérieur et de l'extérieur du cloud Azure. Les tables Azure sont idéales pour le stockage des données structurées non relationnelles. Pour plus d'informations, consultez la page [Utilisation du stockage de tables à partir de .NET][Utilisation du stockage de tables à partir de .NET].

Ajoutez les déclarations d'espace de noms suivantes en haut de chaque fichier C# pour lequel vous souhaitez accéder à Azure Storage par programmation :

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

##### Obtention de la chaîne de connexion de stockage

Pour pouvoir exploiter une table, vous devez avant tout obtenir la chaîne de connexion du compte de stockage dans lequel résideront les tables. Pour représenter les informations de votre compte de stockage, vous pouvez utiliser le type **CloudStorageAccount**. Pour les projets de service cloud, vous pouvez utiliser le type **ConfigurationManager** afin d'extraire votre chaîne de connexion de stockage et les informations de votre compte de stockage à partir de la configuration du service Azure, comme illustré dans le code suivant :

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]

  [Mise en route]: /documentation/articles/vs-storage-cloud-services-getting-started-tables/
  [Que s'est-il passé ?]: /documentation/articles/vs-storage-cloud-services-what-happened/
  [Objets blob]: /documentation/articles/vs-storage-cloud-services-getting-started-blobs/
  [Files d’attente]: /documentation/articles/vs-storage-cloud-services-getting-started-queues/
  [Utilisation du stockage de tables à partir de .NET]: http://azure.microsoft.com/fr-fr/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "Utilisation du stockage de tables à partir de .NET"
