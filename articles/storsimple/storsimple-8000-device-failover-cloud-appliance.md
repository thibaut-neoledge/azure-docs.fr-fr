---
title: "Basculement, récupération d’urgence StorSimple vers une StorSimple Cloud Appliance| Microsoft Docs"
description: "Découvrez comment basculer votre appareil physique de la gamme StorSimple 8000 vers une appliance cloud."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/03/2017
ms.author: alkohli
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: ec8bebf2854e84a37e84b45564e80fc20b63d8d8
ms.contentlocale: fr-fr
ms.lasthandoff: 07/08/2017

---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Basculer vers votre StorSimple Cloud Appliance

## <a name="overview"></a>Vue d'ensemble

Ce didacticiel décrit les étapes requises pour basculer un appareil physique de la gamme StorSimple 8000 vers une StorSimple Cloud Appliance en cas de sinistre. StorSimple utilise l’option de basculement d’appareil pour migrer des données d’un appareil physique source dans le centre de données vers une appliance cloud exécutée dans Azure. Les instructions de ce didacticiel s’appliquent aux appareils physiques de gamme StorSimple 8000 et aux appliances cloud exécutant des versions logicielles Update 3 et versions ultérieures.

Pour en savoir plus sur le basculement d’appareil et comment il est utilisé après un sinistre, accédez à [Basculement et récupération d’urgence pour les appareils StorSimple de la gamme 8000](storsimple-8000-device-failover-disaster-recovery.md).

Pour basculer un appareil physique StorSimple vers un autre appareil physique, accédez à [Basculer vers un appareil physique StorSimple](storsimple-8000-device-failover-physical-device.md). Pour basculer un appareil vers lui-même, accédez à [Basculer vers le même appareil physique StorSimple](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Composants requis

- Veillez à avoir consulté les considérations relatives au basculement d’appareil. Pour plus d’informations, accédez à [Considérations courantes relatives au basculement d’appareil](storsimple-8000-device-failover-disaster-recovery.md).

- Vous devez avoir créé et configuré une StorSimple Cloud Appliance avant d’exécuter cette procédure. Si vous exécutez la version logicielle Update 3 ou une version ultérieure, envisagez d’utiliser une appliance cloud 8020 pour la récupération d’urgence. Le modèle 8020 a 64 To et utilise le stockage Premium. Pour plus d’informations, accédez à [Déployer et gérer une StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Étapes de basculement vers une appliance cloud

Procédez comme suit pour restaurer l’appareil vers une StorSimple Cloud Appliance cible.

1.  Vérifiez que le conteneur de volumes que vous souhaitez basculer est associé à des instantanés cloud. Pour plus d’informations, accédez à [Utiliser le service StorSimple Device Manager pour créer des sauvegardes](storsimple-8000-manage-backup-policies-u2.md).
2. Accédez à votre service StorSimple Device Manager et cliquez sur **Appareils**. Dans le panneau **Appareils**, accédez à la liste des appareils connectés à votre service.
    ![Sélectionner l’appareil](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Sélectionnez et cliquez sur votre appareil source. L’appareil source contient les conteneurs de volumes que vous souhaitez basculer. Accédez à **Paramètres > Conteneurs de volumes**.

    ![Sélectionnez l’appareil](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Sélectionnez un conteneur de volume que vous souhaitez basculer vers un autre appareil. Cliquez sur le conteneur de volume pour afficher la liste des volumes dans ce conteneur. Sélectionnez un volume, cliquez avec le bouton droit, puis cliquez sur **Mettre hors connexion** afin de mettre le volume hors connexion.

    ![Sélectionnez l’appareil](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Répétez ce processus pour tous les volumes dans le conteneur de volume.

     ![Sélectionnez l’appareil](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Répétez l’étape précédente pour tous les conteneurs de volume que vous souhaitez basculer vers un autre appareil.

7. Revenez au panneau **Appareils**. Dans la barre de commandes, cliquez sur **Effectuer un basculement**.

    ![Cliquer sur Effectuer un basculement](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. Dans le panneau **Effectuer un basculement**, procédez comme suit :
   
    1. Cliquez sur **Source**. Sélectionnez les conteneurs de volumes à basculer. **Seuls les conteneurs de volumes associés à des instantanés cloud et des volumes hors connexion sont affichés.**
        ![Sélectionner une source](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Cliquez sur **Cible**. Sélectionnez une appliance cloud cible dans la liste déroulante des appareils disponibles. **Seuls les appareils disposant d’une capacité suffisante pour accueillir les conteneurs de volumes source sont affichés dans la liste.**

        ![Sélectionner la cible](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Passez en revue les paramètres de basculement sous **Résumé** et activez la case à cocher indiquant que les volumes dans les conteneurs de volumes sélectionnés sont hors connexion. 

        ![Passer en revue les paramètres de basculement](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. Un travail de basculement est créé. Pour surveiller le travail de basculement, cliquez sur la notification de travail.

    ![Surveiller le travail de basculement](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. Une fois le basculement terminé, revenez au panneau **Appareils** .

    1. Sélectionnez l’appareil qui a été utilisé en tant que cible pour le basculement.

       ![Sélectionnez l’appareil](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Cliquez sur **Conteneurs de volumes**. Tous les conteneurs de volume, ainsi que les volumes de l’ancien appareil, doivent être répertoriés.

       Si le conteneur de volumes que vous avez basculé contient des volumes épinglés localement, ces volumes sont basculés en tant que volumes à plusieurs niveaux. Les volumes épinglés localement ne sont pas pris en charge sur une StorSimple Cloud Appliance.

       ![Afficher les conteneurs de volumes cibles](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Étapes suivantes

* Après avoir effectué un basculement, vous devrez peut-être [désactiver ou supprimer votre appareil StorSimple](storsimple-8000-deactivate-and-delete-device.md).

* Pour plus d’informations sur l’utilisation du service StorSimple Device Manager, accédez à [Utiliser le service StorSimple Device Manager pour gérer votre appareil StorSimple](storsimple-8000-manager-service-administration.md).


