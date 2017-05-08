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
ms.date: 04/24/2017
ms.author: pajosh;markgal;trinadhk
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: da6c497e10d31dbc98703090a8f7699d0841faa8
ms.lasthandoff: 04/26/2017


---
# <a name="how-to-back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>Guide pratique de sauvegarde et restauration des machines virtuelles chiffrées avec Azure Backup
Cet article présente les étapes pour sauvegarder et restaurer des machines virtuelles à l’aide de Sauvegarde Azure. Il fournit également des détails sur les scénarios pris en charge, les composants requis et les étapes de dépannage en cas d’erreur.

## <a name="supported-scenarios"></a>Scénarios pris en charge
> [!NOTE]
> 1. La sauvegarde et la restauration de machines virtuelles chiffrées sont prises en charge uniquement pour les machines virtuelles déployées dans Resource Manager. Ces opérations ne sont pas prises en charge par les machines virtuelles classiques. <br>
> 2. Il est pris en charge pour les machines virtuelles Windows et Linux utilisant Azure Disk Encryption, qui tire parti de la fonctionnalité BitLocker standard du secteur de Windows et de la fonctionnalité DM-Crypt de Linux pour proposer le chiffrement des disques. <br>
> 3. Elles sont prises en charge uniquement pour les machines virtuelles chiffrées en même temps à l’aide de la clé de chiffrement BitLocker (BEK, BitLocker Encryption Key) et de la clé KEK (Key Encryption Key, clé de chiffrement de clé). Elles ne sont pas prises en charge pour les machines virtuelles chiffrées à l’aide de la clé de chiffrement BitLocker uniquement. <br>
>
>

## <a name="pre-requisites"></a>Conditions préalables
1. Les machines virtuelles ont été chiffrées à l’aide d’[Azure Disk Encryption](../security/azure-security-disk-encryption.md). Elles doivent être chiffrées en même temps à l’aide de la clé de chiffrement BitLocker et de la clé KEK (Key Encryption Key, clé de chiffrement de clé).
2. Le coffre Recovery Services a été créé et la réplication du stockage a été définie à l’aide des étapes mentionnées dans l’article [Préparation de l’environnement pour la sauvegarde](backup-azure-arm-vms-prepare.md).

## <a name="backup-encrypted-vm"></a>Sauvegarde de machines virtuelles chiffrées
Utilisez les étapes suivantes pour définir l’objectif de sauvegarde, définir une stratégie, configurer les éléments et déclencher une sauvegarde.

### <a name="configure-backup"></a>Configurer une sauvegarde
1. Si l’un de vos coffres Recovery Services est déjà ouvert, passez à l’étape suivante. Si vous n’avez aucun coffre Recovery Services ouvert, mais que vous vous trouvez dans le portail Azure, cliquez sur **Parcourir**dans le menu Hub.

   * Dans la liste des ressources, tapez **Recovery Services**.
   * Au fur et à mesure de la saisie, la liste est filtrée. Lorsque vous voyez **Coffres Recovery Services**, cliquez dessus.

      ![Créer un archivage de Recovery Services - Étape 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     La liste des archivages de Recovery Services s’affiche. Dans la liste des archivages de Recovery Services, sélectionnez un archivage.

     Le tableau de bord de l’archivage sélectionné s'ouvre.
2. Dans la liste d’éléments qui s’affiche sous le coffre, cliquez sur **Sauvegarder** pour ouvrir le panneau Sauvegarde.

      ![Ouvrir le panneau Sauvegarde](./media/backup-azure-vms-encryption/select-backup.png)
3. Dans le panneau Sauvegarde, cliquez sur **Objectif de sauvegarde** pour ouvrir le panneau Objectif de sauvegarde.

      ![Ouvrir le panneau Scénario](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
4. Dans le panneau Objectif de sauvegarde, définissez **Où s’exécute votre charge de travail ?** sur Azure et **Que souhaitez-vous sauvegarder ?** sur Machine virtuelle, puis cliquez sur **OK**.

   Le panneau Backup Goal (Objectif de la sauvegarde) se ferme et le panneau Stratégie de sauvegarde s’ouvre.

   ![Ouvrir le panneau Scénario](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
5. Dans le panneau Stratégie de sauvegarde, sélectionnez la stratégie de sauvegarde à appliquer au coffre, puis cliquez sur **OK**.

      ![Sélectionner la stratégie de sauvegarde](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    Les détails de la stratégie par défaut sont indiqués à l’écran. Pour créer une stratégie, sélectionnez **Créer** dans le menu déroulant. Lorsque vous cliquez sur **OK**, la stratégie de sauvegarde est associée au coffre.

    Ensuite, choisissez les machines virtuelles à associer à l'archivage.
6. Sélectionnez les machines virtuelles chiffrées à associer à la stratégie spécifiée, puis cliquez sur **OK**.

      ![Sélectionner des machines virtuelles chiffrées](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. Cette page affiche un message sur le coffre de clés associé aux machines virtuelles chiffrées sélectionnées. Le service de sauvegarde requiert l’accès en lecture seule aux clés et clés secrètes dans le coffre de clés. Il utilise ces autorisations pour sauvegarder la clé et la clé secrète, ainsi que les machines virtuelles associées.

      ![Message Machines virtuelles chiffrées](./media/backup-azure-vms-encryption/encrypted-vm-message.png)

      Maintenant que vous avez défini tous les paramètres du coffre, dans le panneau Sauvegarde, cliquez sur Activer la sauvegarde en bas de la page. Le fait d’activer la sauvegarde déploie la stratégie dans le coffre et les machines virtuelles.
8. La prochaine phase de préparation est l’installation de l’Agent de machine virtuelle ou s’assurer que l’Agent de machine virtuelle est installé. Pour faire de même, utilisez les étapes présentées dans l’article [Préparation de l’environnement pour la sauvegarde](backup-azure-arm-vms-prepare.md).

### <a name="triggering-backup-job"></a>Déclenchement d’un travail de sauvegarde
Utilisez les étapes présentées dans l’article [Sauvegarder des machines virtuelles Azure dans un coffre Recovery Services](backup-azure-arm-vms.md) pour déclencher le travail de sauvegarde.

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>Continuer les sauvegardes des machines virtuelles déjà sauvegardées avec le chiffrement activé  
Si vous avez déjà des machines virtuelles en cours de sauvegarde dans un coffre Recovery Services et si le chiffrement a été activé pour un point ultérieur, vous devez accorder des autorisations au service de sauvegarde pour qu’il puisse accéder au coffre de clés et que les sauvegardes continuent. Vous pouvez fournir ces autorisations dans PowerShell à l’aide des étapes présentées dans la section **Sauvegarde des machines virtuelles Azure** de la [documentation PowerShell](backup-azure-vms-automation.md#backup-azure-vms). 

## <a name="restore-encrypted-vm"></a>Restaurer une machine virtuelle chiffrée
Pour restaurer la machine virtuelle chiffrée, commencez par restaurer les disques en suivant les étapes mentionnées dans la section **Restore backed up disks** (Restaurer les disques sauvegardés) dans [Choosing VM restore configuration](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration) (Choisir la configuration de restauration de la machine virtuelle). Ensuite, vous pouvez utiliser l’une des options suivantes :
* Suivez les étapes de PowerShell mentionnées dans [Créer une machine virtuelle à partir de disques restaurés](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) pour créer une machine virtuelle complète à partir de disques restaurés. 
* OU, suivez les étapes mentionnées dans [Utiliser des modèles pour personnaliser la machine virtuelle de restauration](backup-azure-arm-restore-vms.md#use-templates-to-customize-restore-vm) pour créer des machines virtuelles à partir de disques restaurés. Les modèles peuvent uniquement être utilisés pour les points de restauration créés après le 26 avril 2017.

## <a name="troubleshooting-errors"></a>Résolution des erreurs
| Opération | Détails de l’erreur | Résolution : |
| --- | --- | --- |
| Sauvegarde |La validation a échoué, car la machine virtuelle est chiffrée avec une clé BEK uniquement. Les sauvegardes peuvent être activées uniquement pour les machines virtuelles chiffrées aussi bien avec des clés BEK qu’avec des clés KEK. |La machine virtuelle doit être chiffrée simultanément à l’aide de clés BEK et KEK. Déchiffrez d’abord la machine virtuelle et chiffrez-la à l’aide de BEK et KEK. Activez la sauvegarde une fois que la machine virtuelle est chiffrée à l’aide de BEK et KEK. En savoir plus sur la façon dont vous pouvez [déchiffrer et chiffrer la machine virtuelle](../security/azure-security-disk-encryption.md)  |
| Restauration |Vous ne pouvez pas restaurer cette machine virtuelle chiffrée, car le coffre de clés associé à cette machine virtuelle n’existe pas. |Pour créer un coffre de clés, voir [Prise en main d’Azure Key Vault](../key-vault/key-vault-get-started.md). Consultez [Restore key vault key and secret using Azure Backup](backup-azure-restore-key-secret.md) (Restaurer la clé et le secret de coffre de clés à l’aide de Sauvegarde Azure) pour restaurer la clé et la clé secrète si celles-ci n’existent pas. |
| Restauration |Vous ne pouvez pas restaurer cette machine virtuelle chiffrée, car la clé et la clé secrète associées n’existent pas. |Consultez [Restore key vault key and secret using Azure Backup](backup-azure-restore-key-secret.md) (Restaurer la clé et le secret de coffre de clés à l’aide de Sauvegarde Azure) pour restaurer la clé et la clé secrète si celles-ci n’existent pas. |
| Restauration |Le service de sauvegarde n’a pas l’autorisation d’accéder aux ressources dans votre abonnement. |Comme indiqué ci-dessus, commencez par restaurer les disques en suivant les étapes mentionnées dans la section **Restore backed up disks** (Restaurer les disques sauvegardés) dans [Choosing VM restore configuration](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration) (Choisir la configuration de restauration de la machine virtuelle). Ensuite, utilisez PowerShell pour [Créer une machine virtuelle à partir de disques restaurés](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

