<properties 
   pageTitle="Désactiver et supprimer un appareil StorSimple | Microsoft Azure"
   description="Explique comment supprimer un appareil StorSimple du service en le désactivant dans un premier temps, puis en le supprimant."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/14/2015"
   ms.author="v-sharos" />

# Désactiver et supprimer un appareil StorSimple

Ce didacticiel explique comment supprimer un appareil StorSimple d’un service en le désactivant, puis en le supprimant.

>[AZURE.NOTE]Vous devez désactiver un appareil pour pouvoir le supprimer.

## Désactiver un appareil

Vous pouvez souhaiter mettre un appareil hors service. Dans ce cas, l’appareil doit être désactivé. La désactivation interrompt la connexion entre l’appareil et le service StorSimple Manager correspondant.

>[AZURE.WARNING]La désactivation est une opération DÉFINITIVE et ne peut pas être annulée. Un appareil désactivé ne peut pas être enregistré auprès du service StorSimple Manager, sauf s’il est d’abord réinitialisé par la fabrique.

Si vous désactivez un appareil, les données stockées localement sur ce dernier ne seront plus accessibles. Seules les données associées à l’appareil qui a été stocké dans le cloud peuvent être récupérées. Une fois désactivé, un appareil doit être réinitialisé aux paramètres d’usine avant de pouvoir être réutilisé avec un service nouveau ou existant. La réinitialisation aux paramètres d’usine supprime toutes les données stockées localement sur votre appareil. Par conséquent, il est essentiel de prendre un instantané cloud de toutes vos données avant de désactiver un appareil. Cela vous permettra de récupérer toutes les données à un stade ultérieur.

Pour un appareil virtuel StorSimple, la désactivation supprime la machine virtuelle et les ressources créées lors de sa configuration. Une fois l’appareil virtuel désactivé, il ne peut pas être restauré vers son état précédent. Avant de désactiver un appareil virtuel StorSimple, veillez à arrêter ou supprimer les clients et les hôtes qui en dépendent.

### Désactiver et supprimer des données

Si vous êtes intéressé par la suppression complète de l’appareil et que vous ne souhaitez pas conserver ses données, procédez comme suit :

1. Avant de désactiver un appareil, vous devez supprimer tous les conteneurs de volumes (et les volumes) qui lui sont associés. Vous ne pouvez supprimer des conteneurs de volumes qu’après avoir supprimé les sauvegardes associées.

2. Désactivez l’appareil. Pour connaître les instructions à suivre, consultez [Étapes de la désactivation](#steps-to-deactivate).

3. À l’issue de la désactivation, vous pouvez supprimer complètement l’appareil. Pour connaître les instructions à suivre, consultez [Supprimer un appareil](#delete-a-device).

### Désactiver et conserver des données

Si vous êtes intéressé par la suppression de l’appareil, mais que vous souhaitez conserver ses données, procédez comme suit :

1. Désactivez l’appareil. Tous les conteneurs de volumes et les instantanés de l’appareil sont conservés. Pour connaître les instructions à suivre, consultez [Étapes de la désactivation](#steps-to-deactivate).

2. Vous pouvez maintenant basculer les conteneurs de volumes et les instantanés associés. Pour connaître les procédures, consultez [Basculement et récupération d'urgence pour votre appareil StorSimple](storsimple-device-failover-disaster-recovery.md).

3. Après la désactivation et le basculement, vous pouvez supprimer complètement l’appareil. Pour connaître les instructions à suivre, consultez [Supprimer un appareil](#delete-a-device).

### Étapes de la désactivation

Utilisez la procédure suivante pour désactiver un appareil en vue de sa suppression.

#### Pour désactiver un appareil

1. Dans la page **Appareil** du service StorSimple Manager, sélectionnez l’appareil que vous souhaitez désactiver, puis, en bas de la page, cliquez sur **Désactiver**.

2. Un message de confirmation s’affiche. Cliquez sur **Oui** pour continuer. Le processus de désactivation démarre et son exécution dure quelques minutes.

    Sur un appareil virtuel StorSimple, la désactivation entraîne les actions suivantes :

      - L’appareil virtuel StorSimple est supprimé.

      - Les disques de données et de système d’exploitation créés pour l’appareil virtuel StorSimple sont supprimés.

      - Le service hébergé et le réseau virtuel créés lors de l’approvisionnement sont conservés. Si vous n’utilisez pas ces entités, vous devez les supprimer manuellement.

      - Les instantanés cloud créés par l’appareil virtuel StorSimple sont conservés.

<!--After the device is deactivated, you will need to perform a failover before you can delete it completely. For failover instructions, go to [Failover and disaster recovery for your StorSimple device](storsimple-device-failover-disaster-recovery.md).-->
## Supprimer un appareil

Vous ne pouvez supprimer que les appareils qui ont été désactivés. Si vous supprimez un appareil, il est retiré de la liste des appareils connectés au service. Le service ne peut alors plus gérer l’appareil supprimé.

#### Pour supprimer un appareil

1. Dans la page **Appareils** du service StorSimple Manager, sélectionnez l’appareil désactivé à supprimer.

2. En bas de la page, cliquez sur **Supprimer**.

3. Vous êtes invité à confirmer l’opération. Cliquez sur **Oui** pour continuer.

La suppression de l’appareil peut nécessiter quelques minutes.

## Étapes suivantes
- Pour restaurer les paramètres d’usine de l’appareil désactivé, consultez [Rétablir les paramètres d’usine de l’appareil](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

- Pour obtenir une assistance technique, [contactez le support technique de Microsoft](storsimple-contact-microsoft-support.md).

- Pour en savoir plus sur l’utilisation du service StorSimple Manager, consultez [Utiliser le service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=Oct15_HO3-->