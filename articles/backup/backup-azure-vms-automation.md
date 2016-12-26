---
title: "Déploiement et gestion des sauvegardes de machines virtuelles déployées avec le modèle Resource Manager à l’aide de PowerShell | Microsoft Docs"
description: "Utilisation de PowerShell pour déployer et gérer des sauvegardes dans Azure pour les machines virtuelles déployées avec le modèle Resource Manager"
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
editor: 
ms.assetid: 68606e4f-536d-4eac-9f80-8a198ea94d52
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/01/2016
ms.author: markgal; trinadhk
translationtype: Human Translation
ms.sourcegitcommit: f6a1346b84521806e5523e331b2aeb11648bbe6d
ms.openlocfilehash: a7d2a73760cd015a5a67551f6f6ada8568ed75b8


---
# <a name="deploy-and-manage-backups-for-resource-manager-deployed-vms-using-powershell"></a>Déploiement et gestion des sauvegardes pour les machines virtuelles déployées avec le modèle Resource Manager à l’aide de PowerShell
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

Pour pouvoir utiliser efficacement PowerShell, il est nécessaire de comprendre la hiérarchie d’objets et par où commencer.

![Hiérarchie des objets dans Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Pour afficher les informations de référence sur l’applet de commande PowerShell AzureRmRecoveryServicesBackup, consultez [Azure Backup - Recovery Services Cmdlets](https://msdn.microsoft.com/library/mt723320.aspx) (Azure Backup - Applets de commande Recovery Services) dans la bibliothèque Azure.
Pour afficher les informations de référence sur l’applet de commande PowerShell AzureRmRecoveryServicesVault, consultez [Azure Recovery Service Cmdlets](https://msdn.microsoft.com/library/mt643905.aspx)(Applets de commande Azure Recovery Services).

## <a name="setup-and-registration"></a>Installation et inscription
Pour commencer :

1. [Téléchargez la dernière version de PowerShell](https://github.com/Azure/azure-powershell/releases) (version minimale requise : 1.4.0)
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
* Sauvegarde ou protection de machines virtuelles Azure
* Déclencher une tâche de sauvegarde
* Surveiller une tâche de sauvegarde
* Restauration d’une machine virtuelle Azure

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services
Les étapes suivantes vous montrent comment créer un coffre Recovery Services. Un coffre Recovery Services diffère d’un coffre Backup.

1. Si vous utilisez la Sauvegarde Azure pour la première fois, vous devez recourir à l’applet de commande **[Register-AzureRMResourceProvider](https://msdn.microsoft.com/library/mt679020.aspx)** pour enregistrer le fournisseur Azure Recovery Service auprès de votre abonnement.
   
    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. Le coffre Recovery Services constituant une ressource Resource Manager, vous devez le placer dans un groupe de ressources. Si vous ne disposez pas d’un groupe de ressources, créez-en un avec l’applet de commande **[New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt678985.aspx)**. Quand vous créez un groupe de ressources, spécifiez ses nom et emplacement.  
   
    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
    ```
3. Utilisez l’applet de commande **[New-AzureRmRecoveryServicesVault](https://msdn.microsoft.com/library/mt643910.aspx)** pour créer le coffre. Spécifiez pour le coffre le même emplacement que pour le groupe de ressources.
   
    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```
4. Spécifiez le type de redondance de stockage à utiliser : [Stockage localement redondant (LRS)](../storage/storage-redundancy.md#locally-redundant-storage) ou [Stockage géo-redondant (GRS)](../storage/storage-redundancy.md#geo-redundant-storage). L’exemple suivant montre que l’option -BackupStorageRedundancy pour testVault a la valeur GeoRedundant.
   
    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```
   
   > [!TIP]
   > De nombreuses applets de commande Azure Backup nécessitent l’objet coffre Recovery Services en tant qu’entrée. Pour cette raison, il est pratique de stocker l’objet coffre Backup Recovery Services dans une variable.
   > 
   > 

## <a name="view-the-vaults-in-a-subscription"></a>Afficher les coffres dans un abonnement
Utilisez **[Get-AzureRmRecoveryServicesVault](https://msdn.microsoft.com/library/mt643907.aspx)** pour afficher la liste de tous les coffres dans l’abonnement actuel. Vous pouvez utiliser cette commande pour vérifier qu’un coffre a été créé ou pour voir les coffres disponibles dans l’abonnement.

Exécutez la commande Get-AzureRmRecoveryServicesVault ; tous les coffres dans l’abonnement sont alors répertoriés.

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


## <a name="backup-azure-vms"></a>Sauvegarde des machines virtuelles Azure
Un coffre Recovery Services étant créé, vous pouvez l’utiliser pour protéger une machine virtuelle. Toutefois avant d’appliquer la protection, vous devez définir le contexte du coffre et vérifier la stratégie de protection. Le contexte du coffre définit le type de données protégées dans le coffre. La stratégie de protection consiste à planifier le calendrier de l’exécution du travail de sauvegarde et la durée de conservation de chaque instantané de sauvegarde.

Avant d’activer la protection sur une machine virtuelle, vous devez définir le contexte du coffre. Le contexte est appliqué à toutes les applets de commande suivantes.

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name testvault | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Création d’une stratégie de protection
Lorsque vous créez un coffre, il est fourni avec une stratégie par défaut. Cette stratégie déclenche un travail de sauvegarde chaque jour à une heure spécifiée. D’après la stratégie de sauvegarde, l’instantané de sauvegarde est conservé pendant 30 jours. Vous pouvez utiliser la stratégie par défaut pour protéger rapidement votre machine virtuelle et modifier la stratégie ultérieurement avec différents détails.

Utilisez **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://msdn.microsoft.com/library/mt723300.aspx)** pour afficher la liste des stratégies disponibles dans le coffre :

```
PS C:\> Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType AzureVM
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Le fuseau horaire du champ BackupTime dans PowerShell est UTC. Toutefois, lorsque l’heure de sauvegarde s’affiche dans le portail Azure, elle est alignée sur votre fuseau horaire.
> 
> 

Une stratégie de protection de la sauvegarde est associée à au moins une stratégie de rétention.  La stratégie de rétention définit la durée de conservation d’un point de restauration avec Azure Backup. Utilisez **Get-AzureRmRecoveryServicesBackupRetentionPolicyObject** pour afficher la stratégie de rétention par défaut.  De même, vous pouvez utiliser **Get-AzureRmRecoveryServicesBackupSchedulePolicyObject** pour obtenir la stratégie de planification par défaut. Les objets de stratégie de rétention et de planification sont utilisés comme entrées dans l’applet de commande **New-AzureRmRecoveryServicesBackupProtectionPolicy** .

Une stratégie de protection de la sauvegarde définit quand et à quelle fréquence la sauvegarde d’un élément doit être effectuée. La nouvelle applet de commande New-AzureRmRecoveryServicesBackupProtectionPolicy crée un objet PowerShell contenant des informations sur la stratégie de sauvegarde. La stratégie de sauvegarde est utilisée comme entrée dans l’applet de commande Enable-AzureRmRecoveryServicesBackupProtection.

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\>  $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\>  New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType AzureVM -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Activer la protection
L’activation de la protection implique deux objets : l’élément et la stratégie. Les deux objets sont requis pour activer la protection sur le coffre. Une fois que la stratégie a été associée au coffre, le flux de travail de la sauvegarde est déclenché à l’heure planifiée dans la planification de la stratégie.

Pour activer la protection sur des machines virtuelles ARM non chiffrées

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Pour activer la protection sur des machines virtuelles chiffrées [à l’aide de clés BEK et KEK], vous devez accorder des autorisations au service de sauvegarde Azure afin de lire les clés et les secrets du coffre de clés.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName 'KeyVaultName' -ResourceGroupName 'RGNameOfKeyVault' -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Pour les machines virtuelles ASM

```
PS C:\>  $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\>  Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>Modifier une stratégie de protection
Pour modifier la stratégie, modifiez l’objet BackupSchedulePolicyObject ou BackupRetentionPolicy et modifiez la stratégie à l’aide de Set-AzureRmRecoveryServicesBackupProtectionPolicy

L’exemple suivant modifie le nombre de rétention à 365.

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name NewPolicy
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy  $RetPol
```

## <a name="run-an-initial-backup"></a>Exécuter une sauvegarde initiale
La planification de sauvegarde déclenche une sauvegarde intégrale lors de la première sauvegarde de l’élément. Pour les sauvegardes suivantes, la sauvegarde est une copie incrémentielle. Si vous voulez forcer l’exécution de la sauvegarde initiale à un moment déterminé, voire immédiatement, utilisez l’applet de commande **[Backup-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723312.aspx)** :

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "V2VM"
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup               InProgress            4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [AZURE.NOTE: Le fuseau horaire des champs StartTime et EndTime dans PowerShell est UTC. Toutefois, lorsque l’heure s’affiche dans le portail Azure, elle est alignée sur votre fuseau horaire.
> 
> 

## <a name="monitoring-a-backup-job"></a>Surveillance d’une tâche de sauvegarde
La plupart des opérations longues dans Azure Backup sont reproduites en tant que tâche. Cela permet de suivre facilement la progression sans avoir à garder le portail Azure ouvert en permanence.

Pour connaître l’état récent d’une tâche en cours de traitement, utilisez l’applet de commande Get-AzureRmRecoveryservicesBackupJob.

```
PS C:\ > $joblist = Get-AzureRmRecoveryservicesBackupJob –Status InProgress
PS C:\ > $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Au lieu d’interroger ces travaux pour connaître leur état d’avancement, ce qui implique du code supplémentaire inutile, utilisez l’applet de commande **[Wait-AzureRmRecoveryServicesBackupJob](https://msdn.microsoft.com/library/mt723321.aspx)**. Cette applet de commande suspend l’exécution jusqu’à la fin du travail ou jusqu’à ce que la valeur spécifiée pour l’expiration du délai soit atteinte.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Restauration d’une machine virtuelle Azure
Il existe une différence clé entre la restauration d’une machine virtuelle à l’aide du portail Azure et la restauration d’une machine virtuelle à l’aide de PowerShell. Avec PowerShell, l’opération de restauration est terminée dès que sont créés les disques et les informations de configuration à partir d’un point de restauration. L’opération de restauration ne crée pas de machine virtuelle. Les instructions pour créer la machine virtuelle à partir de disques sont fournies. Toutefois, pour restaurer une machine virtuelle complètement, vous devez effectuer les procédures suivantes :

* Sélection de la machine virtuelle
* Choisir un point de récupération
* Restaurer les disques
* Créer la machine virtuelle à partir des disques stockés

L’illustration ci-dessous montre la hiérarchie d’objets à partir de RecoveryServicesVault jusqu’à BackupRecoveryPoint.

![Hiérarchie d’objets Recovery Services montrant BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Pour restaurer les données de sauvegarde, identifiez l’élément sauvegardé et le point de restauration contenant les données ponctuelles. Ensuite, utilisez l’applet de commande **[Restore-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723316.aspx)** pour restaurer les données du coffre vers le compte du client.

### <a name="select-the-vm"></a>Sélection de la machine virtuelle
Pour obtenir l’objet PowerShell permettant d’identifier l’élément à restaurer, vous devez commencer au niveau du conteneur dans le coffre et descendre dans la hiérarchie d’objets. Pour sélectionner le conteneur qui représente la machine virtuelle, utilisez l’applet de commande **[Get-AzureRmRecoveryServicesBackupContainer](https://msdn.microsoft.com/library/mt723319.aspx)** et dirigez-la vers l’applet de commande **[Get-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723305.aspx)**.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType AzureVM –Status Registered -Name 'V2VM'
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem –Container $namedContainer  –WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Choisir un point de récupération
Utilisez l’applet de commande **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://msdn.microsoft.com/library/mt723308.aspx)** pour répertorier tous les points de récupération pour l’élément de sauvegarde. Ensuite, choisissez le point de récupération à restaurer. Si vous ne savez pas quel point de récupération utiliser, nous vous conseillons de choisir le point RecoveryPointType = AppConsistent le plus récent dans la liste.

Dans le script suivant, la variable **$rp**est un tableau de points de récupération pour l’élément de sauvegarde sélectionné. Le tableau est trié dans l’ordre chronologique inverse, le point de récupération le plus récent détenant l’index 0. Utilisez l'indexation de tableau PowerShell standard pour sélectionner le point de récupération. Par exemple : $rp[0] sélectionnera le point de récupération le plus récent.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM
                              /recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```



### <a name="restore-the-disks"></a>Restaurer les disques
Utilisez l’applet de commande **[Restore-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723316.aspx)** pour restaurer les données et la configuration d’un élément de sauvegarde à un point de récupération. Une fois que vous avez identifié un point de récupération, utilisez-le comme valeur du paramètre **-RecoveryPoint** . Dans l’exemple de code précédent, **$rp[0]** a été choisi comme point de récupération à utiliser. Dans l’exemple de code ci-dessous, **$rp[0]** est spécifié comme point de récupération à utiliser pour la restauration sur disque.

Pour restaurer les disques et les informations de configuration

```
PS C:\> $restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName DestAccount -StorageAccountResourceGroupName DestRG
PS C:\> $restorejob
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Une fois le travail de restauration terminé, utilisez l’applet de commande **[Get-AzureRmRecoveryServicesBackupJobDetails](https://msdn.microsoft.com/library/mt723310.aspx)** pour obtenir les détails du travail de restauration. La propriété JobDetails contient les informations nécessaires pour régénérer la machine virtuelle.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails
```

Une fois les disques restaurés, passez à la section suivante, qui explique comment créer la machine virtuelle.

### <a name="create-a-vm-from-restored-disks"></a>Créer une machine virtuelle à partir de disques restaurés
Après avoir restauré les disques, exécutez les étapes ci-après pour créer et configurer la machine virtuelle à partir du disque.

1. Interrogez les propriétés des disques restaurés pour obtenir les détails du travail.
   
    ```
    PS C:\> $properties = $details.properties
    PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
    PS C:\> $containerName = $properties["Config Blob Container Name"]
    PS C:\> $blobName = $properties["Config Blob Name"]
    ```
2. Définissez le contexte de stockage Azure et restaurez le fichier de configuration JSON.
   
    ```
    PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName testvault
    PS C:\> $destination_path = "C:\vmconfig.json"
    PS C:\> Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path
    PS C:\> $obj = ((Get-Content -Path $destination_path -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
    ```
3. Utilisez le fichier de configuration JSON pour créer la configuration de la machine virtuelle.
   
    ```
   PS C:\> $vm = New-AzureRmVMConfig -VMSize $obj.HardwareProfile.VirtualMachineSize -VMName "testrestore"
    ```
4. Attachez le disque du système d’exploitation et les disques de données.
   
      Pour les machines virtuelles non chiffrées,
   
      ```
      PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.StorageProfile.OSDisk.VirtualHardDisk.Uri -CreateOption “Attach”
      PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.StorageProfile.OSDisk.OperatingSystemType foreach($dd in $obj.StorageProfile.DataDisks)
       {
       $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.VirtualHardDisk.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption Attach
       }
       ```
      For encrypted VMs, you need to specify [Key vault information](https://msdn.microsoft.com/library/dn868052.aspx) before you can attach disks.
   
      ```
      PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.StorageProfile.OSDisk.VirtualHardDisk.Uri -DiskEncryptionKeyUrl "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007" -DiskEncryptionKeyVaultId "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault" -KeyEncryptionKeyUrl "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007" -KeyEncryptionKeyVaultId "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault" -CreateOption "Attach" -Windows    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.StorageProfile.OSDisk.OperatingSystemType foreach($dd in $obj.StorageProfile.DataDisks)     {     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.VirtualHardDisk.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption Attach     }     ```
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
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.StorageProfile.OSDisk.OperatingSystemType
    PS C:\> New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="next-steps"></a>Étapes suivantes
Si vous préférez utiliser PowerShell pour gérer vos ressources Azure, consultez l’article de PowerShell pour la protection de Windows Server : [Déployer et gérer une sauvegarde pour Windows Server](backup-client-automation.md). Il existe également un article PowerShell sur la gestion des sauvegardes DPM : [Déployer et gérer une sauvegarde pour DPM](backup-dpm-automation.md). Ces deux articles ont une version concernant les déploiements avec le modèle Resource Manager et le modèle Classic.  




<!--HONumber=Nov16_HO5-->


