---
title: Sauvegarde de machines virtuelles Azure | Microsoft Docs
description: "Détectez, inscrivez et sauvegardez des machines virtuelles Azure dans un coffre Recovery Services."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "sauvegarde de machine virtuelle ; sauvegarder la machine virtuelle ; sauvegarde et récupération d’urgence ; sauvegarde de machine virtuelle arm"
ms.assetid: 5c68481d-7be3-4e68-b87c-0961c267053e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/15/2017
ms.author: trinadhk;jimpark;markgal;
translationtype: Human Translation
ms.sourcegitcommit: dca042ce1684b35e6a874075e0de28b9d8766331
ms.openlocfilehash: 981c8652629e96f482d9a62b70b0f0992517019f
ms.lasthandoff: 02/16/2017


---
# <a name="back-up-azure-vms-to-a-recovery-services-vault"></a>Sauvegarder des machines virtuelles Azure dans un coffre Recovery Services
> [!div class="op_single_selector"]
> * [Back up VMs to Recovery Services vault](backup-azure-arm-vms.md)
> * [Back up VMs to Backup vault](backup-azure-vms.md)
>
>

Cet article décrit la procédure de sauvegarde des machines virtuelles Azure (déployées à l’aide du modèle Resource Manager ou du modèle Classic) dans un coffre Recovery Services. La plupart du travail requis pour la sauvegarde des machines virtuelles repose sur la préparation. Avant de sauvegarder ou de protéger une machine virtuelle, vous devez remplir les [conditions préalables](backup-azure-arm-vms-prepare.md) pour préparer votre environnement à la protection de vos machines virtuelles. Une fois que vous avez rempli les conditions préalables, vous pouvez lancer l’opération de sauvegarde pour prendre des instantanés de votre machine virtuelle.


[!INCLUDE [learn about backup deployment models](../../includes/backup-deployment-models.md)]

Pour plus d’informations, consultez les articles sur la [planification de votre infrastructure de sauvegarde des machines virtuelles dans Azure](backup-azure-vms-introduction.md) et les [machines virtuelles Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="triggering-the-backup-job"></a>Déclenchement du travail de sauvegarde
La stratégie de sauvegarde associée au coffre Recovery Services définit la fréquence et à quel moment l’opération de sauvegarde s’exécute. Par défaut, la première sauvegarde planifiée est la sauvegarde initiale. Jusqu’à celle-ci, l’état de la dernière sauvegarde dans le panneau **Travaux de sauvegarde** est défini sur **Avertissement (sauvegarde initiale en attente)**.

![Sauvegarde en attente](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

À moins que votre sauvegarde initiale ne soit prévue prochainement, il est recommandé de sélectionner l’option **Sauvegarder maintenant**. La procédure suivante commence à partir du tableau de bord du coffre. Cette procédure est utilisée pour l’exécution du travail de sauvegarde initial une fois les conditions préalables remplies. Si le travail de sauvegarde initial a déjà été exécuté, cette procédure n’est pas disponible. La stratégie de sauvegarde associée détermine le prochain travail de sauvegarde.  

Pour exécuter le travail de sauvegarde initial :

1. Dans le tableau de bord du coffre, cliquez sur le numéro sous **Éléments de sauvegarde** ou sur la mosaïque **Éléments de sauvegarde**. <br/>
  ![Icône Paramètres](./media/backup-azure-vms-first-look-arm/rs-vault-config-vm-back-up-now-1.png)

  Le panneau **Éléments de sauvegarde** s’ouvre.

  ![Éléments de sauvegarde](./media/backup-azure-vms-first-look-arm/back-up-items-list.png)

2. Sélectionnez l’élément dans le panneau **Éléments de sauvegarde**.

  ![Icône Paramètres](./media/backup-azure-vms-first-look-arm/back-up-items-list-selected.png)

  La liste **Éléments de sauvegarde** s’ouvre. <br/>

  ![Travail de sauvegarde déclenché](./media/backup-azure-vms-first-look-arm/backup-items-not-run.png)

3. Dans la liste **Éléments de sauvegarde**, cliquez sur le bouton de sélection **... ** pour ouvrir le menu contextuel.

  ![Menu contextuel](./media/backup-azure-vms-first-look-arm/context-menu.png)

  Le menu contextuel s’affiche.

  ![Menu contextuel](./media/backup-azure-vms-first-look-arm/context-menu-small.png)

4. Dans le menu contextuel, cliquez sur **Sauvegarder maintenant**.

  ![Menu contextuel](./media/backup-azure-vms-first-look-arm/context-menu-small-backup-now.png)

  Le panneau Sauvegarder maintenant s’ouvre.

  ![affiche le panneau Sauvegarder maintenant](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

5. Dans le panneau Sauvegarder maintenant, cliquez sur l’icône de calendrier. Utilisez le contrôle de calendrier pour sélectionner le dernier jour de conservation de ce point de récupération, puis cliquez sur **Sauvegarder**.

  ![définir le dernier jour de conservation du point de récupération de Sauvegarder maintenant](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  Des notifications de déploiement vous font savoir que la sauvegarde a été déclenchée et que vous pouvez surveiller la progression de la tâche sur la page Travaux de sauvegarde. Selon la taille de votre machine virtuelle, la création de la sauvegarde initiale peut prendre un certain temps.

6. Pour afficher ou suivre l’état de la sauvegarde initiale, dans le tableau de bord du coffre, dans la mosaïque **Travaux de sauvegarde**, cliquez sur **En cours**.

  ![Vignette Travaux de sauvegarde](./media/backup-azure-vms-first-look-arm/open-backup-jobs-1.png)

  Le panneau Travaux de sauvegarde s’ouvre.

  ![Vignette Travaux de sauvegarde](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view-1.png)

  Le panneau **Travaux de sauvegarde** indique l’état de tous les travaux. Vérifiez si le travail de sauvegarde de votre machine virtuelle est en cours d’exécution ou s’il est terminé. Lorsqu’un travail de sauvegarde est terminé, son état affiché est *Terminé*.

  > [!NOTE]
  > Dans le cadre de l’opération de sauvegarde, le service Azure Backup émet une commande vers l’extension de sauvegarde de chaque machine virtuelle pour vider toutes les écritures et prendre un instantané cohérent.
  >
  >

## <a name="troubleshooting-errors"></a>Résolution des erreurs
Si vous rencontrez des problèmes pendant la sauvegarde de votre machine virtuelle, consultez [l’article sur le dépannage des machines virtuelles](backup-azure-vms-troubleshoot.md) pour obtenir de l’aide.

## <a name="next-steps"></a>Étapes suivantes
Vous avez protégé votre machine virtuelle. Vous pouvez maintenant consulter les articles suivants pour en savoir plus sur les tâches de gestion de machine virtuelle et la restauration des machines virtuelles.

* [Gestion et surveillance de vos machines virtuelles](backup-azure-manage-vms.md)
* [Restauration des machines virtuelles](backup-azure-arm-restore-vms.md)

