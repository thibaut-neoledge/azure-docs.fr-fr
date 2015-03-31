<properties 
	pageTitle="Présentation des frais liés aux instantanés" 
	description="Guide expliquant la facturation des instantanés d'objets blob Azure Storage" 
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

#Présentation des frais liés aux instantanés

La création d'un instantané, c'est-à-dire d'une copie en lecture seule d'un objet blob, peut entraîner des frais de stockage de données supplémentaires pour votre compte. Lors de la conception de votre application, il est important de savoir comment ces frais peuvent s'accumuler pour pouvoir réduire les coûts superflus.

##Considérations importantes relatives à la facturation

La liste suivante contient des points clés à prendre en compte lors de la création d'un instantané.

- Des frais sont applicables pour des pages ou des blocs uniques, qu'ils soient dans l'objet blob ou dans l'instantané. Aucuns frais supplémentaires ne sont imputés à votre compte pour les instantanés associés à un objet blob tant que vous n'avez pas mis à jour l'objet blob sur lequel ils sont basés. Une fois que vous avez mis à jour l'objet blob de base, il diffère de ses instantanés et vous serez facturé pour les blocs ou pages uniques dans chaque objet blob ou instantané.

- Quand vous remplacez un bloc au sein d'un objet blob de bloc, ce bloc est ensuite facturé comme un bloc unique. Cela est vrai même si le bloc a le même ID de bloc et les mêmes données que dans l'instantané. Une fois le bloc revalidé, il diffère de son homologue dans tous les instantanés et vous serez facturé pour ses données. Il en va de même pour une page dans un objet blob de pages qui est mise à jour avec des données identiques.

- Le remplacement d'un objet blob en appelant la méthode UploadFile, UploadText, UploadStream ou UploadByteArray remplace tous les blocs de cet objet blob. Si vous avez un instantané associé à cet objet blob, tous les blocs dans l'objet blob de base et l'instantané seront désormais différents et vous serez facturé pour tous les blocs des deux objets blob. Cela est vrai même si les données de l'objet blob de base et l'instantané restent identiques.

- Le service blob Azure ne dispose d'aucun moyen pour déterminer si deux blocs contiennent des données identiques. Chaque bloc qui est téléchargé et validé est traité comme étant unique, même s'il a les mêmes données et le même ID de bloc. Comme des frais sont applicables aux blocs uniques, il est important de savoir que la mise à jour d'un objet blob qui a un instantané entraîne la création de blocs uniques supplémentaires et donc des frais supplémentaires.

> [AZURE.NOTE] Un conseil : gérez les instantanés avec attention afin d'éviter des frais supplémentaires. Il est recommandé de gérer les instantanés de la manière suivante :

> - Supprimez et recréez les instantanés associés à un objet blob chaque fois que vous mettez à jour l'objet blob, même si vous le mettez à jour avec des données identiques, sauf si la conception de votre application exige de tenir à jour des instantanés. En supprimant et en recréant les instantanés de l'objet blob, vous pouvez vous assurer que l'objet blob et les instantanés ne diffèrent pas.

> - Si vous tenez à jour des instantanés d'un objet blob, évitez d'appeler UploadFile, UploadText, UploadStream ou UploadByteArray pour mettre à jour l'objet blob, car ces méthodes remplacent tous les blocs dans l'objet blob. Mettez plutôt à jour le moins de blocs possible en utilisant les méthodes PutBlock et PutBlockList.


##Scénarios de facturation d'instantanés


Les scénarios suivants illustrent l'accumulation des coûts pour un objet blob de blocs et ses instantanés. 

Dans le scénario 1, l'objet blob de base n'a pas été mis à jour depuis la capture de l'instantané. Des frais sont donc applicables uniquement pour les blocs uniques 1, 2 et 3.

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-1.png)

Dans le scénario 2, l'objet blob de base a été mis à jour, mais pas l'instantané. Le bloc 3 a été mis à jour, et bien qu'il contienne les mêmes données et le même ID, il est différent du bloc 3 de l'instantané. Par conséquent, des frais pour quatre blocs sont facturés au compte.

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-2.png)

Dans le scénario 3, l'objet blob de base a été mis à jour, mais pas l'instantané. Le bloc 3 a été remplacé par le bloc 4 dans l'objet blob de base, mais l'instantané reflète toujours le bloc 3. Par conséquent, des frais pour quatre blocs sont facturés au compte.
 
![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-3.png)

Dans le scénario 4, l'objet blob de base a été complètement mis à jour et ne contient aucun de ses blocs d'origine. Par conséquent, des frais pour les huit blocs uniques sont facturés au compte. Ce scénario peut se produire si vous utilisez une méthode de mise à jour UploadFile, UploadText, UploadFromStream ou UploadByteArray, car ces méthodes remplacent tout le contenu d'un objet blob.

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-4.png)
<!--HONumber=47-->
