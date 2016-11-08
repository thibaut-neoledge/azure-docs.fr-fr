---
title: Sauvegarder des machines virtuelles Azure dans un coffre Recovery Services | Microsoft Docs
description: Découvrez, inscrivez et sauvegardez des machines virtuelles Azure dans un coffre Recovery Services avec ces procédures de sauvegarde des machines virtuelles Azure.
services: backup
documentationcenter: ''
author: markgalioto
manager: cfreeman
editor: ''
keywords: sauvegarde de machine virtuelle ; sauvegarder la machine virtuelle ; sauvegarde et récupération d’urgence ; sauvegarde de machine virtuelle arm

ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: trinadhk; jimpark; markgal;

---
# Sauvegarder des machines virtuelles Azure dans un coffre Recovery Services
> [!div class="op_single_selector"]
> * [Back up VMs to Recovery Services vault](backup-azure-arm-vms.md) (Sauvegarder des machines virtuelles dans un coffre Recovery Services)
> * [Back up VMs to Backup vault](backup-azure-vms.md) (Sauvegarder des machines virtuelles dans un coffre de sauvegarde)
> 
> 

Cet article décrit la procédure de sauvegarde des machines virtuelles Azure (déployées à l’aide du modèle Resource Manager ou du modèle Classic) dans un coffre Recovery Services. La majorité du travail de sauvegarde des machines virtuelles est effectuée pendant la préparation. Avant de sauvegarder ou de protéger une machine virtuelle, vous devez remplir les [conditions préalables](backup-azure-arm-vms-prepare.md) pour préparer votre environnement à la protection de vos machines virtuelles. Une fois que vous avez rempli les conditions préalables, vous pouvez lancer l’opération de sauvegarde pour prendre des instantanés de votre machine virtuelle.

> [!NOTE]
> Azure dispose de deux modèles de déploiement pour créer et utiliser des ressources : [Azure Resource Manager et Azure Classic](../resource-manager-deployment-model.md). Les coffres Recovery Services vous permettent de protéger aussi bien les machines virtuelles déployées à l’aide du modèle Resource Manager que celles déployées avec le modèle Classic. Consultez la page [Sauvegarde des machines virtuelles Azure](backup-azure-vms.md) pour plus d’informations sur l’utilisation des machines virtuelles avec le modèle de déploiement Classic.
> 
> 

Pour obtenir des informations supplémentaires, consultez les articles sur la [planification de votre infrastructure de sauvegarde des machines virtuelles dans Azure](backup-azure-vms-introduction.md) et les [machines virtuelles Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).

## Déclenchement du travail de sauvegarde
La stratégie de sauvegarde associée au coffre Recovery Services définit la fréquence et à quel moment l’opération de sauvegarde s’exécute. Par défaut, la première sauvegarde planifiée est la sauvegarde initiale. Jusqu’à celle-ci, l’état de la dernière sauvegarde dans le panneau **Travaux de sauvegarde** est défini sur **Avertissement (sauvegarde initiale en attente)**.

![Sauvegarde en attente](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

À moins que votre sauvegarde initiale soit imminente, il est recommandé d’utiliser l’option **Sauvegarder maintenant**. La procédure suivante commence à partir du tableau de bord du coffre. Cette procédure est utilisée pour l’exécution du travail de sauvegarde initial une fois les conditions préalables remplies. Si le travail de sauvegarde initial a déjà été exécuté, cette procédure n’est pas disponible. La stratégie de sauvegarde associée détermine le prochain travail de sauvegarde.

Pour exécuter le travail de sauvegarde initial :

1. Dans la vignette **Sauvegarde** du tableau de bord de l’archivage, cliquez sur **Machines virtuelles Azure**. <br/> ![Icône Paramètres](./media/backup-azure-vms-first-look-arm/rs-vault-in-dashboard-backup-vms.png)
   
    Le panneau **Éléments de sauvegarde** s’ouvre.
2. Dans le panneau **Éléments de sauvegarde**, cliquez avec le bouton droit sur l’archivage à sauvegarder, puis cliquez sur **Sauvegarder maintenant**.
   
    ![Icône Paramètres](./media/backup-azure-vms-first-look-arm/back-up-now.png)
   
    Le travail de sauvegarde est déclenché. <br/>
   
    ![Travail de sauvegarde déclenché](./media/backup-azure-vms-first-look-arm/backup-triggered.png)
3. Pour vérifier si votre sauvegarde initiale est terminée, dans la vignette **Travaux de sauvegarde** du tableau de bord de l’archivage, cliquez sur **Machines virtuelles Azure**.
   
    ![Vignette Travaux de sauvegarde](./media/backup-azure-vms-first-look-arm/open-backup-jobs.png)
   
    Le panneau Travaux de sauvegarde s’ouvre.
4. Le panneau **Travaux de sauvegarde** indique l’état de tous les travaux.
   
    ![Vignette Travaux de sauvegarde](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view.png)
   
   > [!NOTE]
   > Dans le cadre de l’opération de sauvegarde, le service Azure Backup émet une commande vers l’extension de sauvegarde de chaque machine virtuelle pour vider toutes les écritures et prendre un instantané cohérent.
   > 
   > 
   
    Lorsque le travail de sauvegarde est terminé, l’état affiché est *Terminé*.

## Résolution des erreurs
Si vous rencontrez des problèmes pendant la sauvegarde de votre machine virtuelle, consultez l’[article sur le dépannage des machines virtuelles](backup-azure-vms-troubleshoot.md) pour obtenir de l’aide.

## Étapes suivantes
Maintenant que vous avez protégé votre machine virtuelle, consultez les articles suivants indiquant les tâches de gestion supplémentaires que vous pouvez exécuter sur vos machines virtuelles et comment restaurer des machines virtuelles.

* [Gestion et surveillance de vos machines virtuelles](backup-azure-manage-vms.md)
* [Restauration des machines virtuelles](backup-azure-arm-restore-vms.md)

<!---HONumber=AcomDC_0803_2016-->