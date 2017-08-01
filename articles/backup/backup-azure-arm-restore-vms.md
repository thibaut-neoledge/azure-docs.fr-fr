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
ms.date: 3/12/2017
ms.author: markgal;trinadhk;
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 2ab86ed8aafb01e97b3ac9ba0411f4b80f88ac5b
ms.contentlocale: fr-fr
ms.lasthandoff: 06/16/2017


---
# <a name="use-azure-portal-to-restore-virtual-machines"></a>Utiliser le portail Azure pour restaurer des machines virtuelles
> [!div class="op_single_selector"]
> * [Restaurer des machines virtuelles dans le portail classique](backup-azure-restore-vms.md)
> * [Restaurer des machines virtuelles dans le portail Azure](backup-azure-arm-restore-vms.md)
>
>

Protégez vos données en prenant des instantanés de vos données à des intervalles définis. Ces instantanés sont considérés comme des points de récupération stockés dans des coffres Recovery Services. Lorsqu’il est nécessaire de réparer ou de générer de nouveau une machine virtuelle, sa restauration peut s’effectuer à partir des points de récupération enregistrés. Lorsque vous restaurez un point de récupération, vous pouvez créer une machine virtuelle qui est une représentation dans le temps de votre machine virtuelle sauvegardée, restaurer des disques et utiliser le modèle fourni pour personnaliser la machine virtuelle restaurée ou effectuer une récupération de fichier individuel. Cet article explique comment restaurer une machine virtuelle sur une nouvelle machine virtuelle ou restaurer tous les disques de secours. Pour la récupération de fichier individuel, voir [Récupérer des fichiers à partir de la sauvegarde de machine virtuelle Azure](backup-azure-restore-files-from-vm.md)

![3-ways-restore-from-vm-backup](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)

> [!NOTE]
> Azure dispose de deux modèles de déploiement pour créer et utiliser des ressources : [Azure Resource Manager et Azure Classic](../azure-resource-manager/resource-manager-deployment-model.md). Cet article fournit les informations et les procédures relatives à la restauration des machines virtuelles déployées à l’aide du modèle Resource Manager.
>
>

La restauration d’une machine virtuelle ou de tous les disques à partir de la sauvegarde de machine virtuelle implique deux étapes :

1. Sélectionner un point pour la restauration
2. Sélection du type de restauration - créez une machine virtuelle ou restaurez des disques et spécifiez les paramètres requis. 

## <a name="select-restore-point-for-restore"></a>Sélectionner un point pour la restauration
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

## <a name="choosing-a-vm-restore-configuration"></a>Choisir une configuration de restauration de machine virtuelle
Maintenant que vous avez sélectionné le point de restauration, choisissez une configuration pour votre machine virtuelle de restauration. Pour configurer la machine virtuelle de restauration, vous pouvez utiliser le portail Azure ou PowerShell.

1. Si vous n’en n’êtes pas encore là, accédez au panneau **Restaurer** . Vérifiez qu’un [Point de restauration](#select-restore-point-for-restore) a bien été sélectionné, puis cliquez sur **Configuration de la restauration** pour ouvrir le panneau **Configuration de la récupération**.

    ![l’assistant de configuration de récupération est défini](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard-recovery-type.png)
2. Dans le panneau **Restaurer la configuration**, vous avez deux possibilités :
   * Restaurer la totalité de la machine virtuelle
   * Restaurer des disques sauvegardés

Le Portail fournit une option Création rapide pour la machine virtuelle restaurée. Si vous souhaitez personnaliser la configuration de la machine virtuelle ou les noms des ressources créées lors du choix de la machine virtuelle, utilisez PowerShell ou le portail pour restaurer les disques sauvegardés, utilisez les commandes PowerShell pour les joindre au choix de configuration de la machine virtuelle ou utilisez le modèle fourni avec les disques de restauration pour personnaliser la machine virtuelle restaurée. Pour plus d’informations sur la façon de restaurer une machine virtuelle possédant plusieurs cartes réseau ou sous équilibrage de charge, voir [Restauration d’une machine virtuelle avec des configurations de réseau spéciales](#restoring-vms-with-special-network-configurations). Si votre machine virtuelle Windows utilise des [licences HUB](../virtual-machines/windows/hybrid-use-benefit-licensing.md), vous devez restaurer des disques et utiliser PowerShell/modèle comme indiqué ci-dessous pour créer la machine virtuelle et veiller à spécifier LicenseType comme « Windows_Server » lors de la création de machines virtuelles pour profiter des avantages HUB sur la machine virtuelle restaurée. 
 
## <a name="create-a-new-vm-from-restore-point"></a>Créer une machine virtuelle à partir du point de restauration
Si ce n’est pas encore fait, [sélectionnez un point de restauration](#restoring-vms-with-special-network-configurations) avant de procéder à la création d’une machine virtuelle à partir du point de restauration. Une fois le point de restauration sélectionné, sur le panneau **Configuration de la restauration**, entrez ou sélectionnez des valeurs pour chacun des champs suivants :

* **Type de restauration** : créer une machine virtuelle.
* **Nom de la machine virtuelle** : entrez un nom pour la machine virtuelle. Le nom doit être unique pour le groupe de ressources (pour une machine virtuelle déployée à l'aide de Resource Manager) ou le service cloud (pour une machine virtuelle classique). Vous ne pouvez pas remplacer la machine virtuelle si elle existe déjà dans l’abonnement.
* **Groupe de ressources** : sélectionnez un groupe de ressources existant ou créez-en un. Si vous restaurez une machine virtuelle classique, utilisez ce champ pour spécifier le nom d’un nouveau service cloud. Si vous créez un nouveau groupe de ressources/service cloud, le nom doit être globalement unique. En général, le nom du service cloud est associé à une URL publique. Par exemple : [cloudservice]. cloudapp.net. Si vous essayez d’utiliser un nom de service cloud/groupe de ressources cloud déjà utilisé, Azure attribue au service cloud/groupe de ressources le même nom que la machine virtuelle. Azure affiche les groupes de ressources/services cloud et les machines virtuelles qui ne sont associées à aucun groupe d’affinités. Pour en savoir plus, consultez [Migrer des groupes d’affinités vers un réseau virtuel régional](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
* **Réseau virtuel** : sélectionnez le réseau virtuel lors de la création de la machine virtuelle. Le champ indique tous les réseaux virtuels associés à l’abonnement. Le groupe de ressources de la machine virtuelle s’affiche entre parenthèses.
* **Sous-réseau** : si le réseau virtuel a des sous-réseaux, le premier sous-réseau est sélectionné par défaut. En présence de sous-réseaux supplémentaires, sélectionnez le sous-réseau souhaité.
* **Compte de stockage** : ce menu répertorie les comptes de stockage au même emplacement que le coffre Recovery Services. Les comptes de stockage redondant dans une zone ne sont pas pris en charge. S’il n’existe aucun compte de stockage avec le même emplacement que le coffre Recovery Services, vous devez en créer un avant de commencer l’opération de restauration. Le type de réplication du compte de stockage est indiqué entre parenthèses.

![l’assistant de configuration de restauration est défini](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

> [!NOTE]
> 1. Si vous restaurez une machine virtuelle déployée à l'aide de Resource Manager, vous devez identifier un réseau virtuel (VNET). Un réseau virtuel (VNET) est facultatif pour une machine virtuelle classique.
> 2. Si vous restaurez des machines virtuelles avec des disques gérés, vérifiez que le chiffrement du service de stockage (SSE) n’est pas activé sur le compte de stockage sélectionné au cours de la durée de vie de celui-ci.
> 3. Selon le type de stockage du compte de stockage sélectionné (Premium ou Standard), tous les disques restaurés seront des disques Premium ou Standard. Actuellement, le mode mixte de disques n’est pas pris en charge lors de la restauration.  
>
>

Sur le panneau **Configuration de la restauration**, cliquez sur **OK** pour finaliser la configuration de la restauration. Sur le panneau **Restaurer**, cliquez sur **Restaurer** pour déclencher l’opération de restauration.

## <a name="restore-backed-up-disks"></a>Restaurer des disques sauvegardés
Si vous souhaitez personnaliser la machine virtuelle que vous créez à partir des disques sauvegardés d’une façon différente de celle qui est spécifiée dans le panneau de configuration de la restauration, sélectionnez **Restaurer les disques** comme valeur de **Restaurer le type**. Ce choix exige un compte de stockage sur lequel copier les disques à partir des sauvegardes. Lorsque vous choisissez un compte de stockage, sélectionnez un compte qui partage le même emplacement que le coffre Recovery Services. Les comptes de stockage redondant dans une zone ne sont pas pris en charge. S’il n’existe aucun compte de stockage avec le même emplacement que le coffre Recovery Services, vous devez en créer un avant de commencer l’opération de restauration. Le type de réplication du compte de stockage est indiqué entre parenthèses.

Une fois l’opération de restauration terminée, vous pouvez :
* [Utiliser un modèle pour personnaliser la machine virtuelle restaurée](#use-templates-to-customize-restore-vm)
* [Utilisez les disques restaurés pour les attacher à une machine virtuelle existante](../virtual-machines/windows/attach-disk-portal.md)
* [Créer une machine virtuelle à l’aide de PowerShell à partir de disques restaurés](./backup-azure-vms-automation.md#restore-an-azure-vm)

Sur le panneau **Configuration de la restauration**, cliquez sur **OK** pour finaliser la configuration de la restauration. Sur le panneau **Restaurer**, cliquez sur **Restaurer** pour déclencher l’opération de restauration.

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
    
## <a name="use-templates-to-customize-restore-vm"></a>Utiliser des modèles pour personnaliser la machine virtuelle de restauration
Une fois [l’opération de restauration des disques terminée](#Track-the-restore-operation), vous pouvez utiliser le modèle généré dans le cadre de l’opération de restauration pour créer une machine virtuelle avec une configuration différente de la configuration de la sauvegarde ou pour personnaliser les noms de ressources créés lors de l’opération Créer une machine virtuelle à partir du point de restauration. 

> [!NOTE]
> Les modèles seront ajoutés dans le cadre de la restauration des disques pour les points de récupération effectuée après le 1er mars 2017. Ils sont applicables aux machines virtuelles non chiffrées et aux machines virtuelles avec disque non géré. La prise en charge des machines virtuelles chiffrées et des machines virtuelles avec disque géré est prévue dans une prochaine version. 
>
>

Pour obtenir le modèle généré dans le cadre de l’option de restauration des disques,

1. Accédez aux détails de la tâche correspondant à l’opération de restauration. 
2. Cette opération répertorie l’URI de modèle à partir duquel télécharger le modèle. Notez le nom du conteneur à partir des valeurs. 

     ![Détail du travail de restauration](./media/backup-azure-arm-restore-vms/restore-job-drill-down.png)
     
3. Notez le nom du compte de stockage cible, le nom du conteneur et l’URI de l’objet blob de modèle à partir des valeurs. Accédez à *compte de stockage cible > sélectionner des objets blob > Conteneurs*, puis accédez au fichier et téléchargez le fichier qui commence par le nom *azuredeploy*.

    ![download-template-storage-account](./media/backup-azure-arm-restore-vms/download-template.png)
    
   Vous pouvez également utiliser [Azure Storage Explorer](http://storageexplorer.com/) pour accéder à l’abonnement correspondant > compte de stockage cible > conteneurs d’objets blob et sélectionnez le nom du conteneur noté à l’étape précédente. Dans le volet de droite qui affiche les fichiers inclus dans le conteneur, téléchargez le fichier qui commence par le nom *azuredeploy*. 
   
   ![download-template-storage-explorer](./media/backup-azure-arm-restore-vms/template-storage-explorer-download.png)
     
Une fois le modèle est téléchargé, utiliser le déploiement du modèle pour [modifier et déployer le modèle](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) ou ajouter d’autres personnalisations en [créant un modèle](../azure-resource-manager/resource-group-authoring-templates.md) avant de déployer. Vous pouvez utiliser l’option Charger le fichier pour déployer le modèle téléchargé plus haut. 

   ![chargement du déploiement de modèle](./media/backup-azure-arm-restore-vms/loading-template.png)
   
Après avoir entré les valeurs requises, acceptez les *Conditions générales*, puis cliquez sur **Acheter**.

   ![envoi du déploiement de modèle](./media/backup-azure-arm-restore-vms/submitting-template.png)

## <a name="post-restore-steps"></a>Étapes post-restauration
* Si vous utilisez une distribution Linux basée sur cloud-init telle qu’Ubuntu, le mot de passe est bloqué après la restauration pour des raisons de sécurité. Pour [réinitialiser le mot de passe](../virtual-machines/linux/classic/reset-access.md), utilisez l’extension VMAccess sur la machine virtuelle restaurée. Nous vous recommandons d’utiliser des clés SSH sur ces distributions pour éviter de réinitialiser le mot de passe après la restauration.
* Les extensions présentes au cours de la configuration de sauvegarde sont installées, mais pas activées. En cas de problème, réinstallez les extensions. 
* Si la machine virtuelle sauvegardée a une IP statique, la machine virtuelle restaurée aura une adresse IP dynamique après la restauration pour éviter tout conflit lors de la création d’une machine virtuelle restaurée. En savoir plus sur la façon dont vous pouvez [ajouter une adresse IP statique à la machine virtuelle restaurée](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
* La machine virtuelle restaurée n’aura pas de valeur de disponibilité définie. Nous recommandons d’utiliser l’option de restauration des disques et [d’ajouter un groupe à haute disponibilité](../virtual-machines/windows/create-availability-set.md#use-powershell-to-create-an-availability-set) lors de la création d’une machine virtuelle depuis PowerShell ou de modèles à l’aide de disques restaurés. 


## <a name="backup-for-restored-vms"></a>Sauvegarde de machines virtuelles restaurées
Si vous avez restauré une machine virtuelle sur le même groupe de ressources avec le même nom que la machine virtuelle sauvegardée d’origine, la sauvegarde se poursuit sur la machine virtuelle après la restauration. Si vous avez restauré une machine virtuelle sur un autre groupe de ressources ou si vous avez spécifié un autre nom pour la machine virtuelle restaurée, celle-ci est traitée comme une machine virtuelle nouvelle et vous devez configurer la sauvegarde pour la machine virtuelle restaurée.

## <a name="restoring-a-vm-during-azure-datacenter-disaster"></a>Restauration d’une machine virtuelle en cas de défaillance du centre de données Azure
Azure Backup permet de restaurer des machines virtuelles sauvegardées sur le centre de données associé en cas de défaillance du centre de données principal sur lequel s’exécutent les machines virtuelles et si vous avez configuré le coffre de sauvegarde pour qu’il soit géo-redondant. Le cas échéant, vous devez sélectionner un compte de stockage présent dans le centre de données associé ; le reste du processus de restauration est le même. Azure Backup utilise le service de calcul du centre de données géo-redondant associé pour créer la machine virtuelle restaurée. En savoir plus sur la [résilience des centres de données Azure](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)

## <a name="restoring-domain-controller-vms"></a>Restauration de machines virtuelles d’un contrôleur de domaine
Azure Backup prend en charge la sauvegarde de machines virtuelles d’un contrôleur de domaine. Vous devez toutefois faire preuve de prudence au cours du processus de restauration. Le processus de restauration correct dépend de la structure du domaine. Dans le cas le plus simple, vous avez un seul contrôleur de domaine dans un domaine unique. Plus communément pour les charges de production, vous avez un domaine unique avec plusieurs contrôleurs de domaine, dont certains sont peut-être sur site. Enfin, vous avez peut-être une forêt avec plusieurs domaines. 

Du point de vue d’Active Directory, la machine virtuelle Azure est semblable à toute autre machine virtuelle sur un hyperviseur pris en charge moderne. La principale différence avec les hyperviseurs sur site est qu’aucune console de machine virtuelle n’est disponible dans Azure. Une console est nécessaire pour certains scénarios tels que la récupération à l’aide d’une sauvegarde de type Récupération complète (BMR, Bare Metal Recovery). Toutefois, la restauration d’une machine virtuelle à partir du coffre de sauvegarde constitue un remplacement total de la récupération complète. Le mode DSRM (Active Directory Restore Mode) est également disponible, donc tous les scénarios de récupération Active Directory sont viables. Pour plus d’informations, consultez les rubriques sur les [considérations en matière de sauvegarde et de restauration pour les contrôleurs de domaine virtualisés](https://technet.microsoft.com/en-us/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) et la [planification de la récupération de forêt Active Directory](https://technet.microsoft.com/en-us/library/planning-active-directory-forest-recovery(v=ws.10).aspx).

### <a name="single-dc-in-a-single-domain"></a>Contrôleur de domaine unique dans un seul domaine
La machine virtuelle peut être restaurée (comme toute autre machine virtuelle) depuis le portail Azure ou à l’aide de PowerShell.

### <a name="multiple-dcs-in-a-single-domain"></a>Contrôleurs de domaine multiples dans un seul domaine
Lorsque d’autres contrôleurs de domaine du même domaine sont accessibles sur le réseau, le contrôleur de domaine peut être restauré comme n’importe quelle machine virtuelle. S’il s’agit du dernier contrôleur de domaine dans le domaine, ou si une récupération dans un réseau isolé est effectuée, une procédure de récupération de forêt doit être appliquée.

### <a name="multiple-domains-in-one-forest"></a>Domaines multiples dans une seule forêt
Lorsque d’autres contrôleurs de domaine du même domaine sont accessibles sur le réseau, le contrôleur de domaine peut être restauré comme n’importe quelle machine virtuelle. Toutefois, dans tous les autres cas, une récupération de forêt est recommandée.

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

