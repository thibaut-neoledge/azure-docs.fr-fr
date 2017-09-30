---
title: "Sauvegarder et restaurer les machines virtuelles chiffrées à l’aide de Sauvegarde Azure"
description: "Cet article présente l’expérience de sauvegarde et de restauration de machines virtuelles chiffrées à l’aide d’Azure Disk Encryption (ADE)."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 8387f186-7d7b-400a-8fc3-88a85403ea63
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: pajosh;markgal;trinadhk
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 3c0c13aebbf325bc1f03a39a456c8eef1551c64a
ms.contentlocale: fr-fr
ms.lasthandoff: 09/20/2017

---
# <a name="how-to-back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>Guide pratique de sauvegarde et restauration des machines virtuelles chiffrées avec Sauvegarde Azure
Cet article présente les étapes pour sauvegarder et restaurer des machines virtuelles à l’aide de Sauvegarde Azure. Il fournit également des détails sur les scénarios pris en charge, les composants requis et les étapes de dépannage en cas d’erreur.

## <a name="supported-scenarios"></a>Scénarios pris en charge

 * La sauvegarde et la restauration de machines virtuelles chiffrées sont prises en charge uniquement pour les machines virtuelles déployées dans Resource Manager. Ces opérations ne sont pas prises en charge par les machines virtuelles classiques. <br>
 * Il est pris en charge pour les machines virtuelles Windows et Linux utilisant Azure Disk Encryption, qui utilise la fonctionnalité BitLocker standard du secteur de Windows et de la fonctionnalité DM-Crypt de Linux pour proposer le chiffrement des disques. <br>
 * Le tableau suivant indique les scénarios pris en charge par les machines virtuelles chiffrées avec une clé de chiffrement BitLocker (BEK) uniquement et chiffrées avec une clé de chiffrement de clé (KEK).
 
 
   |  | Machines virtuelles avec BEK et KEK | Machines virtuelles avec BEK uniquement |
   | --- | --- | --- |
   | **Machines virtuelles non gérées**  | Oui | Oui  |
   | **Machines virtuelles gérées**  | Oui | Non  |

## <a name="prerequisites"></a>Composants requis
1. Les machines virtuelles ont été chiffrées à l’aide d’[Azure Disk Encryption](../security/azure-security-disk-encryption.md). 
2. Le coffre Recovery Services a été créé et la réplication du stockage a été définie à l’aide des étapes mentionnées dans l’article [Préparation de l’environnement pour la sauvegarde](backup-azure-arm-vms-prepare.md).
3. Des [autorisations d’accès au coffre de clés](#provide-permissions-to-azure-backup) contenant des clés et secrets pour les machines virtuelles chiffrées ont été accordées à Sauvegarde Azure.

## <a name="backup-encrypted-vm"></a>Sauvegarde de machines virtuelles chiffrées
Utilisez les étapes suivantes pour définir l’objectif de sauvegarde, définir une stratégie, configurer les éléments et déclencher une sauvegarde.

### <a name="configure-backup"></a>Configurer une sauvegarde
1. Si l’un de vos coffres Recovery Services est déjà ouvert, passez à l’étape suivante. Si vous n’avez aucun coffre Recovery Services ouvert, mais que vous vous trouvez dans le portail Azure, cliquez sur **Parcourir**dans le menu Hub.

   * Dans la liste des ressources, tapez **Recovery Services**.
   * Au fur et à mesure de la saisie, la liste est filtrée. Lorsque vous voyez **Coffres Recovery Services**, cliquez dessus.

      ![Créer un archivage de Recovery Services - Étape 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     La liste des archivages de Recovery Services s’affiche. Dans la liste des archivages de Recovery Services, sélectionnez un archivage.

     Le tableau de bord de l’archivage sélectionné s'ouvre.
2. Dans la liste d’éléments qui s’affiche sous le coffre, cliquez sur **Sauvegarder** pour démarrer la sauvegarde d’une machine virtuelle chiffrée.

      ![Ouvrir le panneau Sauvegarde](./media/backup-azure-vms-encryption/select-backup.png)
3. Cliquez sur **Objectif de sauvegarde** comme première étape pour sélectionner l’objectif de sauvegarde.

      ![Ouvrir le panneau Scénario](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
4. Dans la première étape de la sélection de l’objectif de sauvegarde, définissez **Où s’exécute votre charge de travail ?** sur Azure et **Que souhaitez-vous sauvegarder ?** sur Machine virtuelle, puis cliquez sur **OK**.

   Cela nous mène à la deuxième étape de la sélection d’une stratégie de sauvegarde.

   ![Ouvrir le panneau Scénario](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
5. Dans la deuxième étape de sélection d’une stratégie, sélectionnez la stratégie de sauvegarde à appliquer au coffre, puis cliquez sur **OK**.

      ![Sélectionner la stratégie de sauvegarde](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    Les détails de la stratégie par défaut sont indiqués à l’écran. Pour créer une stratégie, sélectionnez **Créer** dans le menu déroulant. Lorsque vous cliquez sur **OK**, la stratégie de sauvegarde est associée au coffre.

    Ensuite, choisissez les machines virtuelles à associer à l'archivage.
6. Sélectionnez les machines virtuelles chiffrées à associer à la stratégie spécifiée, puis cliquez sur **OK**.

      ![Sélectionner des machines virtuelles chiffrées](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. Cette page affiche un message sur le coffre de clés associé aux machines virtuelles chiffrées sélectionnées. Le service de sauvegarde requiert l’accès en lecture seule aux clés et clés secrètes dans le coffre de clés. Il utilise ces autorisations pour sauvegarder la clé et la clé secrète, ainsi que les machines virtuelles associées. **Pour que les sauvegardes fonctionnent, vous devez fournir des autorisations d’accès au coffre de clés au service de sauvegarde**. Vous pouvez fournir ces autorisations à l’aide des [étapes mentionnées dans la section ci-dessous](#provide-permissions-to-azure-backup).

      ![Message Machines virtuelles chiffrées](./media/backup-azure-vms-encryption/encrypted-vm-warning-message.png)

      Maintenant que vous avez défini tous les paramètres du coffre, cliquez sur Activer la sauvegarde en bas de la page. L’activation de la sauvegarde déploie la stratégie dans le coffre et les machines virtuelles.
8. La prochaine phase de préparation est l’installation de l’Agent de machine virtuelle ou s’assurer que l’Agent de machine virtuelle est installé. Pour faire de même, utilisez les étapes présentées dans l’article [Préparation de l’environnement pour la sauvegarde](backup-azure-arm-vms-prepare.md).

### <a name="triggering-backup-job"></a>Déclenchement d’un travail de sauvegarde
Utilisez les étapes présentées dans l’article [Sauvegarder des machines virtuelles Azure dans un coffre Recovery Services](backup-azure-arm-vms.md) pour déclencher le travail de sauvegarde.

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>Continuer les sauvegardes des machines virtuelles déjà sauvegardées avec le chiffrement activé  
Si vous avez déjà des machines virtuelles en cours de sauvegarde dans un coffre Recovery Services et si le chiffrement a été activé pour un point ultérieur, vous devez accorder des autorisations au service de sauvegarde pour qu’il puisse accéder au coffre de clés et que les sauvegardes continuent. Vous pouvez fournir ces autorisations dans PowerShell à l’aide des [étapes de la section ci-dessous](#provide-permissions-to-azure-backup) ou des étapes PowerShell présentées dans la section **Activer la sauvegarde** de la [documentation PowerShell](backup-azure-vms-automation.md). 

## <a name="provide-permissions-to-azure-backup"></a>Fournir des autorisations à Sauvegarde Azure
Utilisez les étapes suivantes pour fournir à Sauvegarde Azure des autorisations d’accès appropriées au coffre de clés et lui permettre d’effectuer la sauvegarde des machines virtuelles chiffrées :
1. Sélectionnez **Plus de services** et recherchez **Coffres de clés**.

    ![Rechercher le coffre de clés](./media/backup-azure-vms-encryption/search-key-vault.png)
    
2. Dans la liste des coffres de clés, sélectionnez celui qui est associé aux machines virtuelles chiffrées et qui doit être sauvegardé.

     ![Sélectionner le coffre de clés](./media/backup-azure-vms-encryption/select-key-vault.png)
     
3. Cliquez sur **Stratégies d’accès**, puis sur **Ajouter nouveau**.

    ![Ajouter une stratégie d’accès](./media/backup-azure-vms-encryption/select-key-vault-access-policy.png)
    
4. Cliquez sur **Sélectionner le principal** et tapez **Service de gestion de sauvegarde** dans la barre de recherche. 

    ![Rechercher le service de sauvegarde](./media/backup-azure-vms-encryption/search-backup-service.png)
    
5. Sélectionnez **Service de gestion de sauvegarde** et cliquez sur le bouton Sélectionner.

    ![Sélectionner le service de sauvegarde](./media/backup-azure-vms-encryption/select-backup-service.png)
    
6. Sélectionnez **Sauvegarde Azure** dans la liste déroulante Configurer à partir du modèle. Les autorisations requises sont préremplies dans les listes déroulantes Autorisations de clé et Autorisations du secret. Si votre machine virtuelle est chiffrée à l’aide de **BEK uniquement**, seules les autorisations du secret sont nécessaires, vous devez donc supprimer la sélection pour les autorisations de clé.

    ![Sélectionner Sauvegarde Azure](./media/backup-azure-vms-encryption/select-backup-template.png)
    
7. Cliquez sur **OK**. Notez que le Service de gestion de sauvegarde est ajouté aux stratégies d’accès. 

    ![Stratégie d’accès au service de sauvegarde](./media/backup-azure-vms-encryption/backup-service-access-policy.png)
    
8. Cliquez sur **Enregistrer**. Cela procure les autorisations requises à Sauvegarde Azure.

    ![Stratégie d’accès au service de sauvegarde](./media/backup-azure-vms-encryption/save-access-policy.png)

Une fois les autorisations fournies, vous pouvez activer la sauvegarde pour les machines virtuelles chiffrées.

## <a name="restore-encrypted-vm"></a>Restaurer une machine virtuelle chiffrée
Pour restaurer la machine virtuelle chiffrée, commencez par restaurer les disques en suivant les étapes mentionnées dans la section **Restaurer les disques sauvegardés** dans [Choix de la configuration de restauration de la machine virtuelle](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration). Ensuite, vous pouvez utiliser l’une des options suivantes :
* Suivez les étapes de PowerShell mentionnées dans [Créer une machine virtuelle à partir de disques restaurés](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) pour créer une machine virtuelle complète à partir de disques restaurés.
* OU, suivez les étapes mentionnées dans [Utiliser des modèles pour personnaliser la machine virtuelle de restauration](backup-azure-arm-restore-vms.md#use-templates-to-customize-restore-vm) pour créer des machines virtuelles à partir de disques restaurés. Les modèles peuvent uniquement être utilisés pour les points de restauration créés après le 26 avril 2017.

## <a name="troubleshooting-errors"></a>Résolution des erreurs
| Opération | Détails de l’erreur | Résolution : |
| --- | --- | --- |
|Sauvegarde | Le service de sauvegarde Azure n’a pas les autorisations requises sur Key Vault pour effectuer une sauvegarde des machines virtuelles chiffrées. | La machine virtuelle doit être chiffrée à l’aide de la clé de chiffrement BitLocker et de la clé KEK (Key Encryption Key, clé de chiffrement de clé). Après l’application de ce chiffrement, la sauvegarde doit être activée.  Ces autorisations doivent être accordées au service de sauvegarde à l’aide des [étapes mentionnées dans la section ci-dessus](#provide-permissions-to-azure-backup) ou des étapes PowerShell mentionnées dans la section **Activer la protection** de la documentation PowerShell dans [Utiliser les applets de commande AzureRM.RecoveryServices.Backup pour sauvegarder des machines virtuelles](backup-azure-vms-automation.md#back-up-azure-vms). |  
| Sauvegarde |La validation a échoué, car la machine virtuelle est chiffrée avec une clé BEK uniquement. Les sauvegardes peuvent être activées uniquement pour les machines virtuelles chiffrées aussi bien avec des clés BEK qu’avec des clés KEK. |La machine virtuelle doit être chiffrée simultanément à l’aide de clés BEK et KEK. Déchiffrez d’abord la machine virtuelle et chiffrez-la à l’aide de BEK et KEK. Activez la sauvegarde une fois que la machine virtuelle est chiffrée à l’aide de BEK et KEK. En savoir plus sur la façon dont vous pouvez [déchiffrer et chiffrer la machine virtuelle](../security/azure-security-disk-encryption.md)  |
| Restauration |Vous ne pouvez pas restaurer cette machine virtuelle chiffrée, car le coffre de clés associé à cette machine virtuelle n’existe pas. |Pour créer un coffre de clés, voir [Prise en main d’Azure Key Vault](../key-vault/key-vault-get-started.md). Consultez [Restaurer la clé et le secret de coffre de clés à l’aide de Sauvegarde Azure](backup-azure-restore-key-secret.md) pour restaurer la clé et la clé secrète si celles-ci n’existent pas. |
| Restauration |Vous ne pouvez pas restaurer cette machine virtuelle chiffrée, car la clé et la clé secrète associées n’existent pas. |Consultez [Restaurer la clé et le secret de coffre de clés à l’aide de Sauvegarde Azure](backup-azure-restore-key-secret.md) pour restaurer la clé et la clé secrète si celles-ci n’existent pas. |
| Restauration |Le service de sauvegarde n’a pas l’autorisation d’accéder aux ressources dans votre abonnement. |Comme indiqué ci-dessus, commencez par restaurer les disques en suivant les étapes mentionnées dans la section **Restaurer les disques sauvegardés** dans [Choix de la configuration de restauration de la machine virtuelle](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration). Ensuite, utilisez PowerShell pour [Créer une machine virtuelle à partir de disques restaurés](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). |


