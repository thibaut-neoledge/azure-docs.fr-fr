---
title: "Désactiver et supprimer un appareil StorSimple | Microsoft Docs"
description: "Explique comment supprimer un appareil StorSimple du service en le désactivant dans un premier temps, puis en le supprimant."
services: storsimple
documentationcenter: 
author: SharS
manager: carmonm
editor: 
ms.assetid: 155cda38-c5ae-45dc-b7e8-6444494afc9e
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2016
ms.author: anbacker
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 014d4faeb478690168b44f88e7a0bda2b406cd4b


---
# <a name="deactivate-and-delete-a-storsimple-device"></a>Désactiver et supprimer un appareil StorSimple
## <a name="overview"></a>Vue d’ensemble
Vous pouvez souhaiter mettre un appareil StorSimple hors service (par exemple, si vous voulez le remplacer ou le mettre à niveau, ou si vous ne voulez plus utiliser StorSimple). Dans ce cas, vous devez désactiver l’appareil avant de le supprimer. La désactivation interrompt la connexion entre l’appareil et le service StorSimple Manager correspondant. Ce didacticiel explique comment mettre un appareil StorSimple hors service en le désactivant, puis en le supprimant. 

Si vous désactivez un appareil, les données stockées localement sur ce dernier ne seront plus accessibles. Seules les données associées à l’appareil qui a été stocké dans le cloud peuvent être récupérées.  

> [!WARNING]
> La désactivation est une opération DÉFINITIVE et ne peut pas être annulée. Un appareil désactivé ne peut pas être enregistré auprès du service StorSimple Manager, sauf s’il est d’abord réinitialisé par la fabrique. 
> 
> La réinitialisation aux paramètres d’usine supprime toutes les données stockées localement sur votre appareil. Par conséquent, il est essentiel de prendre un instantané cloud de toutes vos données avant de désactiver un appareil. Cela vous permettra de récupérer toutes les données à un stade ultérieur.
> 
> 

Ce didacticiel explique comment :

* Désactiver un appareil et supprimer ses données
* Désactiver un appareil et conserver ses données

Il explique également comment fonctionnent la désactivation et la suppression sur un appareil virtuel StorSimple.

> [!NOTE]
> Avant de désactiver un appareil physique ou virtuel StorSimple, arrêtez ou supprimez les clients et les hôtes qui en dépendent.
> 
> 

## <a name="deactivate-and-delete-data"></a>Désactiver et supprimer des données
Si vous êtes intéressé par la suppression complète de l’appareil et que vous ne voulez pas conserver ses données, procédez comme suit :

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Pour désactiver l’appareil et supprimer ses données
1. Avant de désactiver un appareil, vous devez supprimer tous les conteneurs de volumes (et les volumes) qui lui sont associés. Vous ne pouvez supprimer les conteneurs de volumes qu’après avoir supprimé les sauvegardes associées.
2. Désactivez l’appareil de la manière suivante :
   
   1. Dans la page **Appareil** du service StorSimple Manager, sélectionnez l’appareil que vous souhaitez désactiver, puis, en bas de la page, cliquez sur **Désactiver**.
   2. Un message de confirmation s’affiche. Cliquez sur **Oui** pour continuer. Le processus de désactivation démarre et son exécution dure quelques minutes.
3. À l’issue de la désactivation, vous pouvez supprimer complètement l’appareil. Si vous supprimez un appareil, il est retiré de la liste des appareils connectés au service. Le service ne peut alors plus gérer l’appareil supprimé. Procédez comme suit pour supprimer l’appareil :
   
   1. Dans la page **Appareils** du service StorSimple Manager, sélectionnez l’appareil désactivé à supprimer.
   2. En bas de la page, cliquez sur **Supprimer**.
   3. Vous êtes invité à confirmer l’opération. Cliquez sur **Oui** pour continuer.
      
      La suppression de l’appareil peut nécessiter quelques minutes.

## <a name="deactivate-and-retain-data"></a>Désactiver et conserver des données
Si vous êtes intéressé par la suppression de l’appareil, mais voulez conserver ses données, procédez comme suit :

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Pour désactiver un appareil et conserver ses données
1. Désactivez l’appareil. Tous les conteneurs de volumes et les instantanés de l’appareil sont conservés.
   
   1. Dans la page **Appareils** du service StorSimple Manager, sélectionnez l’appareil que vous souhaitez désactiver, puis, en bas de la page, cliquez sur **Désactiver**.
   2. Un message de confirmation s’affiche. Cliquez sur **Oui** pour continuer. Le processus de désactivation démarre et son exécution dure quelques minutes.
2. Vous pouvez maintenant basculer les conteneurs de volumes et les instantanés associés. Pour connaître les procédures, consultez [Basculement et récupération d'urgence pour votre appareil StorSimple](storsimple-device-failover-disaster-recovery.md).
3. Après la désactivation et le basculement, vous pouvez supprimer complètement l’appareil. Si vous supprimez un appareil, il est retiré de la liste des appareils connectés au service. Le service ne peut alors plus gérer l’appareil supprimé. Procédez comme suit pour supprimer l’appareil :
   
   1. Dans la page **Appareils** du service StorSimple Manager, sélectionnez l’appareil désactivé à supprimer.
   2. En bas de la page, cliquez sur **Supprimer**.
   3. Vous êtes invité à confirmer l’opération. Cliquez sur **Oui** pour continuer.
      
      La suppression de l’appareil peut nécessiter quelques minutes.

## <a name="deactivate-and-delete-a-virtual-device"></a>Désactiver et supprimer un appareil virtuel
Dans le cas d’un appareil virtuel StorSimple, la désactivation a pour effet de libérer la machine virtuelle. Vous pouvez ensuite supprimer la machine virtuelle et les ressources créées lors de son approvisionnement. Une fois l’appareil virtuel désactivé, il ne peut pas être restauré vers son état précédent. 

La désactivation entraîne ce qui suit :

* L’appareil virtuel StorSimple est supprimé.
* Les disques de données et de système d’exploitation créés pour l’appareil virtuel StorSimple sont supprimés.
* Le service hébergé et le réseau virtuel créés lors de l’approvisionnement sont conservés. Si vous n’utilisez pas ces entités, vous devez les supprimer manuellement.
* Les instantanés cloud créés par l’appareil virtuel StorSimple sont conservés.

## <a name="next-steps"></a>Étapes suivantes
* Pour restaurer les paramètres d’usine de l’appareil désactivé, consultez [Rétablir les paramètres d’usine de l’appareil](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Pour obtenir une assistance technique, [contactez le support technique de Microsoft](storsimple-contact-microsoft-support.md).
* Pour en savoir plus sur l’utilisation du service StorSimple Manager, consultez [Utiliser le service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md). 




<!--HONumber=Nov16_HO3-->


