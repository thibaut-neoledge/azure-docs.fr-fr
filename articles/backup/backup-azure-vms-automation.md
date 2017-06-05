---
title: "Déploiement et gestion des sauvegardes de machines virtuelles déployées avec le modèle Resource Manager à l’aide de PowerShell | Microsoft Docs"
description: "Utilisation de PowerShell pour déployer et gérer des sauvegardes dans Azure pour les machines virtuelles déployées avec le modèle Resource Manager"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 68606e4f-536d-4eac-9f80-8a198ea94d52
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/25/2017
ms.author: markgal;trinadhk
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: dcbcdf6f81d2acc9d515eca6915ac783b082d5b0
ms.contentlocale: fr-fr
ms.lasthandoff: 05/05/2017


---
# <a name="use-azurermrecoveryservicesbackup-cmdlets-to-back-up-virtual-machines"></a>Utilisez les applets de commande AzureRM.RecoveryServices.Backup pour sauvegarder des machines virtuelles
> [!div class="op_single_selector"]
> * [Gestionnaire de ressources](backup-azure-vms-automation.md)
> * [Classique](backup-azure-vms-classic-automation.md)
>
>

Cet article vous montre comment utiliser des applets de commande Azure PowerShell pour sauvegarder et restaurer une machine virtuelle Azure à partir d’un coffre Recovery Services. Un coffre Recovery Services est une ressource Azure Resource Manager utilisée pour protéger les données et les actifs dans Azure Backup et Azure Site Recovery Services. Vous pouvez utiliser un coffre Recovery Services pour protéger des machines virtuelles déployées avec Azure Service Manager, ainsi que des machines virtuelles déployées avec le modèle Azure Resource Manager.

> [!NOTE]
> Azure dispose de deux modèles de déploiement pour créer et utiliser des ressources : [Azure Resource Manager et Azure Classic](../azure-resource-manager/resource-manager-deployment-model.md). Cet article concerne l’utilisation avec des machines virtuelles créées à l’aide du modèle Resource Manager.
>
>

Cet article vous guide tout au long de l’utilisation de PowerShell pour protéger une machine virtuelle et la restauration de données à partir d’un point de récupération.

## <a name="concepts"></a>Concepts
Si vous n’êtes pas familiarisé avec le service de sauvegarde Azure, consultez [Qu’est-ce que la Sauvegarde Azure ?](backup-introduction-to-azure-backup.md) pour obtenir une vue d’ensemble du service. Avant de commencer, assurez-vous de connaître les conditions préalables de base nécessaires pour travailler avec Azure Backup et les limitations de la solution actuelle de sauvegarde de la machines virtuelles.

Pour pouvoir utiliser efficacement PowerShell, il est nécessaire de comprendre la hiérarchie des objets et par où commencer.

![Hiérarchie des objets dans Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Pour voir les informations de référence sur l’applet de commande PowerShell AzureRmRecoveryServicesBackup, consultez [Sauvegarde Azure - Applets de commande de Recovery Services](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) dans la bibliothèque Azure.

## <a name="setup-and-registration"></a>Installation et inscription
Pour commencer :

1. [Téléchargez la dernière version de PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (version minimale requise : 1.4.0)
2. Rechercher les applets de commande PowerShell Azure Backup disponibles en tapant la commande suivante :

```
PS C:\> Get-Command *azurermrecoveryservices*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.4.0      AzureRM.RecoveryServices
Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          New-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Remove-AzureRmRecoveryServicesVault                1.4.0      AzureRM.RecoveryServices
Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.4.0      AzureRM.RecoveryServices
Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
```


Les tâches ci-après peuvent être automatisées avec PowerShell :

* Créer un coffre Recovery Services
* Sauvegarder des machines virtuelles Azure
* Déclencher une tâche de sauvegarde
* Surveiller une tâche de sauvegarde
* Restauration d’une machine virtuelle Azure

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services
Les étapes suivantes vous montrent comment créer un coffre Recovery Services. Un coffre Recovery Services diffère d’un coffre Backup.

1. Si vous utilisez Sauvegarde Azure pour la première fois, vous devez utiliser l’applet de commande **[Register-AzureRMResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** pour inscrire le fournisseur Azure Recovery Service auprès de votre abonnement.

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. Le coffre Recovery Services étant une ressource Resource Manager, vous devez le placer dans un groupe de ressources. Si vous ne disposez pas d’un groupe de ressources, créez-en un avec la cmdlet **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)**. Quand vous créez un groupe de ressources, spécifiez son nom et son emplacement.  

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
    ```
3. Utilisez l’applet de commande **[New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)** pour créer un coffre Recovery Services. Spécifiez pour le coffre le même emplacement que pour le groupe de ressources.

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```
4. Spécifiez le type de redondance de stockage à utiliser : [Stockage localement redondant (LRS)](../storage/storage-redundancy.md#locally-redundant-storage) ou [Stockage géo-redondant (GRS)](../storage/storage-redundancy.md#geo-redundant-storage). L’exemple suivant montre que l’option -BackupStorageRedundancy pour testvault est définie sur GeoRedundant.

    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testvault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > De nombreuses applets de commande Azure Backup nécessitent l’objet coffre Recovery Services en tant qu’entrée. Pour cette raison, il est pratique de stocker l’objet coffre Backup Recovery Services dans une variable.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Afficher les coffres dans un abonnement
Utilisez **[Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)** pour afficher la liste de tous les coffres dans l’abonnement actuel. Vous pouvez utiliser cette commande pour vérifier qu’un coffre a été créé ou pour voir les coffres disponibles dans l’abonnement.

Exécutez la commande Get-AzureRmRecoveryServicesVault pour afficher tous les coffres de l’abonnement. L’exemple suivant montre les informations affichées pour chaque coffre.

```
PS C:\> Get-AzureRmRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>Sauvegarder des machines virtuelles Azure
Utilisez un coffre Recovery Services pour protéger vos machines virtuelles. Avant d’appliquer la protection, définissez le contexte du coffre (le type de données qu’il protège) et vérifiez la stratégie de protection. La stratégie de protection consiste à planifier l’exécution du travail de sauvegarde et la durée de conservation de chaque instantané de sauvegarde.

### <a name="set-vault-context"></a>Définir le contexte du coffre
Avant d’activer la protection sur une machine virtuelle, utilisez  **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)**  pour définir le contexte du coffre. Une fois le contexte du coffre défini, il s’applique à toutes les applets de commande suivantes. L’exemple suivant définit le contexte du coffre pour le coffre *testvault*.

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Création d’une stratégie de protection
Quand vous créez un coffre Recovery Services, il est fourni avec des stratégies de protection et de conservation par défaut. La stratégie de protection par défaut déclenche une tâche de sauvegarde chaque jour à une heure spécifiée. La stratégie de conservation par défaut conserve le point de récupération quotidien pendant 30 jours. Vous pouvez utiliser la stratégie par défaut pour protéger rapidement votre machine virtuelle et modifier la stratégie ultérieurement avec différents détails.

Utilisez **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)** pour afficher les stratégies de protection du coffre. Vous pouvez utiliser cette applet de commande pour obtenir une stratégie spécifique ou pour afficher les stratégies associées à un type de charge de travail. L’exemple suivant obtient les stratégies pour le type de charge de travail AzureVM.

```
PS C:\> Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Le fuseau horaire du champ BackupTime dans PowerShell est UTC. Toutefois, lorsque l’heure de sauvegarde s’affiche dans le portail Azure, elle est alignée sur votre fuseau horaire.
>
>

Une stratégie de protection de la sauvegarde est associée à au moins une stratégie de rétention. La stratégie de conservation définit la durée de conservation d’un point de restauration avant sa suppression. Utilisez **[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)** pour afficher la stratégie de conservation par défaut.  De même, vous pouvez utiliser **[Get-AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)** pour obtenir la stratégie de planification par défaut. L’applet de commande **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** crée un objet PowerShell contenant des informations sur la stratégie de sauvegarde. Les objets de la stratégie de planification et de conservation sont utilisés comme entrées pour l’applet de commande **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)**. L’exemple suivant stocke la stratégie de planification et la stratégie de conservation dans des variables. L’exemple utilise ces variables pour définir les paramètres lors de la création d’une stratégie de protection, *NewPolicy*.

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```


### <a name="enable-protection"></a>Activer la protection
Une fois que vous avez défini la stratégie de protection de la sauvegarde, vous devez encore activer la stratégie pour un élément. Utilisez  **[Enable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)**  pour activer la protection. L’activation de la protection nécessite deux objets : l’élément et la stratégie. Une fois que la stratégie a été associée au coffre, le flux de travail de la sauvegarde est déclenché à l’heure planifiée dans la planification de la stratégie.

L’exemple suivant active la protection pour l’élément V2VM avec la stratégie NewPolicy. Pour activer la protection sur des machines virtuelles Resource Manager non chiffrées

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Pour activer la protection sur des machines virtuelles chiffrées (à l’aide de clés BEK et KEK), vous devez accorder au service Sauvegarde Azure l’autorisation de lire les clés et les secrets dans le coffre de clés.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Si vous êtes sur le cloud Azure Government, utilisez la valeur ff281ffe-705c-4f53-9f37-a40e6f2c68f3 pour le paramètre **- ServicePrincipalName** dans l’applet de commande [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy).
>
>

Pour les machines virtuelles Classic

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>Modifier une stratégie de protection
Pour modifier la stratégie de protection, utilisez [Set-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy) pour modifier les objets SchedulePolicy ou RetentionPolicy.

L’exemple suivant change la conservation des points de récupération en 365 jours.

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>Déclencher une sauvegarde
Vous pouvez utiliser  **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** pour déclencher une tâche de sauvegarde. S’il s’agit de la sauvegarde initiale, c’est une sauvegarde complète. Les sauvegardes suivantes prennent une copie incrémentielle. Veillez à utiliser  **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)**  pour définir le contexte du coffre avant de déclencher la tâche de sauvegarde. L’exemple suivant suppose que la valeur du contexte du coffre a été définie.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup               InProgress            4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Le fuseau horaire des champs StartTime et EndTime affichés dans PowerShell est UTC. Toutefois, lorsque l’heure s’affiche dans le portail Azure, elle est alignée sur votre fuseau horaire.
>
>

## <a name="monitoring-a-backup-job"></a>Surveillance d’une tâche de sauvegarde
Vous pouvez surveiller les opérations à exécution longue, comme les tâches de sauvegarde, sans utiliser le portail Azure. Pour obtenir l’état d’une tâche en cours d’exécution, utilisez l’applet de commande **[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)**. Cette applet de commande obtient les tâches de sauvegarde pour un coffre spécifique, et ce coffre est spécifié dans le contexte du coffre. L’exemple suivant obtient l’état d’une tâche en cours d’exécution sous forme de tableau et stocke l’état dans la variable $joblist.

```
PS C:\> $joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
PS C:\> $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Au lieu d’interroger ces travaux pour connaître leur état d’avancement, ce qui implique du code supplémentaire inutile, utilisez l’applet de commande **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)**. Cette applet de commande suspend l’exécution jusqu’à la fin de la tâche ou jusqu’à ce que la valeur spécifiée pour l’expiration du délai soit atteinte.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Restauration d’une machine virtuelle Azure
Il existe une différence clé entre la restauration d’une machine virtuelle à l’aide du portail Azure et la restauration d’une machine virtuelle à l’aide de PowerShell. Avec PowerShell, l’opération de restauration est terminée dès que sont créés les disques et les informations de configuration à partir d’un point de restauration.

> [!NOTE]
> L’opération de restauration ne crée pas de machine virtuelle.
>
>

Pour créer une machine virtuelle à partir d’un disque, consultez la section [Créer la machine virtuelle à partir de disques stockés](backup-azure-vms-automation.md#create-a-vm-from-stored-disks). Les étapes de base pour restaurer une machine virtuelle Azure sont :

* Sélection de la machine virtuelle
* Choisir un point de récupération
* Restaurer les disques
* Créer la machine virtuelle à partir des disques stockés

L’illustration suivante montre la hiérarchie d’objets à partir de RecoveryServicesVault jusqu’à BackupRecoveryPoint.

![Hiérarchie d’objets Recovery Services montrant BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Pour restaurer les données de sauvegarde, identifiez l’élément sauvegardé et le point de récupération contenant les données ponctuelles. Utilisez l’applet de commande **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** pour restaurer les données à partir du coffre vers le compte du client.

### <a name="select-the-vm"></a>Sélection de la machine virtuelle
Pour obtenir l’objet PowerShell permettant d’identifier l’élément à restaurer, vous devez commencer au niveau du conteneur dans le coffre et descendre dans la hiérarchie d’objets. Pour sélectionner le conteneur qui représente la machine virtuelle, utilisez l’applet de commande **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** et dirigez-la vers l’applet de commande **[Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)**.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" –Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem –Container $namedContainer  –WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Choisir un point de récupération
Utilisez l’applet de commande **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** pour répertorier tous les points de récupération pour l’élément de sauvegarde. Ensuite, choisissez le point de récupération à restaurer. Si vous ne savez pas quel point de récupération utiliser, nous vous conseillons de choisir le point RecoveryPointType = AppConsistent le plus récent dans la liste.

Dans le script suivant, la variable **$rp**est un tableau de points de récupération des 7 derniers jours pour l’élément de sauvegarde sélectionné. Le tableau est trié dans l’ordre chronologique inverse, le point de récupération le plus récent détenant l’index 0. Utilisez l'indexation de tableau PowerShell standard pour sélectionner le point de récupération. Dans l’exemple, $rp[0] sélectionne le dernier point de récupération.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```



### <a name="restore-the-disks"></a>Restaurer les disques
Utilisez l’applet de commande **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** pour restaurer les données et la configuration d’un élément de sauvegarde à un point de récupération. Lorsque vous avez identifié un point de récupération, utilisez-le comme valeur du paramètre **-RecoveryPoint** . Dans l’exemple de code précédent, **$rp[0]** était le point de récupération à utiliser. Dans l’exemple de code suivant, **$rp [0]** est le point de récupération à utiliser pour restaurer le disque.

Pour restaurer les disques et les informations de configuration :

```
PS C:\> $restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
PS C:\> $restorejob
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Utilisez l’applet de commande **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** pour attendre que la tâche de restauration soit terminée.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Une fois le travail de restauration terminé, utilisez l’applet de commande **[Get-AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)** pour obtenir les détails du travail de restauration. La propriété JobDetails contient les informations nécessaires pour régénérer la machine virtuelle.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

Une fois que vous avez restauré les disques, accédez à la section suivante pour créer la machine virtuelle.

## <a name="create-a-vm-from-restored-disks"></a>Créer une machine virtuelle à partir de disques restaurés
Après avoir restauré les disques, exécutez les étapes ci-après pour créer et configurer la machine virtuelle à partir du disque.

> [!NOTE]
> Pour créer des machines virtuelles chiffrées à partir de disques restaurés, votre rôle Azure doit avoir l’autorisation d’effectuer l’action **Microsoft.KeyVault/vaults/deploy/action**. Si votre rôle ne dispose pas de cette autorisation, créez un rôle personnalisé avec cette action. Pour plus d’informations, consultez [Rôles personnalisés dans le contrôle d’accès en fonction du rôle (RBAC) Azure](../active-directory/role-based-access-control-custom-roles.md).
>
>

1. Interrogez les propriétés des disques restaurés pour obtenir les détails du travail.

  ```
  PS C:\> $properties = $details.properties
  PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
  PS C:\> $containerName = $properties["Config Blob Container Name"]
  PS C:\> $blobName = $properties["Config Blob Name"]
  ```

2. Définissez le contexte de stockage Azure et restaurez le fichier de configuration JSON.

    ```
    PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
    PS C:\> $destination_path = "C:\vmconfig.json"
    PS C:\> Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path
    PS C:\> $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
    ```

3. Utilisez le fichier de configuration JSON pour créer la configuration de la machine virtuelle.

    ```
   PS C:\> $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
    ```

4. Attachez le disque du système d’exploitation et les disques de données.

  Utilisez l’exemple suivant pour une machine virtuelle non gérée et non chiffrée

    ```
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
    PS C:\> foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

  Pour les machines virtuelles non gérées et chiffrées, vous devez restaurer la clé et le secret dans le coffre de clés avant d’attacher des disques. Pour plus d’informations, consultez l’article [Restaurer une machine virtuelle chiffrée à partir d’un point de récupération Sauvegarde Azure](backup-azure-restore-key-secret.md). L’exemple suivant montre comment attacher des disques de système d’exploitation et de données pour les machines virtuelles chiffrées.

    ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

  Pour les machines virtuelles chiffrées et gérées, vous devez créer des disques managés depuis Stockage Blob, puis attacher les disques. Pour plus d’informations, consultez l’article [Attacher un disque de données à une machine virtuelle Windows à l’aide de PowerShell](../virtual-machines/windows/attach-disk-ps.md). L’exemple de code suivant montre comment attacher les disques de données pour des machines virtuelles chiffrées et gérées.

    ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
        $dataDiskName = $vm.Name + $dd.name ;
        $dataVhdUri = $dd.vhd.uri ;
        $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
        $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
        Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
      }
    ```

5. Définissez les paramètres réseau.

    ```
    PS C:\> $nicName="p1234"
    PS C:\> $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    PS C:\> $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    PS C:\> $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    PS C:\> $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```
6. Créez la machine virtuelle.

    ```    
    PS C:\> New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="next-steps"></a>Étapes suivantes
Si vous préférez utiliser PowerShell pour gérer vos ressources Azure, consultez l’article PowerShell [Déployer et gérer une sauvegarde pour Windows Server](backup-client-automation.md). Si vous gérez des sauvegardes DPM, consultez l’article [Déployer et gérer la sauvegarde pour DPM](backup-dpm-automation.md). Ces deux articles ont une version concernant les déploiements avec le modèle Resource Manager et le modèle Classic.  

