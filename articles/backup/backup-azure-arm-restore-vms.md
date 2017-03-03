---
title: "Sauvegarde Azure : Restaurer des machines virtuelles à l’aide du portail Azure | Microsoft Docs"
description: "Restaurer une machine virtuelle Azure à partir d’un point de récupération à l’aide du portail Azure"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "restauration de sauvegarde ; restauration ; point de récupération ;"
ms.assetid: 372b87c6-3544-4dc5-bbc9-c742ca502159
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/26/2017
ms.author: markgal;trinadhk;
translationtype: Human Translation
ms.sourcegitcommit: d7a2b9c13b2c3372ba2e83f726c7bf5cc7e98c02
ms.openlocfilehash: 6f55bdbb97ead96edf7ca41562b1c4b5a712d6e8
ms.lasthandoff: 02/17/2017


---
# <a name="use-azure-portal-to-restore-virtual-machines"></a>Utiliser le portail Azure pour restaurer des machines virtuelles
> [!div class="op_single_selector"]
> * [Restaurer des machines virtuelles dans le portail classique](backup-azure-restore-vms.md)
> * [Restaurer des machines virtuelles dans le portail Azure](backup-azure-arm-restore-vms.md)
>
>

Protégez vos données en prenant des instantanés de vos données à des intervalles définis. Ces instantanés sont considérés comme des points de récupération stockés dans des coffres Recovery Services. Lorsqu’il est nécessaire de réparer ou de générer de nouveau une machine virtuelle, sa restauration peut s’effectuer à partir des points de récupération enregistrés. La restauration d’un point de récupération permet de revenir à l’état dans lequel se trouvait la machine virtuelle au moment de l’enregistrement du point de récupération. Cet article explique comment restaurer une machine virtuelle.

> [!NOTE]
> Azure dispose de deux modèles de déploiement pour créer et utiliser des ressources : [Azure Resource Manager et Azure Classic](../azure-resource-manager/resource-manager-deployment-model.md). Cet article fournit les informations et les procédures relatives à la restauration des machines virtuelles déployées à l’aide du modèle Resource Manager.
>
>

## <a name="restore-a-recovery-point"></a>Restaurer un point de récupération
1. Connectez-vous au [portail Azure](http://portal.azure.com/)
2. Dans le menu Azure, cliquez sur **Parcourir** et, dans la liste des services, tapez **Recovery Services**. La liste des services s’ajuste en fonction de ce que vous tapez. Lorsque vous voyez **Coffres Recovery Services**, cliquez dessus.

    ![Ouvrir le coffre Recovery Services](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    La liste des coffres de l’abonnement s’affiche.

    ![Liste des coffres Recovery Services](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
3. Dans la liste, sélectionnez le coffre associé à la machine virtuelle que vous souhaitez restaurer. Lorsque vous cliquez sur le coffre, son tableau de bord s’affiche.

    ![Liste des coffres Recovery Services](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)
4. Dans le tableau de bord du coffre, effectuez les opérations suivantes : Sur la vignette **Éléments de sauvegarde**, cliquez sur **Machines Virtuelles Azure** pour afficher les machines virtuelles associées au coffre.

    ![tableau de bord du coffre](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    Le panneau **Éléments de sauvegarde** s’ouvre et affiche la liste des machines virtuelles.

    ![liste des machines virtuelles du coffre](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)
5. Dans la liste, sélectionnez une machine virtuelle pour ouvrir le tableau de bord. Le tableau de bord de la machine virtuelle s’ouvre sur la zone de surveillance qui contient la vignette Points de restauration.

    ![liste des machines virtuelles du coffre](./media/backup-azure-arm-restore-vms/vm-blade.png)
6. Dans le menu du tableau de bord de la machine virtuelle, cliquez sur **Restaurer**

    ![liste des machines virtuelles du coffre](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    Le panneau de restauration s’ouvre.

    ![panneau de restauration](./media/backup-azure-arm-restore-vms/restore-blade.png)
7. Sur le panneau **Restaurer**, cliquez sur **Point de restauration** pour ouvrir le panneau **Sélectionner le point de restauration**.

    ![panneau de restauration](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    Par défaut, la boîte de dialogue affiche tous les points de restauration des 30 derniers jours. Utilisez le **filtre** pour modifier la plage d’heures des points de restauration affichés. Par défaut, les points de restauration de toute cohérence sont affichés. Modifiez le filtre **Tous les points de restauration** afin de sélectionner une cohérence spécifique des points de restauration. Pour plus d’informations sur chaque type de point de restauration, consultez l’explication de la [Cohérence des données](backup-azure-vms-introduction.md#data-consistency).  

   * **Cohérence du point de restauration** : dans cette liste, choisissez :
     * points de restauration cohérents d’incident ;
     * points de restauration cohérents d’application ;
     * points de restauration cohérents de système de fichiers ;
     * tous les points de restauration.  
8. Choisissez un point de restauration et cliquez sur **OK**.

    ![sélectionner un point de restauration](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    Le panneau **Restaurer** indique que le point de restauration est défini.

    ![le point de restauration est défini](./media/backup-azure-arm-restore-vms/recovery-point-set.png)
9. Sur le panneau **Restaurer**, l’option **Restaurer la configuration** s’ouvre automatiquement après la définition du point de restauration.

    ![l’assistant de configuration de restauration est défini](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

## <a name="choosing-a-vm-restore-configuration"></a>Choisir une configuration de restauration de machine virtuelle
Maintenant que vous avez sélectionné le point de restauration, choisissez une configuration pour votre machine virtuelle de restauration. Pour configurer la machine virtuelle de restauration, vous pouvez utiliser le portail Azure ou PowerShell.

> [!NOTE]
> Le Portail fournit une option Création rapide pour la machine virtuelle restaurée. Si vous souhaitez personnaliser la configuration de la machine virtuelle à restaurer, utilisez PowerShell ou le portail pour restaurer les disques sauvegardés et utilisez les commandes PowerShell pour les joindre au choix de configuration de la machine virtuelle. Consultez [Restauration d’une machine virtuelle avec des configurations de réseau spéciales](#restoring-vms-with-special-network-configurations).
>
>

1. Si vous n’en n’êtes pas encore là, accédez au panneau **Restaurer** . Vérifiez qu’un **Point de restauration** a bien été sélectionné, puis cliquez sur **Configuration de la restauration** pour ouvrir le panneau **Configuration de la récupération**.

    ![l’assistant de configuration de récupération est défini](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)
2. Dans le panneau **Restaurer la configuration**, vous avez deux possibilités :

   * Restaurer la totalité de la machine virtuelle
   * Restaurer des disques sauvegardés

   ### <a name="restore-full-virtual-machine"></a>Restaurer la totalité de la machine virtuelle
   Sur le panneau **Configuration de la restauration** , entrez ou sélectionnez des valeurs pour chacun des champs suivants :

   * **Type de restauration** : créer une machine virtuelle.
   * **Nom de la machine virtuelle** : entrez un nom pour la machine virtuelle. Le nom doit être unique pour le groupe de ressources (pour une machine virtuelle déployée à l'aide de Resource Manager) ou le service cloud (pour une machine virtuelle classique). Vous ne pouvez pas remplacer la machine virtuelle si elle existe déjà dans l’abonnement.
   * **Groupe de ressources** : sélectionnez un groupe de ressources existant ou créez-en un. Si vous restaurez une machine virtuelle classique, utilisez ce champ pour spécifier le nom d’un nouveau service cloud. Si vous créez un nouveau groupe de ressources/service cloud, le nom doit être globalement unique. En général, le nom du service cloud est associé à une URL publique. Par exemple : [cloudservice]. cloudapp.net. Si vous essayez d’utiliser un nom de service cloud/groupe de ressources cloud déjà utilisé, Azure attribue au service cloud/groupe de ressources le même nom que la machine virtuelle. Azure affiche les groupes de ressources/services cloud et les machines virtuelles qui ne sont associées à aucun groupe d’affinités. Pour en savoir plus, consultez [Migrer des groupes d’affinités vers un réseau virtuel régional](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
   * **Réseau virtuel** : sélectionnez le réseau virtuel lors de la création de la machine virtuelle. Le champ indique tous les réseaux virtuels associés à l’abonnement. Le groupe de ressources de la machine virtuelle s’affiche entre parenthèses.
   * **Sous-réseau** : si le réseau virtuel a des sous-réseaux, le premier sous-réseau est sélectionné par défaut. En présence de sous-réseaux supplémentaires, sélectionnez le sous-réseau souhaité.
   * **Compte de stockage** : ce menu répertorie les comptes de stockage au même emplacement que le coffre Recovery Services. Lorsque vous choisissez un compte de stockage, sélectionnez un compte qui partage le même emplacement que le coffre Recovery Services. Les comptes de stockage redondant dans une zone ne sont pas pris en charge. S’il n’existe aucun compte de stockage avec le même emplacement que le coffre Recovery Services, vous devez en créer un avant de commencer l’opération de restauration. Le type de réplication du compte de stockage est indiqué entre parenthèses.

     > [!NOTE]
     > Si vous restaurez une machine virtuelle déployée à l'aide de Resource Manager, vous devez identifier un réseau virtuel (VNET). Un réseau virtuel (VNET) est facultatif pour une machine virtuelle classique.
     >
     >

   ### <a name="restore-backed-up-disks"></a>Restaurer des disques sauvegardés
   Si vous souhaitez personnaliser la machine virtuelle que vous créez à partir des disques sauvegardés d’une façon différente de celle qui est spécifiée dans le panneau de configuration de la restauration, sélectionnez **Restaurer les disques** comme valeur de **Restaurer le type**. Si vous choisissez cette option, vous êtes invité à sélectionner un compte de stockage (les mêmes restrictions s’appliquent que pour la sélection d’un compte de stockage Restaurer la totalité de la machine virtuelle) où sont copiés les disques à partir des sauvegardes. Vous pouvez [attacher les disques restaurés à une machine virtuelle existante](../virtual-machines/virtual-machines-windows-attach-disk-portal.md) ou [créer une nouvelle machine virtuelle à partir des disques restaurés.](./backup-azure-vms-automation.md#restore-an-azure-vm)

3. Sur le panneau **Configuration de la restauration**, cliquez sur **OK** pour finaliser la configuration de la restauration.
4. Sur le panneau **Restaurer**, cliquez sur **Restaurer** pour déclencher l’opération de restauration.

    ![Configuration de la récupération terminée](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## <a name="track-the-restore-operation"></a>Suivi de l’opération de restauration
Une fois que vous déclenchez l’opération de restauration, le service de sauvegarde crée une tâche pour le suivi de l’opération de restauration. Par ailleurs, le service de sauvegarde crée et affiche temporairement la notification dans la zone Notifications du portail. Si vous ne voyez pas la notification, vous avez toujours la possibilité de cliquer sur l’icône Notifications pour afficher les notifications.

![Restauration déclenchée](./media/backup-azure-arm-restore-vms/restore-notification.png)

Pour voir l’opération en cours de traitement, ou l’afficher lorsqu’elle est terminée, ouvrez la liste des tâches de sauvegarde.

1. Dans le menu Azure, cliquez sur **Parcourir** et, dans la liste des services, tapez **Recovery Services**. La liste des services s’ajuste en fonction de ce que vous tapez. Lorsque vous voyez **Coffres Recovery Services**, cliquez dessus.

    ![Ouvrir le coffre Recovery Services](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    La liste des coffres de l’abonnement s’affiche.

    ![Liste des coffres Recovery Services](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
2. Dans la liste, sélectionnez le coffre associé à la machine virtuelle que vous avez restaurée. Lorsque vous cliquez sur le coffre, son tableau de bord s’affiche.
3. Sur la vignette **Travaux de sauvegarde** du tableau de bord du coffre, cliquez sur **Machines Virtuelles Azure** pour afficher les travaux associés au coffre.

    ![tableau de bord du coffre](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    Le panneau **Travaux de sauvegarde** s’ouvre et affiche la liste des travaux.

    ![liste des machines virtuelles du coffre](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)

## <a name="post-restore-steps"></a>Étapes post-restauration
* Si vous utilisez une distribution Linux basée sur cloud-init telle qu’Ubuntu, le mot de passe est bloqué après la restauration pour des raisons de sécurité. Pour [réinitialiser le mot de passe](../virtual-machines/virtual-machines-linux-classic-reset-access.md), utilisez l’extension VMAccess sur la machine virtuelle restaurée. Nous vous recommandons d’utiliser des clés SSH sur ces distributions pour éviter de réinitialiser le mot de passe après la restauration.
* Les extensions présentes au cours de la configuration de sauvegarde sont installées, mais pas activées. En cas de problème, réinstallez les extensions. 
* Si la machine virtuelle sauvegardée a une IP statique, la machine virtuelle restaurée aura une adresse IP dynamique après la restauration pour éviter tout conflit lors de la création d’une machine virtuelle restaurée. En savoir plus sur la façon dont vous pouvez [ajouter une adresse IP statique à la machine virtuelle restaurée](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
* La machine virtuelle restaurée n’aura pas de valeur de disponibilité définie. Nous recommandons d’utiliser l’option de restauration des disques et [d’ajouter un groupe à haute disponibilité](../virtual-machines/virtual-machines-windows-create-availability-set.md#use-powershell-to-create-an-availability-set) lors de la création d’une machine virtuelle depuis PowerShell à l’aide de disques restaurés. 

## <a name="backup-for-restored-vms"></a>Sauvegarde de machines virtuelles restaurées
Si vous avez restauré une machine virtuelle sur le même groupe de ressources avec le même nom que la machine virtuelle sauvegardée d’origine, la sauvegarde se poursuit sur la machine virtuelle après la restauration. Si vous avez restauré une machine virtuelle sur un autre groupe de ressources ou si vous avez spécifié un autre nom pour la machine virtuelle restaurée, celle-ci est traitée comme une machine virtuelle nouvelle et vous devez configurer la sauvegarde pour la machine virtuelle restaurée.

## <a name="restoring-a-vm-during-azure-datacenter-disaster"></a>Restauration d’une machine virtuelle en cas de défaillance du centre de données Azure
Azure Backup permet de restaurer des machines virtuelles sauvegardées sur le centre de données associé en cas de défaillance du centre de données principal sur lequel s’exécutent les machines virtuelles et si vous avez configuré le coffre de sauvegarde pour qu’il soit géo-redondant. Le cas échéant, vous devez sélectionner un compte de stockage présent dans le centre de données associé ; le reste du processus de restauration est le même. Azure Backup utilise le service de calcul du centre de données géo-redondant associé pour créer la machine virtuelle restaurée. En savoir plus sur la [résilience des centres de données Azure](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)

## <a name="restoring-vms-with-special-network-configurations"></a>Restauration de machines virtuelles avec des configurations de réseau spéciales
Il est possible de sauvegarder et de restaurer des machines virtuelles avec les configurations de réseau spéciales suivantes. Toutefois, ces configurations requièrent une attention particulière au cours du processus de restauration.

* Machines virtuelles sous un équilibreur de charge (interne et externe)
* Machines virtuelles avec plusieurs adresses IP réservées
* Machines virtuelles avec plusieurs cartes d'interface réseau

> [!IMPORTANT]
> Lors de la création de la configuration réseau spéciale pour les machines virtuelles, vous devez utiliser PowerShell pour créer des machines virtuelles à partir des disques restaurés.
>
>

Pour pouvoir recréer entièrement les machines virtuelles après les avoir restaurées sur le disque, procédez comme suit :

1. Restaurez les disques à partir d’un coffre Recovery services à l’aide de [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm)
2. Créez la configuration de machine virtuelle requise pour l’équilibreur de charge/plusieurs cartes réseau/plusieurs adresses IP réservées à l’aide des applets de commande PowerShell et utilisez-la pour créer la machine virtuelle avec la configuration souhaitée.

   * Créer une machine virtuelle dans le service cloud avec un [équilibreur de charge interne ](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)
   * Créer une machine virtuelle pour vous connecter à [l’équilibreur de charge accessible sur Internet](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted/)
   * Créer une machine virtuelle avec [plusieurs cartes d’interface réseau](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)
   * Créer des machines virtuelles avec [plusieurs adresses IP réservées](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous pouvez restaurer vos machines virtuelles, consultez l’article de résolution des problèmes pour en savoir plus sur les erreurs courantes relatives aux machines virtuelles. En outre, consultez l’article sur la gestion des tâches avec vos machines virtuelles.

* [Résolution des erreurs](backup-azure-vms-troubleshoot.md#restore)
* [Gestion des machines virtuelles](backup-azure-manage-vms.md)

