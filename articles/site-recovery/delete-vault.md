---
title: Supprimer un coffre Recovery Services
service: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/04/2017
ms.author: rajani-janaki-ram
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 32fcab0c9e4665d07691dc3792bdee90fb01fe66
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017


---
# <a name="delete-recovery-services-vault"></a>Supprimer un coffre Recovery Services
Les dépendances vous empêchent de supprimer le coffre Recovery Services, et les actions que vous devez prendre varient en fonction du type de scénario Azure Site Recovery : VMWare vers Azure, Hyper-V (avec et sans VMM) vers Azure, et Sauvegarde Azure. Pour supprimer un coffre utilisé dans Sauvegarde Azure, cliquez sur [ce](../backup/backup-azure-delete-vault.md) lien.

>[!Important]
>Si vous testez le produit et souhaitez supprimer rapidement le coffre, et si n’êtes pas préoccupé par la perte de données, vous pouvez utiliser la méthode de suppression par la force pour supprimer le coffre et toutes ses dépendances.

> La commande PowerShell supprime tout le contenu du coffre, et cette étape est irréversible

## <a name="force-delete-vault-using-powershell"></a>Forcer la suppression du coffre à l’aide de PowerShell

Suivez les étapes ci-dessous pour supprimer le coffre Site Recovery même s’il contient des éléments protégés

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmSiteRecoveryVault -Name "vaultname"

    Remove-AzureRmSiteRecoveryVault -Vault $vault



Suivez les étapes recommandées (dans l’ordre indiqué) pour que votre scénario supprime le coffre

## <a name="delete-vault-used-in-site-recovery-for-protecting-vmware-vms-to-azure"></a>Supprimez le coffre, utilisé en mode Site Recovery pour protéger les machines virtuelles VMWare vers Azure :
1. Vérifiez que toutes les machines virtuelles protégées sont supprimées. Pour ce faire, cliquez [ici](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).
2.  Vérifiez que toutes les stratégies de réplication sont supprimées. Pour ce faire, cliquez [ici](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).
3.  Vérifiez que les références à vCenter sont supprimées. Pour ce faire, cliquez [ici](site-recovery-vmware-to-azure-manage-vCenter.md##delete-a-vcenter-in-azure-site-recovery).
4. Vérifiez que le serveur de configuration est supprimé. Pour ce faire, cliquez [ici](site-recovery-vmware-to-azure-manage-configuration-server.md##decommissioning-a-configuration-server).
5. Essayez à présent de supprimer le coffre.


## <a name="delete-vault-used-in-site-recovery-for-protecting-hyper-v-vms-with-vmm-to-azure"></a>Supprimez le coffre, utilisé en mode Site Recovery pour protéger les machines virtuelles Hyper-V (avec VMM) vers Azure :
1.  Vérifiez que toutes les machines virtuelles protégées sont supprimées. Pour ce faire, cliquez [ici](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).
- Vérifiez que toutes les stratégies de réplication sont supprimées. Pour ce faire, cliquez [ici](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).
-   Supprimez les références aux serveurs VMM. Pour ce faire, cliquez [ici](site-recovery-manage-registration-and-protection.md##unregister-a-connected-vmm-server).
-   Essayez à présent de supprimer le coffre.

## <a name="delete-vault-used-in-site-recovery--for-protecting-hyper-v-vms-without-vmm-to-azure"></a>Supprimez le coffre, utilisé en mode Site Recovery pour protéger les machines virtuelles Hyper-V (sans VMM) vers Azure :
1. Vérifiez que toutes les machines virtuelles protégées sont supprimées. Pour ce faire, cliquez [ici](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).
- Vérifiez que toutes les stratégies de réplication sont supprimées. Pour ce faire, cliquez [ici](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).
-   Supprimez les références aux serveurs Hyper-V. Pour ce faire, cliquez [ici](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site).
-   Supprimez le site Hyper-V.
-   Essayez à présent de supprimer le coffre.

