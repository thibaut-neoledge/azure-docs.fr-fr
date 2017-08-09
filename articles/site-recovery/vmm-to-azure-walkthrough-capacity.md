---
title: "Planification de la capacité et de la mise à l’échelle pour la réplication des machines virtuelles Hyper-V (avec VMM) vers Azure avec Azure Site Recovery | Microsoft Docs"
description: "Utilisez cet article pour planifier la capacité et la mise à l’échelle lors de la réplication des machines virtuelles Hyper-V dans les clouds VMM vers Azure, avec Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 41c3c83e-6b1a-496a-8179-498c552ef0c7
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: ab1dc21a829140f8cd2e57837d83a05b0d71bcdf
ms.contentlocale: fr-fr
ms.lasthandoff: 07/26/2017

---

# <a name="step-3-plan-capacity-and-scaling-for-hyper-v-with-vmm-to-azure-replication"></a>Étape 3 : Planifier la capacité et la mise à l’échelle pour la réplication d’Hyper-V (avec VMM) vers Azure

Après avoir vérifié les [prérequis pour le déploiement](site-recovery-overview.md), utilisez cet article pour déterminer la planification de la capacité et de la mise à l’échelle pendant la réplication vers Azure de machines virtuelles Hyper-V locales dans les clouds System Center Virtual Machine Manager (VMM) avec [Azure Site Recovery](vmm-to-azure-walkthrough-prerequisites.md).

Après avoir lu cet article, envoyez vos commentaires en bas ou posez vos questions techniques sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="how-do-i-start-capacity-planning"></a>Comment dois-je commencer la planification de la capacité ?


Collectez des informations sur l’environnement de réplication, puis planifiez la capacité en tenant compte de ces données et des considérations présentes dans l’article.


## <a name="gather-information"></a>Collecter des informations

1. collecter les informations relatives à votre environnement de réplication, notamment les machines virtuelles, le nombre de disques par machine virtuelle et le stockage par disque ;
2. Déterminer le taux de modification (l’évolution) quotidienne des données répliquées. Téléchargez l’[outil de planification de la capacité Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) pour obtenir le taux de modifications. Nous vous recommandons d’exécuter cet outil sur une semaine pour enregistrer les moyennes.
 

## <a name="figure-out-capacity"></a>Déterminer la capacité

En fonction des informations que vous avez collectées, exécutez l’outil [Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) pour analyser votre environnement source et vos charges de travail, déterminer vos besoins en bande passante et en ressources serveur à l’emplacement source, ainsi que les ressources (machines virtuelles et stockage, etc.) dont vous avez besoin à l’emplacement cible. Vous pouvez exécuter l’outil de deux manières :

- Planification rapide : exécutez l’outil dans ce mode pour obtenir des projections réseau et serveur sur la base de la quantité moyenne de machines virtuelles, de disques et de stockage, et sur le taux de modifications moyen.
- Planification détaillée : exécutez l’outil dans ce mode et fournissez les détails de chaque charge de travail au niveau de la machine virtuelle. Analysez la compatibilité de machine virtuelle et obtenez des projections réseau et serveur.

Exécuter l’outil :

1. Téléchargez l’[outil](http://aka.ms/asr-capacity-planner-excel)
2. Pour exécuter la planification rapide, suivez [ces instructions](site-recovery-capacity-planner.md#run-the-quick-planner) et sélectionnez le scénario **Hyper-V vers Azure**.
3. Pour exécuter la planification détaillée, suivez [ces instructions](site-recovery-capacity-planner.md#run-the-detailed-planner) et sélectionnez le scénario **Hyper-V vers Azure**.

## <a name="control-network-bandwidth"></a>Contrôler la bande passante réseau

Une fois que vous avez calculé la bande passante dont vous avez besoin, vous avez deux options pour contrôler la quantité de bande passante utilisée pour la réplication :

* **Limite de bande passante**: le trafic Hyper-V qui est répliqué vers Azure passe par un hôte Hyper-V spécifique. Vous pouvez limiter la bande passante sur le serveur hôte.
* **Influer sur la bande passante** : vous pouvez influer sur la bande passante utilisée pour la réplication à l’aide de quelques clés de Registre.

### <a name="throttle-bandwidth"></a>Limiter la bande passante
1. Ouvrez le composant logiciel enfichable MMC de Microsoft Azure Backup sur le serveur hôte Hyper-V. Par défaut, un raccourci vers Microsoft Azure Backup est créé sur le Bureau. Vous pouvez également le trouver ici : C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Dans le composant logiciel enfichable, cliquez sur **Modifier les propriétés**.
3. Dans l’onglet **Limitation**, sélectionnez **Activer la limitation de la bande passante sur Internet pour les opérations de sauvegarde** et définissez les limites relatives aux heures de travail et aux heures non travaillées. Les plages valides vont de 512 Kbits/s à 102 Mbits/s par seconde.

    ![Limiter la bande passante](./media/vmm-to-azure-walkthrough-capacity/throttle2.png)

Vous pouvez également utiliser l’applet de commande [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) pour définir la limitation. Voici un exemple :

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indique qu’aucune limitation n’est requise.

### <a name="influence-network-bandwidth"></a>Influer sur la bande passante réseau
1. Dans le registre, accédez à **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Pour influer sur le trafic de la bande passante sur un disque de réplication, modifiez la valeur du paramètre **UploadThreadsPerVM**, ou créez la clé si elle n’existe pas.
   * Pour influer sur la bande passante utilisée pour le trafic lié à la restauration automatique à partir d’Azure, modifiez la valeur du paramètre **DownloadThreadsPerVM**.
2. La valeur par défaut est 4. Dans un réseau « surutilisé », ces clés de Registre doivent être modifiées par rapport aux valeurs par défaut. La valeur maximale est de 32. Surveillez le trafic pour optimiser la valeur.

## <a name="next-steps"></a>Étapes suivantes

Aller à l’[Étape 4 : Planifier la mise en réseau](vmm-to-azure-walkthrough-network.md).

