---
title: "Récupération des informations d’état d’un travail du service Azure Import/Export | Microsoft Docs"
description: "Découvrez comment obtenir les informations d’état des travaux du service Microsoft Azure Import/Export"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 22d7e5f0-94da-49b4-a1ac-dd4c14a423c2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 22e37e26fec913a7638c71b2547c38f5efacd10b
ms.openlocfilehash: c1626024262959d33e18dbceba74b89997972c98


---

# <a name="retrieving-state-information-for-a-job"></a>Récupération des informations d’état d’un travail
Vous pouvez appeler l’opération [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) pour récupérer des informations sur les travaux d’importation et d’exportation. Les informations renvoyées incluent :

-   L’état actuel du travail.

-   Le pourcentage approximatif de progression de chaque travail.

-   L’état actuel de chaque disque.

-   Les URI des blobs contenant les journaux d’erreurs et des informations de journalisation détaillées (si activé).

Les sections suivantes décrivent les informations renvoyées par l’opération `Get Job`.

## <a name="job-states"></a>États de travail
Le tableau et le diagramme d’état ci-dessous décrivent les états par lesquels un travail passe pendant son cycle de vie. L’état actuel du travail peut être déterminé en appelant l’opération `Get Job`.

![JobStates](./media/storage-import-export-retrieving-state-info-for-a-job/JobStates.png "JobStates")

Le tableau suivant décrit chacun des états par lesquels un travail peut passer.

|État du travail|Description|
|---------------|-----------------|
|`Creating`|Après avoir appelé l’opération Put Job, un travail est créé et son état est défini sur `Creating`. Pendant que le travail se trouve dans l’état `Creating`, le service Import/Export suppose que les disques n’ont pas été expédiés au centre de données. Un travail peut rester dans l’état `Creating` pendant deux semaines maximum. Passé ce délai, il est automatiquement supprimé par le service.<br /><br /> Si vous appelez l’opération Update Job Properties pendant que le travail est dans l’état `Creating`, le travail reste dans l’état `Creating` et le délai d’expiration est réinitialisé à deux semaines.|
|`Shipping`|Après avoir expédié votre colis, vous devez appeler l’opération Update Job Properties pour mettre à jour l’état du travail en `Shipping`. L’état d’expédition peut être défini uniquement si les propriétés `DeliveryPackage` (transporteur et numéro de suivi) et `ReturnAddress` ont été définies pour le travail.<br /><br /> Le travail restera dans l’état Expédition pendant deux semaines maximum. Si deux semaines se sont écoulées et si les disques n’ont pas été reçus, les opérateurs du service Import/Export sont notifiés.|
|`Received`|Lorsque tous les disques ont été reçus dans le centre de données, l’état du travail est défini sur Reçu.|
|`Transferring`|Lorsque les disques ont été reçus dans le centre de données, et qu’au moins un disque a commencé à être traité, l’état du travail est défini sur `Transferring`. Pour plus d’informations, consultez la section `Drive States` ci-dessous.|
|`Packaging`|Une fois le traitement terminé pour tous les disques, le travail est défini sur l’état `Packaging` jusqu’à ce que les disques soient renvoyés au client.|
|`Completed`|Une fois que tous les disques ont été renvoyés au client, si le travail s’est terminé sans erreur, son état est défini sur `Completed`. Le travail est automatiquement supprimé au bout de 90 jours dans l’état `Completed`.|
|`Closed`|Une fois que tous les disques ont été renvoyés au client, si des erreurs sont survenues pendant le traitement du travail, son état est défini sur `Closed`. Le travail est automatiquement supprimé au bout de 90 jours dans l’état `Closed`.|

Vous pouvez annuler un travail uniquement lorsqu’il est défini sur certains états. Un travail annulé ignore l’étape de copie des données, mais il passe quand même par toutes les étapes d’un travail qui n’a pas été annulé.

Le tableau suivant décrit les erreurs qui peuvent se produire pour chaque état, ainsi que leur effet sur le travail lorsqu’elles se produisent.

|État du travail|Événement|Résolution/Étapes suivantes|
|---------------|-----------|------------------------------|
|`Creating or Undefined`|Un ou plusieurs disques pour un travail sont arrivés, mais le travail n’est pas dans l’état `Shipping` ou il n’existe aucun enregistrement du travail dans le service.|L’équipe des opérations du service Import/Export va tenter de contacter le client pour créer ou mettre à jour le travail avec les informations nécessaires pour faire avancer le travail.<br /><br /> Si l’équipe des opérations ne parvient pas à contacter le client dans les deux semaines, elle essaiera de renvoyer les disques au client.<br /><br /> Dans le cas où les disques ne peuvent pas être renvoyés et si le client ne peut pas être contacté, les disques sont détruits de manière sécurisée dans les 90 jours.<br /><br /> Notez qu’un travail ne peut pas être traité tant que son état n’est pas mis à jour sur `Shipping`.|
|`Shipping`|Depuis plus de deux semaines, le colis associé à un travail n’est toujours pas arrivé.|L’équipe des opérations informe le client que le colis est manquant. En fonction de la réponse du client, l’équipe des opérations étend le délai d’attente du colis ou annule le travail.<br /><br /> Dans le cas où le client ne peut pas être contacté ou ne répond pas dans les 30 jours, l’équipe des opérations initie une action pour faire passer le travail de l’état `Shipping` à l’état `Closed` directement.|
|`Completed/Closed`|Les disques renvoyés n’ont jamais atteint leur destination ou ont été endommagés pendant l’expédition (s’applique uniquement pour un travail d’exportation).|Si les disques renvoyés n’atteignent pas leur destination, le client doit tout d’abord appeler l’opération Get Job ou vérifier l’état du travail dans le portail pour s’assurer que les disques ont été expédiés. Si les disques ont été expédiés, le client doit contacter le fournisseur d’expédition pour essayer de localiser les disques.<br /><br /> Si les disques sont endommagés pendant l’expédition, le client peut demander un autre travail d’exportation ou télécharger les blobs manquants.|
|`Transferring/Packaging`|L’adresse de retour est incorrecte ou manquante.|L’équipe des opérations va joindre la personne à contacter pour le travail afin d’obtenir l’adresse correcte.<br /><br /> Dans le cas où le client ne peut pas être contacté, les disques sont détruits de manière sécurisée dans les 90 jours.|
|`Creating / Shipping/ Transferring`|Un disque qui n’apparaît pas dans la liste des disques à importer est inclus dans le colis d’expédition.|Les disques supplémentaires ne sont pas traités et sont renvoyés au client lorsque le travail est terminé.|

## <a name="drive-states"></a>États du disque
Le tableau et le schéma ci-dessous décrivent le cycle de vie d’un disque individuel tout au long d’un travail d’importation ou d’exportation. Vous pouvez récupérer l’état actuel du disque en appelant l’opération `Get Job` et en inspectant l’élément `State` de la propriété `DriveList`.

![DriveStates](./media/storage-import-export-retrieving-state-info-for-a-job/DriveStates.png "DriveStates")

Le tableau suivant décrit chacun des états par lesquels un disque peut passer.

|État du disque|Description|
|-----------------|-----------------|
|`Specified`|Pour un travail d’importation, lorsque le travail est créé avec l’opération Put Job, l’état initial d’un disque est l’état `Specified`. Pour un travail d’exportation, comme aucun disque n’est spécifié lors de la création du travail, l’état initial du disque est l’état `Received`.|
|`Received`|Le disque passe à l’état `Received` une fois que l’opérateur du service Import/Export a traité les disques reçus de l’entreprise de transport pour un travail d’importation. Pour un travail d’exportation, l’état initial du disque est l’état `Received`.|
|`NeverReceived`|Le disque passe à l’état `NeverReceived` lorsque le colis associé à un travail est arrivé, mais ne contient pas le disque. Un disque peut également passer à cet état si le colis n’est toujours pas arrivé au centre de données deux semaines après réception des informations d’expédition par le service.|
|`Transferring`|Un disque passe à l’état `Transferring` lorsque le service commence à transférer les données du disque au stockage Azure.|
|`Completed`|Un disque passe à l’état `Completed` lorsque le service a transféré toutes les données sans erreurs.|
|`CompletedMoreInfo`|Un disque passe à l’état `CompletedMoreInfo` lorsque le service a rencontré des erreurs pendant la copie des données à partir du disque ou sur celui-ci. Les informations peuvent inclure des erreurs, des avertissements ou des messages d’informations sur le remplacement des blobs.|
|`ShippedBack`|Le disque passe à l’état `ShippedBack` lorsqu’il a été renvoyé du centre de données à l’adresse de retour.|

Le tableau suivant décrit les états associés aux défaillances de disque et les mesures mises en œuvre pour chacun de ces états.

|État du disque|Événement|Résolution/Étape suivante|
|-----------------|-----------|-----------------------------|
|`NeverReceived`|Un disque qui a été marqué comme `NeverReceived`, car le colis associé au travail ne le contenait pas, arrive dans un autre colis.|L’équipe des opérations fait passer le disque à l’état `Received`.|
|`N/A`|Un disque qui n’est associé à aucun travail arrive au centre de données dans le cadre d’un autre travail.|Le disque est marqué en tant que disque supplémentaire et est retourné au client une fois le travail associé au colis d’origine terminé.|

## <a name="faulted-states"></a>États d’échec
Lorsqu’un travail ou un disque rencontre un échec au cours de son cycle de vie, le travail ou le disque est placé dans un état `Faulted`. À ce stade, l’équipe des opérations contacte le client par e-mail ou par téléphone. Une fois que le problème est résolu, l’état `Faulted` du travail ou du disque ayant rencontré un échec est remplacé par l’état approprié.

## <a name="see-also"></a>Voir aussi
[Using the Import/Export service REST API (Utilisation de l’API REST du service Import/Export)](storage-import-export-using-the-rest-api.md)



<!--HONumber=Dec16_HO3-->


