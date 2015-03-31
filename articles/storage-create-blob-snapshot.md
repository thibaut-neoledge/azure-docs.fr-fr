<properties 
	pageTitle="Création d'un instantané d'objet blob" 
	description="Guide pour la création d'instantanés d'objets blob Azure Storage" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

#Création d'un instantané d'objet blob

Vous pouvez créer un instantané d'un objet blob. Un instantané est une version en lecture seule d'un objet blob capturé à un instant donné. Un instantané peut être lu, copié ou supprimé, mais pas modifié. Les instantanés sont une façon de sauvegarder un objet blob à un instant T.

Un instantané d'objet blob porte le même non que l'objet blob de base à partir duquel il a été créé, avec une valeur DateTime annexée qui indique l'heure à laquelle il a été créé.  Par exemple, si l'URI de l'objet blob de pages est http://storagesample.core.blob.windows.net/mydrives/myvhd, l'URI de l'instantané ressemblera à http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z. Cette valeur peut être utilisée pour faire référence à l'instantané lors d'opérations ultérieures. Les instantanés d'un objet blob partagent son URI et se distinguent uniquement par cette valeur DateTime. Dans le code de bibliothèque cliente, la propriété Snapshot de l'objet blob retourne une valeur DateTime qui identifie de façon unique l'instantané par rapport à son objet blob de base. Vous pouvez utiliser cette valeur pour exécuter d'autres opérations sur l'instantané.

Un objet blob peut avoir plusieurs instantanés. Ceux-ci sont persistants jusqu'à ce qu'ils soient explicitement supprimés. Un instantané ne peut pas survivre à son objet blob source. Vous pouvez énumérer les instantanés associés à votre objet blob pour effectuer le suivi de vos instantanés actuels.

##Héritage de propriétés

Lorsque vous créez un instantané d'un objet blob, les propriétés système sont copiées dans l'instantané avec les mêmes valeurs. La liste suivante indique les propriétés système copiées pour la bibliothèque cliente de stockage .NET :

- ContentType 

- ContentEncoding 

- ContentLanguage

- Length

- CacheControl

- ContentMd5 


Un bail associé à l'objet blob de base n'est pas copié dans l'instantané. Les instantanés ne peuvent pas être loués.

##Copie d'instantanés 

Les opérations de copie impliquant des objets blob et des instantanés respectent les règles suivantes :

- Vous pouvez copier un instantané sur son objet blob de base. En plaçant un instantané à la place d'un objet blob de base, vous pouvez restaurer une version antérieure de l'objet blob. L'instantané reste, mais sa source est remplacée par une copie qui peut être lue et modifiée.

- Vous pouvez copier un instantané sur un objet blob de destination avec un nom différent. L'objet blob de destination qui en découle est modifiable et n'est pas un instantané.

- Quand un objet blob source est copié, ses instantanés ne sont pas copiés dans la destination. Quand un objet blob de destination est remplacé par une copie, tous les instantanés associés à l'objet blob de destination restent intacts sous son nom. 

- Quand vous créez un instantané d'un objet blob de blocs, la liste des blocs validés de l'objet blob est également copiée dans l'instantané. Les blocs non validés ne sont pas copiés.

##Spécification d'une condition d'accès 

Vous pouvez spécifier une condition d'accès pour que l'instantané ne soit créé que si la condition est remplie. Pour spécifier une condition d'accès, utilisez la propriété AccessCondition. Si la condition spécifiée n'est pas remplie, l'instantané n'est pas créé et le service BLOB retourne le code d'état HTTPStatusCode.PreconditionFailed.

##Suppression d'instantanés 

Un objet blob auquel sont associés des instantanés ne peut pas être supprimé, à moins de supprimer aussi les instantanés. Vous pouvez supprimer un instantané individuellement ou faire en sorte que le service de stockage supprime tous les instantanés en même temps que l'objet blob source. Si vous essayez de supprimer un objet blob auquel des instantanés sont encore associés, votre appel retourne une erreur.

##Instantanés avec stockage Premium
Si vous utilisez des instantanés avec le stockage Premium, vous devez respecter ces règles :

- Le nombre d'instantanés par objet blob de pages dans un compte de stockage Premium est limité à 100. Si vous dépassez cette limite, l'opération de capture instantanée d'objet blob retourne le code d'erreur 409 (SnapshotCountExceeded).

- Un instantané d'un objet blob de pages dans un compte de stockage Premium peut être capturé toutes les dix minutes. Si vous dépassez ce taux, l'opération de capture instantanée d'objet blob retourne le code d'erreur 409 (SnaphotOperationRateExceeded).

- La lecture d'un instantané d'un objet blob de pages dans un compte de stockage Premium via Get Blob n'est pas prise en charge. L'appel de Get Blob sur un instantané dans un compte de stockage Premium retourne le code d'erreur 400 (Opération non valide). Toutefois, l'appel de Get Blob Properties et Get Blob Metadata sur un instantané est pris en charge.

- Pour lire un instantané, vous pouvez utiliser l'opération Copy Blob pour copier un instantané vers un autre objet blob de pages dans le compte. L'objet blob de destination pour l'opération de copie ne doit pas avoir d'instantanés. Si l'objet blob de destination a des instantanés, Copy Blob retourne le code d'erreur 409 (SnapshotsPresent).

##Construction de l'URI absolu d'un instantané 

Cet exemple de code construit l'URI absolu d'un instantané à partir de son objet blob de base.

C#

	var snapshot = blob.CreateSnapshot();
	var uri = Microsoft.WindowsAzure.StorageClient.Protocol.BlobRequest.Get
    (snapshot.Uri, 
    0, 
    snapshot.SnapshotTime.Value, 
    null).Address.AbsoluteUri;

<!--HONumber=47-->
