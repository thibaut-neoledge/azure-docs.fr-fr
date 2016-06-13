<properties
	pageTitle="Créer un instantané en lecture seule d’un objet blob | Microsoft Azure"
	description="Apprenez à créer un instantané d’un objet blob pour sauvegarder ses données à un moment donné. Découvrez comment les instantanés sont facturés et comment les utiliser pour réduire les frais de capacité."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/27/2016"
	ms.author="tamram"/>

# Création d’un instantané d’objet blob

## Vue d'ensemble

Un instantané est une version en lecture seule d'un objet blob capturé à un instant donné. Les captures instantanées sont utiles pour la sauvegarde des objets blob. Une fois créé, un instantané peut être lu, copié ou supprimé, mais pas modifié.

Un instantané d’objet blob porte le même nom que l’objet blob de base à partir duquel il a été créé, avec une valeur **DateTime** ajoutée qui indique l’heure à laquelle il a été créé. Par exemple, si l’URI de l’objet blob de pages est `http://storagesample.core.blob.windows.net/mydrives/myvhd`, l’URI de l’instantané ressemblera à `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`. Tous les instantanés d’un objet blob partagent son URI et se distinguent uniquement par cette valeur **DateTime ** ajoutée.

Un objet blob peut avoir plusieurs instantanés. Les instantanés sont conservés jusqu’à ce qu’ils soient explicitement supprimés. Un instantané ne peut toutefois pas être conservé plus longtemps que son objet blob source. Vous pouvez énumérer les instantanés associés à votre objet blob pour effectuer le suivi de vos instantanés actuels.

Lorsque vous créez un instantané d’un objet blob, les propriétés système de l’objet blob sont copiées dans l’instantané avec les mêmes valeurs.

Aucun bail associé à l’objet blob de base n’a d’incidence sur l’instantané. Vous ne pouvez pas acquérir de bail pour un instantané.

## Copie d'instantanés

Les opérations de copie impliquant des objets blob et des instantanés respectent les règles suivantes :

- Vous pouvez copier un instantané sur son objet blob de base. En plaçant un instantané à la place d'un objet blob de base, vous pouvez restaurer une version antérieure de l’objet blob. L’instantané est conservé, mais l’objet blob de base est remplacé par une copie accessible en écriture de l’instantané.

- Vous pouvez copier un instantané sur un objet blob de destination avec un nom différent. L’objet blob de destination qui en découle est modifiable et n’est pas un instantané.

- Quand un objet blob source est copié, ses instantanés ne sont pas copiés dans la destination. Quand un objet blob de destination est remplacé par une copie, tous les instantanés associés à l’objet blob de destination au moment de son remplacement restent intacts sous son nom.

- Quand vous créez un instantané d'un objet blob de blocs, la liste des blocs validés de l'objet blob est également copiée dans l'instantané. Les blocs non validés ne sont pas copiés.

## Spécification d'une condition d'accès

Vous pouvez spécifier une condition d’accès pour que l’instantané ne soit créé que si la condition est remplie. Pour spécifier une condition d'accès, utilisez la propriété **AccessCondition**. Si la condition spécifiée n’est pas remplie, l’instantané n’est pas créé et le service BLOB retourne le code d’état HTTPStatusCode.PreconditionFailed.

## Suppression d'instantanés

Un objet blob auquel sont associés des instantanés ne peut pas être supprimé, à moins de supprimer aussi les instantanés. Vous pouvez supprimer un instantané individuellement ou faire en sorte que le service de stockage supprime tous les instantanés en même temps que l'objet blob source. Si vous essayez de supprimer un objet blob auquel des instantanés sont encore associés, vous obtiendrez une erreur.

## Instantanés avec Azure Premium Storage
Si vous utilisez des instantanés avec le stockage Premium, vous devez respecter ces règles :

- Le nombre d'instantanés par objet blob de pages dans un compte de stockage premium est limité à 100. Si vous dépassez cette limite, l'opération de capture instantanée d'objet blob retourne le code d'erreur 409 (**SnapshotCountExceeded**).

- Un instantané d'un objet blob de pages dans un compte de stockage premium peut être capturé toutes les dix minutes. Si vous dépassez ce taux, l'opération de capture instantanée d'objet blob retourne le code d'erreur 409 (**SnaphotOperationRateExceeded**).

- La lecture d'un instantané d'un objet blob de pages dans un compte de stockage premium via Get Blob n'est pas prise en charge. L'appel de Get Blob sur un instantané dans un compte de stockage premium retourne le code d'erreur 400 (**InvalidOperation**). Toutefois, l’appel de Get Blob Properties et Get Blob Metadata sur un instantané est pris en charge.

- Pour lire un instantané, vous pouvez utiliser l’opération Copy Blob pour copier un instantané vers un autre objet blob de pages dans le compte. L’objet blob de destination pour l'opération de copie ne doit pas avoir d'instantanés. Si l'objet blob de destination a des instantanés, l'opération Copy Blob retourne le code d'erreur 409 (**SnapshotsPresent**).

## Renvoi de l'URI absolu d'un instantané

Cet exemple de code C# crée un nouvel instantané et écrit l’URI absolu de l’emplacement principal.

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



## Présentation des frais liés aux instantanés

La création d’un instantané, c’est-à-dire d’une copie en lecture seule d’un objet blob, peut entraîner des frais de stockage de données supplémentaires pour votre compte. Lors de la conception de votre application, il est important de savoir comment ces frais peuvent s'accumuler pour pouvoir réduire les coûts superflus.

### Considérations importantes relatives à la facturation

La liste suivante contient des points clés à prendre en compte lors de la création d’un instantané.

- Des frais sont applicables pour des pages ou des blocs uniques, qu’ils soient dans l’objet blob ou dans l’instantané. Aucuns frais supplémentaires ne sont imputés à votre compte pour les instantanés associés à un objet blob tant que vous n'avez pas mis à jour l’objet blob sur lequel ils sont basés. Une fois que vous avez mis à jour l'objet blob de base, il diffère de ses instantanés et vous serez facturé pour les blocs ou pages uniques dans chaque objet blob ou instantané.

- Quand vous remplacez un bloc au sein d’un objet blob de bloc, ce bloc est ensuite facturé comme un bloc unique. Cela est vrai même si le bloc a le même ID de bloc et les mêmes données que dans l'instantané. Une fois le bloc revalidé, il diffère de son homologue dans tous les instantanés et vous serez facturé pour ses données. Il en va de même pour une page dans un objet blob de pages qui est mise à jour avec des données identiques.

- Le remplacement d'un objet blob en appelant la méthode **UploadFile**, **UploadText**, **UploadStream** ou **UploadByteArray** remplace tous les blocs de cet objet blob. Si vous avez un instantané associé à cet objet blob, tous les blocs dans l'objet blob de base et l'instantané seront désormais différents et vous serez facturé pour tous les blocs des deux objets blob. Cela est vrai même si les données de l’objet blob de base et l’instantané restent identiques.

- Le service blob Azure ne dispose d'aucun moyen pour déterminer si deux blocs contiennent des données identiques. Chaque bloc qui est téléchargé et validé est traité comme étant unique, même s’il a les mêmes données et le même ID de bloc. Comme des frais sont applicables aux blocs uniques, il est important de savoir que la mise à jour d'un objet blob qui a un instantané entraîne la création de blocs uniques supplémentaires et donc des frais supplémentaires.

> [AZURE.NOTE] Un conseil : gérez les instantanés avec attention afin d’éviter des frais supplémentaires. Nous vous recommandons de gérer les instantanés de la manière suivante :

> - Supprimez et recréez les instantanés associés à un objet blob chaque fois que vous mettez à jour l'objet blob, même si vous le mettez à jour avec des données identiques, sauf si la conception de votre application exige de tenir à jour des instantanés. En supprimant et en recréant les instantanés de l’objet blob, vous pouvez vous assurer que l’objet blob et les instantanés ne diffèrent pas.

> - Si vous tenez à jour des instantanés d'un objet blob, évitez d'appeler **UploadFile**, **UploadText**, **UploadStream** ou **UploadByteArray** pour mettre à jour l'objet blob, car ces méthodes remplacent tous les blocs dans l'objet blob. Mettez plutôt à jour le moins de blocs possible en utilisant les méthodes **PutBlock** et **PutBlockList**.


### Scénarios de facturation d’instantanés


Les scénarios suivants illustrent l’accumulation des coûts pour un objet blob de blocs et ses instantanés.

Dans le scénario 1, l'objet blob de base n'a pas été mis à jour depuis la capture de l'instantané. Des frais sont donc applicables uniquement pour les blocs uniques 1, 2 et 3.

![Ressources Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

Dans le scénario 2, l'objet blob de base a été mis à jour, mais pas l'instantané. Le bloc 3 a été mis à jour, et bien qu’il contienne les mêmes données et le même ID, il est différent du bloc 3 de l’instantané. Par conséquent, des frais pour quatre blocs sont facturés au compte.

![Ressources Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

Dans le scénario 3, l'objet blob de base a été mis à jour, mais pas l'instantané. Le bloc 3 a été remplacé par le bloc 4 dans l’objet blob de base, mais l’instantané reflète toujours le bloc 3. Par conséquent, des frais pour quatre blocs sont facturés au compte.

![Ressources Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

Dans le scénario 4, l'objet blob de base a été complètement mis à jour et ne contient aucun de ses blocs d'origine. Par conséquent, des frais pour les huit blocs uniques sont facturés au compte. Ce scénario peut se produire si vous utilisez une méthode de mise à jour **UploadFile**, **UploadText**, **UploadFromStream** ou **UploadByteArray**, car ces méthodes remplacent tout le contenu d'un objet blob.

![Ressources Azure Storage](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

<!---HONumber=AcomDC_0601_2016-->