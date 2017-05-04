---
title: "Gérer l’expiration des objets blob d’Azure Storage dans Azure CDN | Microsoft Docs"
description: "Découvrez les options de contrôle de la durée de vie des objets blob dans la mise en cache Azure CDN."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: f44119d651767881cfd999808925b0066a5e502b
ms.lasthandoff: 04/27/2017


---
# <a name="manage-expiration-of-azure-storage-blobs-in-azure-cdn"></a>Gérer l’expiration des objets blob d’Azure Storage dans Azure CDN
> [!div class="op_single_selector"]
> * [Azure Web Apps/Cloud Services, ASP.NET ou IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Service Azure Storage Blob](cdn-manage-expiration-of-blob-content.md)
> 
> 

Le [service BLOB](../storage/storage-introduction.md#blob-storage) dans [Stockage Azure](../storage/storage-introduction.md) fait partie des différentes origines Azure intégrées à Azure CDN.  Tout contenu d’objet BLOB publiquement accessible peut être mis en cache dans Azure CDN jusqu’à l’expiration de sa durée de vie.  La durée de vie est déterminée par l’ [*Cache-Control* ](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) dans la réponse HTTP d’Azure Storage.

> [!TIP]
> Vous pouvez choisir de ne pas définir la durée de vie d’un objet BLOB.  Dans ce cas, Azure CDN applique automatiquement une durée de vie de sept jours par défaut.
> 
> Pour découvrir comment Azure CDN accélère l’accès aux objets blob et à d’autres fichiers, consultez la [Vue d’ensemble d’Azure CDN](cdn-overview.md).
> 
> Pour plus d’informations sur le service BLOB Azure Storage, consultez la page [Concepts du service BLOB](https://msdn.microsoft.com/library/dd179376.aspx). 
> 
> 

Ce didacticiel présente plusieurs façons de définir la durée de vie d’un objet BLOB dans Azure Storage.  

## <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) est l’un des moyens les plus rapides et puissants de gérer vos services Azure.  Utilisez l’applet de commande `Get-AzureStorageBlob` pour obtenir une référence à l’objet BLOB, puis définissez la propriété `.ICloudBlob.Properties.CacheControl`. 

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "public, max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> Vous pouvez également utiliser PowerShell pour [gérer vos profils et points de terminaison CDN](cdn-manage-powershell.md).
> 
> 

## <a name="azure-storage-client-library-for-net"></a>Bibliothèque cliente Azure Storage pour .NET
Pour définir la durée de vie d’un objet blob à l’aide de .NET, utilisez la [bibliothèque cliente Stockage Azure pour .NET](../storage/storage-dotnet-how-to-use-blobs.md) pour définir la propriété [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx).

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob blob = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "public, max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> De nombreux autres exemples de code .NET sont disponibles dans les [exemples de stockage d’objets BLOB Azure pour .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 
> 

## <a name="other-methods"></a>Autres méthodes
* [Interface de ligne de commande Azure](../cli-install-nodejs.md)
  
    Lors du chargement de l’objet blob, définissez la propriété *cacheControl* à l’aide du switch `-p`.  Cet exemple définit la durée de vie sur une heure (3 600 secondes).
  
    ```text
    azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
    ```
* [API REST des services d’Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
  
    Définissez explicitement la propriété *x-ms-blob-cache-control* sur une demande [Put Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx), [Put Block List](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx) ou [Set Blob Properties](https://msdn.microsoft.com/library/azure/ee691966.aspx).
* Outils de gestion du stockage tiers
  
    Certains outils de gestion Azure Storage tiers vous permettent de définir la propriété *CacheControl* sur les objets BLOB. 

## <a name="testing-the-cache-control-header"></a>Test de l’en-tête *Cache-Control*
Vous pouvez facilement vérifier la durée de vie de vos objets BLOB.  À l’aide des [outils de développement](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)de votre navigateur, vérifiez que votre objet BLOB comprend l’en-tête de réponse *Cache-Control* .  Vous pouvez également utiliser un outil tel que **wget**, [Postman](https://www.getpostman.com/) ou [Fiddler](http://www.telerik.com/fiddler) pour examiner les en-têtes de réponse.

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur les en-têtes *Cache-Control*](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* [Comment gérer l’expiration des contenus de service cloud dans Azure CDN](cdn-manage-expiration-of-cloud-service-content.md)


