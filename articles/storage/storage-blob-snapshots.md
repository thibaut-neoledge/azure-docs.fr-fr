---
title: "Création d’un instantané en lecture seule d’un objet blob | Microsoft Docs"
description: "Apprenez à créer un instantané d’un objet blob pour sauvegarder ses données à un moment donné. Découvrez comment les instantanés sont facturés et comment les utiliser pour réduire les frais de capacité."
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 3710705d-e127-4b01-8d0f-29853fb06d0d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: a8b570cfbab594e1a21417e081eaf6d34a4659d0
ms.openlocfilehash: 40f10b1cb860ca0c018cc3589417c54588269b0c
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-blob-snapshot"></a>Création d’un instantané d’objet blob
## <a name="overview"></a>Vue d'ensemble
Un instantané est une version en lecture seule d'un objet blob capturé à un instant donné. Les captures instantanées sont utiles pour la sauvegarde des objets blob. Une fois créé, un instantané peut être lu, copié ou supprimé, mais pas modifié.

Un instantané d’un objet blob est identique à l’objet blob de base, à la différence que l’URI de l’objet blob a une valeur **DateTime** à la fin qui indique l’heure à laquelle l’instantané a été pris. Par exemple, si l’URI de l’objet blob de pages est `http://storagesample.core.blob.windows.net/mydrives/myvhd`, l’URI de l’instantané est du type `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Tous les instantanés partagent l’URI de l’objet blob de base. La seule distinction entre l’objet blob de base et l’instantané est la valeur **DateTime** ajoutée à la fin.
>
>

Un objet blob peut avoir plusieurs instantanés. Les instantanés sont conservés jusqu’à ce qu’ils soient explicitement supprimés. Un instantané ne peut pas être conservé plus longtemps que son objet blob de base. Vous pouvez énumérer les instantanés associés à l’objet blob de base pour effectuer le suivi de vos instantanés actuels.

Lorsque vous créez un instantané d’un objet blob, les propriétés système de l’objet blob sont copiées dans l’instantané avec les mêmes valeurs. Les métadonnées de l’objet blob de base sont également copiées dans l’instantané, sauf si vous spécifiez des métadonnées distinctes pour l’instantané lorsque vous le créez.

Aucun bail associé à l’objet blob de base n’a d’incidence sur l’instantané. Vous ne pouvez pas acquérir de bail pour un instantané.

Un fichier de disque dur virtuel est utilisé pour stocker les informations et l’état actuels d’un disque de machine virtuelle. Vous pouvez détacher un disque de la machine virtuelle ou arrêter la machine virtuelle, puis prendre une capture instantanée de son fichier de disque dur virtuel. Vous pourrez par la suite utiliser ce fichier de capture instantanée pour récupérer le fichier de disque dur virtuel à ce moment précis et recréer la machine virtuelle.

Si le chiffrement du service de stockage (SSE) est activé pour le compte de stockage dans lequel réside l’objet blob, toutes les captures instantanées de cet objet blob seront chiffrées au repos.

## <a name="create-a-snapshot"></a>Créer un instantané
L’exemple de code suivant montre comment créer un instantané dans .NET. Cet exemple spécifie des métadonnées distinctes pour l’instantané lors de sa création.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
        // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="copy-snapshots"></a>Copie d'instantanés
Les opérations de copie impliquant des objets blob et des instantanés respectent les règles suivantes :

* Vous pouvez copier un instantané sur son objet blob de base. En plaçant un instantané à la place d'un objet blob de base, vous pouvez restaurer une version antérieure de l’objet blob. L’instantané est conservé, mais l’objet blob de base est remplacé par une copie accessible en écriture de l’instantané.
* Vous pouvez copier un instantané sur un objet blob de destination avec un nom différent. L’objet blob de destination qui en découle est modifiable et n’est pas un instantané.
* Quand un objet blob source est copié, ses instantanés ne sont pas copiés dans la destination. Quand un objet blob de destination est remplacé par une copie, tous les instantanés associés à l’objet blob de destination d’origine restent intacts.
* Quand vous créez un instantané d'un objet blob de blocs, la liste des blocs validés de l'objet blob est également copiée dans l'instantané. Les blocs non validés ne sont pas copiés.

## <a name="specify-an-access-condition"></a>Spécification d'une condition d'accès
Vous pouvez spécifier une condition d’accès pour que l’instantané ne soit créé que si la condition est remplie. Pour spécifier une condition d'accès, utilisez la propriété **AccessCondition** . Si la condition spécifiée n’est pas remplie, l’instantané n’est pas créé et le service BLOB retourne le code d’état HTTPStatusCode.PreconditionFailed.

## <a name="delete-snapshots"></a>Suppression d'instantanés
Un objet blob auquel sont associés des instantanés ne peut pas être supprimé, à moins de supprimer aussi les instantanés. Vous pouvez supprimer un instantané individuellement, ou spécifier que tous les instantanés doivent être supprimés lors de la suppression de l’objet blob source. Si vous essayez de supprimer un objet blob auquel des instantanés sont encore associés, une erreur se produit.

L’exemple de code suivant montre comment supprimer un objet blob et ses instantanés dans .NET, où `blockBlob` est une variable de type **CloudBlockBlob**:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="snapshots-with-azure-premium-storage"></a>Instantanés avec Azure Premium Storage
Si vous utilisez des instantanés avec le stockage Premium, vous devez respecter ces règles :

* Le nombre maximum d’instantanés par objet blob de pages dans un compte de stockage premium est 100. Si vous dépassez cette limite, l'opération de capture instantanée d'objet blob retourne le code d'erreur 409 (**SnapshotCountExceeded**).
* Vous pouvez prendre un instantané d’un objet blob de pages dans un compte de stockage premium toutes les 10 minutes. Si vous dépassez ce taux, l'opération de capture instantanée d'objet blob retourne le code d'erreur 409 (**SnaphotOperationRateExceeded**).
* Pour lire un instantané, vous pouvez utiliser l’opération Copy Blob pour copier un instantané vers un autre objet blob de pages dans le compte. L’objet blob de destination pour l'opération de copie ne doit pas avoir d'instantanés. Si l'objet blob de destination a des instantanés, l'opération Copy Blob retourne le code d'erreur 409 (**SnapshotsPresent**).

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Renvoi de l'URI absolu d'un instantané
Cet exemple de code C# crée un instantané et écrit l’URI absolu de l’emplacement principal.

```csharp
//Create the blob service client object.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();

//Get a reference to a blob.
CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
blob.UploadText("This is a blob.");

//Create a snapshot of the blob and write out its primary URI.
CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);
```

## <a name="understand-how-snapshots-accrue-charges"></a>Présentation des frais liés aux instantanés
La création d’un instantané, c’est-à-dire d’une copie en lecture seule d’un objet blob, peut entraîner des frais de stockage de données supplémentaires pour votre compte. Lors de la conception de votre application, il est important de savoir comment ces frais peuvent s'accumuler pour pouvoir réduire les coûts superflus.

### <a name="important-billing-considerations"></a>Considérations importantes relatives à la facturation
La liste suivante contient des points clés à prendre en compte lors de la création d’un instantané.

* Des frais s’appliquent à votre compte de stockage pour des pages ou des blocs uniques, qu’ils soient dans l’objet blob ou dans l’instantané. Aucuns frais supplémentaires ne sont imputés à votre compte pour les instantanés associés à un objet blob tant que vous n'avez pas mis à jour l’objet blob sur lequel ils sont basés. Une fois que vous avez mis à jour l’objet blob de base, il diffère de ses instantanés. Dans ce cas, vous êtes facturé pour les blocs ou pages uniques de chaque objet blob ou instantané.
* Quand vous remplacez un bloc au sein d’un objet blob de bloc, ce bloc est ensuite facturé comme un bloc unique. Cela est vrai même si le bloc a le même ID de bloc et les mêmes données que dans l'instantané. Une fois le bloc revalidé, il diffère de son homologue dans tous les instantanés et vous serez facturé pour ses données. Il en va de même pour une page dans un objet blob de pages qui est mise à jour avec des données identiques.
* Le remplacement d’un objet blob de blocs en appelant la méthode **UploadFile**, **UploadText**, **UploadStream** ou **UploadByteArray** remplace tous les blocs de l’objet blob. Si vous avez un instantané associé à cet objet blob, tous les blocs dans l’objet blob de base et l’instantané seront désormais différents et vous serez facturé pour tous les blocs des deux objets blob. Cela est vrai même si les données de l’objet blob de base et l’instantané restent identiques.
* Le service blob Azure ne dispose d'aucun moyen pour déterminer si deux blocs contiennent des données identiques. Chaque bloc qui est téléchargé et validé est traité comme étant unique, même s’il a les mêmes données et le même ID de bloc. Comme des frais sont applicables aux blocs uniques, il est important de savoir que la mise à jour d'un objet blob qui a un instantané entraîne la création de blocs uniques supplémentaires et donc des frais supplémentaires.

> [!NOTE]
> Un conseil : gérez les instantanés avec attention afin d’éviter des frais supplémentaires. Nous vous recommandons de gérer les instantanés de la manière suivante :
>
> * Supprimez et recréez les instantanés associés à un objet blob chaque fois que vous mettez à jour l'objet blob, même si vous le mettez à jour avec des données identiques, sauf si la conception de votre application exige de tenir à jour des instantanés. En supprimant et en recréant les instantanés de l’objet blob, vous pouvez vous assurer que l’objet blob et les instantanés ne diffèrent pas.
> * Si vous tenez à jour des instantanés d’un objet blob, évitez d’appeler **UploadFile**, **UploadText**, **UploadStream** ou **UploadByteArray** pour mettre à jour l’objet blob. Ces méthodes remplacent tous les blocs dans l’objet blob. Ainsi, votre objet blob de base et vos instantanés diffèrent de façon significative. Mettez plutôt à jour le moins de blocs possible en utilisant les méthodes **PutBlock** et **PutBlockList**.
>
>

### <a name="snapshot-billing-scenarios"></a>Scénarios de facturation d’instantanés
Les scénarios suivants illustrent l’accumulation des coûts pour un objet blob de blocs et ses instantanés.

Dans le scénario 1, l'objet blob de base n'a pas été mis à jour depuis la capture de l'instantané. Des frais sont donc applicables uniquement pour les blocs uniques 1, 2 et 3.

![Ressources Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

Dans le scénario 2, l'objet blob de base a été mis à jour, mais pas l'instantané. Le bloc 3 a été mis à jour, et bien qu’il contienne les mêmes données et le même ID, il est différent du bloc 3 de l’instantané. Par conséquent, des frais pour quatre blocs sont facturés au compte.

![Ressources Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

Dans le scénario 3, l'objet blob de base a été mis à jour, mais pas l'instantané. Le bloc 3 a été remplacé par le bloc 4 dans l’objet blob de base, mais l’instantané reflète toujours le bloc 3. Par conséquent, des frais pour quatre blocs sont facturés au compte.

![Ressources Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

Dans le scénario 4, l'objet blob de base a été complètement mis à jour et ne contient aucun de ses blocs d'origine. Par conséquent, des frais pour les huit blocs uniques sont facturés au compte. Ce scénario peut se produire si vous utilisez une méthode de mise à jour **UploadFile**, **UploadText**, **UploadFromStream** ou **UploadByteArray**, car ces méthodes remplacent tout le contenu d’un objet blob.

![Ressources Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des exemples supplémentaires d’utilisation de Blob Storage, consultez [Exemples de code Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Vous pouvez télécharger un exemple d’application et l’exécuter ou parcourir le code sur GitHub.


