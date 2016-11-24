---
title: "Découverte : Protéger les machines virtuelles Azure avec un coffre Recovery Services | Microsoft Docs"
description: "Protégez les machines virtuelles Azure avec un coffre Recovery Services. Utilisez les sauvegardes des machines virtuelles déployées à l’aide de Resource Manager, des machines virtuelles déployées à l’aide du modèle Classic et des machines virtuelles Premium Storage pour protéger vos données. Créez et enregistrez un coffre Recovery Services. Enregistrez des machines virtuelles, créez une stratégie et protégez des machines virtuelles dans Azure."
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
editor: 
keyword: backups; vm backup
ms.assetid: 45e773d6-c91f-4501-8876-ae57db517cd1
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/10/2016
ms.author: markgal; jimpark
translationtype: Human Translation
ms.sourcegitcommit: 85b291e3916d1274fefc71bc0c1f12cac2920bb4
ms.openlocfilehash: 77b4f6e5ee18cb3772487820bc72d7794f82162f


---
# <a name="first-look-protect-azure-vms-with-a-recovery-services-vault"></a>Découverte : Protéger les machines virtuelles Azure avec un coffre Recovery Services
> [!div class="op_single_selector"]
> * [Protéger les machines virtuelles avec un coffre Recovery Services](backup-azure-vms-first-look-arm.md)
> * [Protéger les machines virtuelles avec un coffre de sauvegarde](backup-azure-vms-first-look.md)
>
>

Ce didacticiel détaille les procédures de création d’un coffre Recovery Services et de sauvegarde d’une machine virtuelle Azure. Les coffres Recovery Services protègent :

* Machines virtuelles déployées à l’aide de Resource Manager
* les machines virtuelles Classic,
* les machines virtuelles de stockage standard,
* les machines virtuelles Premium Storage.
* les machines virtuelles chiffrées à l’aide d’Azure Disk Encryption, avec des clés BEK et KEK

Pour plus d’informations sur la protection des machines virtuelles Premium Storage, consultez [Sauvegarder et restaurer des machines virtuelles Premium Storage](backup-introduction-to-azure-backup.md#using-premium-storage-vms-with-azure-backup)

> [!NOTE]
> Dans ce didacticiel, nous partons du principe que vous disposez déjà d’une machine virtuelle dans votre abonnement Azure et que vous avez autorisé le service de sauvegarde à accéder à la machine virtuelle.
>
>

[!INCLUDE [learn-about-Azure-Backup-deployment-models](../../includes/backup-deployment-models.md)]

En fonction du nombre de machines virtuelles que vous souhaitez protéger, vous pouvez commencer à partir de différents points : si vous voulez sauvegarder plusieurs machines virtuelles en une seule opération, accédez au coffre Recovery Services et lancez la sauvegarde à partir du tableau de bord du coffre. Si vous ne voulez sauvegarder qu’une seule machine virtuelle, vous pouvez effectuer la sauvegarde directement dans le panneau de gestion de la machine virtuelle.

## <a name="configure-backup-from-vm-management-blade"></a>Configurer la sauvegarde à partir du panneau de gestion de la machine virtuelle
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu Hub, cliquez sur **Plus de services** et, dans la liste des ressources, tapez **Machines virtuelles**.  La liste des machines virtuelles s’affiche. Dans la liste des machines virtuelles, sélectionnez celle que vous voulez sauvegarder. Cette action ouvre le panneau de gestion de la machine virtuelle. 
 ![Panneau de gestion de la machine virtuelle](./media/backup-azure-vms-first-look-arm/vm-management-blade.png)
 
3. Dans le panneau de gestion de la machine virtuelle, cliquez sur l’option « Sauvegarde » disponible sur le côté gauche sous Paramètres.
![Option de sauvegarde dans le panneau de gestion de la machine virtuelle](./media/backup-azure-vms-first-look-arm/backup-option-vm-management-blade.png)

4. Cette action ouvre le panneau Activer la sauvegarde. Ce panneau requiert deux entrées : Coffre Recovery Services (ressource Azure Backup utilisée pour stocker les sauvegardes des machines virtuelles) et Stratégie de sauvegarde (stratégie de sauvegarde qui spécifie la planification des sauvegardes et la durée pendant laquelle en conserver les copies). Ce panneau comporte des options définies par défaut. Vous pouvez les personnaliser en fonction des exigences de sauvegarde. 
![Activer l’Assistant Sauvegarde](./media/backup-azure-vms-first-look-arm/vm-blade-enable-backup.png)

5. Pour le coffre Recovery Services, vous pouvez sélectionner un coffre existant ou en créer un nouveau. Si vous créez un coffre, celui-ci est créé dans le même groupe de ressources que la machine virtuelle et son emplacement est identique à celui de cette dernière. Si vous souhaitez créer un coffre Recovery Services comportant des valeurs différentes, [créez un coffre Recovery Services](backup-azure-vms-first-look-arm.md#create-a-recovery-services-vault-for-a-vm) avant de cliquer sur l’option Sauvegarde lors de l’étape 3 et sélectionnez-le dans ce panneau. 

6. Dans le panneau Stratégie de sauvegarde, sélectionnez la stratégie de sauvegarde à appliquer au coffre, puis cliquez sur **OK**.
    ![Sélectionner la stratégie de sauvegarde](./media/backup-azure-vms-first-look-arm/setting-rs-backup-policy-new.png)

    Les détails de la stratégie par défaut sont indiqués à l’écran. Pour créer une stratégie, sélectionnez **Créer** dans le menu déroulant. Le menu déroulant offre également une option permettant de définir l’heure de prise de l’instantané. Pour savoir comment définir une stratégie de sauvegarde, consultez la section [Définition d’une stratégie de sauvegarde](backup-azure-vms-first-look-arm.md#defining-a-backup-policy). Lorsque vous cliquez sur **OK**, la stratégie de sauvegarde est associée à la machine virtuelle.
    
7. Cliquez sur « Activer la sauvegarde » pour configurer la sauvegarde sur la machine virtuelle. Vous déclencherez ainsi le déploiement. 
![Bouton Activer la sauvegarde](./media/backup-azure-vms-first-look-arm/vm-management-blade-enable-backup-button.png)

8. Vous pouvez suivre la progression de la configuration via des notifications. 
![Notification Activer la sauvegarde](./media/backup-azure-vms-first-look-arm/vm-management-blade-enable-backup-notification.png)

9. Une fois le déploiement de la configuration de sauvegarde terminé, cliquez sur l’option « Sauvegarde » dans le panneau de gestion de la machine virtuelle pour accéder au panneau Éléments de sauvegarde correspondant à la machine virtuelle sauvegardée.
![Affichage des éléments de sauvegarde de la machine virtuelle](./media/backup-azure-vms-first-look-arm/backup-item-view.png)

## <a name="configure-backup-from-recovery-services-vault-view"></a>Configurer la sauvegarde à partir de la vue du coffre Recovery Services
Voici globalement les étapes que vous allez suivre.  

1. Créez un coffre Recovery Services pour une machine virtuelle.
2. Utilisez le portail Azure pour sélectionner un scénario, définir la stratégie et identifier les éléments à protéger.
3. Effectuez la sauvegarde initiale.

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Création d’un coffre Recovery Services pour une machine virtuelle
Un coffre Recovery Services est une entité qui stocke l’ensemble des sauvegardes et des points de récupération créés au fil du temps. Le coffre Recovery Services contient également la stratégie de sauvegarde appliquée aux machines virtuelles protégées.

> [!NOTE]
> La sauvegarde de machines virtuelles est un processus local. Vous ne pouvez pas sauvegarder de machines virtuelles d’un emplacement dans un coffre Recovery Services situé à un autre emplacement. Un coffre Recovery Services doit donc exister à chaque emplacement Azure contenant des machines virtuelles à sauvegarder.
>
>

Pour créer un coffre Recovery Services :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu Hub, cliquez sur **Parcourir** et, dans la liste des ressources, tapez **Recovery Services**. Au fur et à mesure de la saisie, la liste est filtrée. Cliquez sur **Coffre Recovery Services**.

    ![Créer un archivage de Recovery Services - Étape 1](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>

    La liste des coffres Recovery Services s’affiche.
3. Dans le menu **Coffres Recovery Services**, cliquez sur **Ajouter**.

    ![Créer un archivage de Recovery Services - Étape 2](./media/backup-azure-vms-first-look-arm/rs-vault-menu.png)

    Le panneau du coffre Recovery Services s’affiche et vous invite à renseigner les champs **Nom**, **Abonnement**, **Groupe de ressources** et **Emplacement**.

    ![Créer un archivage de Recovery Services - Étape 5](./media/backup-azure-vms-first-look-arm/rs-vault-attributes.png)
4. Sous **Nom**, entrez un nom convivial permettant d’identifier le coffre. Le nom doit être unique pour l’abonnement Azure. Tapez un nom contenant entre 2 et 50 caractères. Il doit commencer par une lettre, et ne peut contenir que des lettres, des chiffres et des traits d’union.
5. Cliquez sur **Abonnement** pour afficher la liste des abonnements disponibles. Si vous n’êtes pas sûr de l’abonnement à utiliser, utilisez l’abonnement par défaut (ou suggéré). Vous ne disposez de plusieurs choix que si votre compte professionnel est associé à plusieurs abonnements Azure.
6. Cliquez sur **Groupe de ressources** pour afficher la liste des groupes de ressources disponibles ou sur **Nouveau** pour en créer un. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
7. Cliquez sur **Emplacement** pour sélectionner la région géographique du coffre. Le coffre **doit** se trouver dans la même région que les machines virtuelles que vous souhaitez protéger.

   > [!IMPORTANT]
   > Si vous ne savez pas où se trouve votre machine virtuelle, fermez la boîte de dialogue de création d’archivage et accédez à la liste des machines virtuelles dans le portail. Si vous disposez de machines virtuelles dans plusieurs régions, créez un coffre Recovery Services dans chacune de ces régions. Créez l’archivage dans le premier emplacement avant de passer à l'emplacement suivant. Il est inutile de spécifier des comptes de stockage dans lesquels stocker les données de sauvegarde : le coffre Recovery Services et le service Azure Backup gèrent cela automatiquement.
   >
   >
8. Cliquez sur **Create**. La création du coffre Recovery Services peut prendre un certain temps. Surveillez les notifications d'état dans l'angle supérieur droit du portail. Une fois votre coffre créé, il apparaît dans la liste des coffres Recovery Services.

    ![Liste des archivages de sauvegarde](./media/backup-azure-vms-first-look-arm/rs-list-of-vaults.png)

Maintenant que vous avez créé votre archivage, découvrez comment définir la réplication du stockage.

### <a name="set-storage-replication"></a>Définir la réplication du stockage
L’option de réplication du stockage vous permet de choisir entre stockage géo-redondant et stockage localement redondant. Par défaut, votre archivage utilise un stockage géo-redondant. Si vous utilisez Azure comme sauvegarde principale, laissez cette option inchangée. Choisissez Stockage localement redondant si vous souhaitez une option plus économique, mais moins durable. Pour en savoir plus sur les options de stockage [géo-redondant](../storage/storage-redundancy.md#geo-redundant-storage) et [localement redondant](../storage/storage-redundancy.md#locally-redundant-storage), consultez l’article [Réplication Azure Storage](../storage/storage-redundancy.md).

Pour modifier le paramètre de réplication du stockage :

1. Sélectionnez votre archivage pour ouvrir le tableau de bord associé et le panneau Paramètres. Si le panneau **Paramètres** ne s’ouvre pas, cliquez sur **Tous les paramètres** dans le tableau de bord du coffre.
2. Dans le panneau **Paramètres**, cliquez sur **Infrastructure de sauvegarde** > **Configuration de la sauvegarde** pour ouvrir le panneau **Configuration de la sauvegarde**. Dans le panneau **Configuration de la sauvegarde** , choisissez l’option de réplication du stockage à appliquer à votre coffre.

    ![Liste des archivages de sauvegarde](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    Après avoir sélectionné l’option de stockage pour votre archivage, vous pouvez associer la machine virtuelle à l’archivage. Pour commencer l’association, vous devez découvrir et enregistrer les machines virtuelles Azure.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Sélectionner l’objectif d’une sauvegarde, définir la stratégie et définir les éléments à protéger
Avant d’enregistrer une machine virtuelle dans un coffre, lancez le processus de découverte pour vérifier que les nouvelles machines virtuelles ajoutées à l’abonnement sont bien identifiées. Le processus interroge Azure pour obtenir la liste des machines virtuelles de l’abonnement ainsi que des informations supplémentaires, comme le nom du service cloud et la région. Dans le portail Azure, l’objectif fait référence à ce que vous allez placer dans l’archivage de Recovery Services. La stratégie permet de planifier la fréquence et l’heure de la création des points de récupération. Elle inclut également la durée de rétention de ces derniers.

1. Si l’un de vos coffres Recovery Services est déjà ouvert, passez à l’étape 2. Si vous n’avez aucun coffre Recovery Services ouvert alors que vous vous trouvez dans le portail Azure, cliquez sur **Parcourir**dans le menu Hub.

   * Dans la liste des ressources, tapez **Recovery Services**.
   * Au fur et à mesure de la saisie, la liste est filtrée. Lorsque vous voyez **Coffres Recovery Services**, cliquez dessus.

     ![Créer un archivage de Recovery Services - Étape 1](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>

     La liste des coffres Recovery Services s’affiche.
   * Dans la liste des coffres Recovery Services, sélectionnez un coffre.

     Le tableau de bord de l’archivage sélectionné s'ouvre.

     ![Ouvrir le panneau de l’archivage](./media/backup-azure-vms-first-look-arm/vault-settings.png)
2. Dans le menu du tableau de bord du coffre, cliquez sur **Sauvegarder** pour ouvrir le panneau Sauvegarde.

    ![Ouvrir le panneau Sauvegarde](./media/backup-azure-vms-first-look-arm/backup-button.png)

    Lorsque le panneau s’ouvre, le service Backup recherche les nouvelles machines virtuelles dans l’abonnement.

    ![Détection des machines virtuelles](./media/backup-azure-vms-first-look-arm/discovering-new-vms.png)
3. Dans le panneau Sauvegarde, cliquez sur **Objectif de sauvegarde** pour ouvrir le panneau Objectif de sauvegarde.

    ![Ouvrir le panneau Scénario](./media/backup-azure-vms-first-look-arm/select-backup-goal-one.png)
4. Dans le panneau Objectif de sauvegarde, définissez **Où s’exécute votre charge de travail ?** sur Azure et **Que souhaitez-vous sauvegarder ?** sur Machine virtuelle, puis cliquez sur **OK**.

    Le panneau Backup Goal (Objectif de la sauvegarde) se ferme et le panneau Stratégie de sauvegarde s’ouvre.

    ![Ouvrir le panneau Scénario](./media/backup-azure-vms-first-look-arm/select-backup-goal-two.png)
5. Dans le panneau Stratégie de sauvegarde, sélectionnez la stratégie de sauvegarde à appliquer au coffre, puis cliquez sur **OK**.

    ![Sélectionner la stratégie de sauvegarde](./media/backup-azure-vms-first-look-arm/setting-rs-backup-policy-new.png)

    Les détails de la stratégie par défaut sont indiqués à l’écran. Pour créer une stratégie, sélectionnez **Créer** dans le menu déroulant. Le menu déroulant offre également une option permettant de définir l’heure de prise de l’instantané sur 19 h 00. Pour savoir comment définir une stratégie de sauvegarde, consultez la section [Définition d’une stratégie de sauvegarde](backup-azure-vms-first-look-arm.md#defining-a-backup-policy). Lorsque vous cliquez sur **OK**, la stratégie de sauvegarde est associée au coffre.

    Ensuite, choisissez les machines virtuelles à associer à l'archivage.
6. Sélectionnez les machines virtuelles à associer à la stratégie spécifiée, puis cliquez sur **Sélectionner**.

    ![Sélectionner la charge de travail](./media/backup-azure-vms-first-look-arm/select-vms-to-backup-new.png)

    Si vous ne voyez pas la machine virtuelle souhaitée, vérifiez qu’elle existe dans le même emplacement Azure que l’archivage de Recovery Services.
7. Maintenant que vous avez défini tous les paramètres du coffre, dans le panneau Sauvegarde, cliquez sur **Activer la sauvegarde** en bas de la page. La stratégie est déployée dans l’archivage et les machines virtuelles.

    ![Activer la sauvegarde](./media/backup-azure-vms-first-look-arm/enable-backup-settings-new.png)

## <a name="initial-backup"></a>Sauvegarde initiale
Le déploiement d’une stratégie de sauvegarde sur la machine virtuelle ne signifie pas que les données ont été sauvegardées. Par défaut, la première sauvegarde planifiée (définie dans la stratégie de sauvegarde) est la sauvegarde initiale. Jusqu’à celle-ci, l’état de la dernière sauvegarde dans le panneau **Travaux de sauvegarde** est défini sur **Avertissement (sauvegarde initiale en attente)**.

![Sauvegarde en attente](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

À moins que votre sauvegarde initiale ne soit prévue prochainement, il est recommandé de sélectionner l’option **Sauvegarder maintenant**.

Pour utiliser l’option **Sauvegarder maintenant**:

1. Dans la vignette **Sauvegarde** du tableau de bord du coffre, cliquez sur **Machines virtuelles Azure** <br/>
    ![Icône Paramètres](./media/backup-azure-vms-first-look-arm/rs-vault-in-dashboard-backup-vms.png)

    Le panneau **Éléments de sauvegarde** s’ouvre.
2. Dans le panneau **Éléments de sauvegarde**, cliquez avec le bouton droit sur le coffre à sauvegarder, puis cliquez sur **Sauvegarder maintenant**.

    ![Icône Paramètres](./media/backup-azure-vms-first-look-arm/back-up-now.png)

    Le travail de sauvegarde est déclenché. <br/>

    ![Travail de sauvegarde déclenché](./media/backup-azure-vms-first-look-arm/backup-triggered.png)
3. Pour vérifier si votre sauvegarde initiale est terminée, dans la vignette **Travaux de sauvegarde** du tableau de bord du coffre, cliquez sur **Machines virtuelles Azure**.

    ![Vignette Travaux de sauvegarde](./media/backup-azure-vms-first-look-arm/open-backup-jobs.png)

    Le panneau Travaux de sauvegarde s’ouvre.
4. Le panneau Travaux de sauvegarde indique l’état de tous les travaux.

    ![Vignette Travaux de sauvegarde](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view.png)

   > [!NOTE]
   > Dans le cadre de l’opération de sauvegarde, le service Azure Backup émet une commande vers l’extension de sauvegarde de chaque machine virtuelle pour vider toutes les écritures et prendre un instantané cohérent.
   >
   >

    Lorsque le travail de sauvegarde est terminé, l’état affiché est *Terminé*.

[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Installer l’agent de machine virtuelle sur la machine virtuelle
Ces informations sont fournies en cas de nécessité. L’agent de machine virtuelle Azure doit être installé sur la machine virtuelle Azure pour permettre la prise en charge de l’extension Backup. Cependant, si votre machine virtuelle a été créée à partir de la galerie Azure, l’agent y est déjà installé. L’agent de machine virtuelle n’est pas préinstallé sur les machines virtuelles qui ont été migrées à partir de centres de données locaux. Dans ce cas, il faut l’installer de manière explicite. Si vous rencontrez des problèmes de sauvegarde de la machine virtuelle Azure, vérifiez que son agent est correctement installé sur celle-ci (reportez-vous au tableau ci-dessous). Si vous créez une machine virtuelle personnalisée, [vérifiez que la case **Installer l’agent de machine virtuelle** est bien cochée](../virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) avant de mettre en service la machine virtuelle.

En savoir plus sur l’[agent de machine virtuelle](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) et [comment l’installer](../virtual-machines/virtual-machines-windows-classic-manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Le tableau suivant fournit des informations supplémentaires sur l’agent de machine virtuelle pour les machines virtuelles Windows et Linux.

| **Opération** | **Windows** | **Linux** |
| --- | --- | --- |
| Installation de l’agent de machine virtuelle |<li>Téléchargez et installez le fichier [MSI de l’agent](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Vous avez besoin de privilèges d’administrateur pour terminer l’installation. <li>[Mettez à jour la propriété de la machine virtuelle](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) pour indiquer que l’agent est installé. |<li> Installez l’ [agent Linux](https://github.com/Azure/WALinuxAgent) le plus récent à partir de GitHub. Vous avez besoin de privilèges d’administrateur pour terminer l’installation. <li> [Mettez à jour la propriété de la machine virtuelle](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) pour indiquer que l’agent est installé. |
| Mise à jour de l’agent de machine virtuelle |La mise à jour de l’agent de machine virtuelle est aussi simple que la réinstallation des [fichiers binaires de l’agent de machine virtuelle](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Vérifiez qu’aucune opération de sauvegarde n’est en cours pendant la mise à jour de l’agent de machine virtuelle. |Suivez les instructions fournies dans l’article [Mise à jour d’un agent de machine virtuelle Linux ](../virtual-machines/virtual-machines-linux-update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). <br>Vérifiez qu’aucune opération de sauvegarde n’est en cours pendant la mise à jour de l’agent de machine virtuelle. |
| Validation de l’installation de l’agent de machine virtuelle |<li>Accédez au dossier *C:\WindowsAzure\Packages* sur la machine virtuelle Azure. <li>Le fichier WaAppAgent.exe doit être présent.<li> Cliquez avec le bouton droit sur le fichier, accédez à **Propriétés**, puis sélectionnez l’onglet **Détails**. Le champ Version du produit doit être défini sur 2.6.1198.718 ou une version ultérieure. |N/A |

### <a name="backup-extension"></a>Extension de sauvegarde
Une fois l’agent de machine virtuelle installé sur la machine virtuelle, le service Azure Backup installe l’extension de sauvegarde vers l’agent de machine virtuelle. Le service Azure Backup met à niveau et corrige en toute transparence l'extension de sauvegarde sans intervention supplémentaire de l'utilisateur.

Le service Backup installe l’extension de sauvegarde si machine virtuelle est en cours d’exécution. Une machine virtuelle en cours d’exécution offre le plus de chance d’obtenir un point de récupération d’application cohérent. Toutefois, le service Azure Backup poursuit la sauvegarde de la machine virtuelle, même si elle est éteinte et si l’extension n’a pas été installée, autrement dit si la machine virtuelle est hors connexion. Dans ce cas, le point de récupération sera *cohérent en cas d’incident*.

## <a name="troubleshooting-information"></a>Résolution de problèmes
Si vous rencontrez des problèmes pour accomplir certaines tâches décrites dans cet article, consultez les [instructions pour la résolution des problèmes](backup-azure-vms-troubleshoot.md).

## <a name="pricing"></a>Tarification
La sauvegarde de machine virtuelle Azure sera facturée en fonction du modèle des Instances protégées. En savoir plus sur [tarification de la sauvegarde](https://azure.microsoft.com/pricing/details/backup/)

## <a name="questions"></a>Des questions ?
Si vous avez des questions ou si vous souhaitez que certaines fonctionnalités soient incluses, [envoyez-nous vos commentaires](http://aka.ms/azurebackup_feedback).



<!--HONumber=Nov16_HO2-->


