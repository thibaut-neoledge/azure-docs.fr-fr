<properties
   pageTitle="Déploiement et gestion d’une sauvegarde pour les machines virtuelles ARM à l’aide de PowerShell | Microsoft Azure"
   description="Utiliser PowerShell pour déployer et gérer les sauvegardes dans Azure pour les machines virtuelles ARM"
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="05/09/2016"
   ms.author="markgal; trinadhk"/>

# Déploiement et gestion de la sauvegarde pour les machines virtuelles ARM à l’aide de PowerShell

> [AZURE.SELECTOR]
- [Resource Manager PowerShell](backup-azure-vms-automation.md)
- [Classic PowerShell](backup-azure-vms-classic-automation.md)

Cet article vous montre comment utiliser Azure PowerShell pour sauvegarder et restaurer une machine virtuelle Azure à partir d’un coffre Recovery Services. Un coffre Recovery Services est une ressource Azure Resource Manager (ARM) utilisée pour protéger les données et les actifs dans Azure Backup et Azure Site Recovery Services. Utiliser un coffre Recovery Services dans un déploiement ARM. Vous pouvez utiliser un coffre Recovery Services pour protéger des machines virtuelles déployées avec Azure Service Manager (ASM), ainsi que des machines virtuelles ARM.

>[AZURE.NOTE] Azure dispose de deux modèles de déploiement pour créer et utiliser des ressources : [Azure Resource Manager et Azure Classic](../resource-manager-deployment-model.md). Cet article concerne l’utilisation avec des machines virtuelles créées à l’aide du modèle Resource Manager.

Cet article vous guide tout au long de l’utilisation de PowerShell pour protéger une machine virtuelle et la restauration de données à partir d’un point de récupération.

## Concepts

Si vous n’êtes pas familiarisé avec le service Azure Backup, consultez [Qu’est-ce qu’Azure Backup ?](backup-introduction-to-azure-backup.md) pour obtenir une vue d’ensemble du service. Avant de commencer, assurez-vous de connaître les conditions préalables de base nécessaires pour travailler avec Azure Backup et les limitations de la solution actuelle de sauvegarde de la machines virtuelles.

Pour pouvoir utiliser efficacement PowerShell, il est nécessaire de comprendre la hiérarchie d’objets et par où commencer.

![Hiérarchie des objets dans Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## Installation et inscription

Pour commencer :

1. [Téléchargez la dernière version de PowerShell](https://github.com/Azure/azure-powershell/releases) (version minimale requise : 1.0.0).

2. Rechercher les applets de commande PowerShell Azure Backup disponibles en tapant la commande suivante :

```
PS C:\WINDOWS\system32> Get-Command *azurermrecoveryservices*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.0.7      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesVault                   1.0.7      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.0.7      AzureRM.RecoveryServices
Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          New-AzureRmRecoveryServicesVault                   1.0.7      AzureRM.RecoveryServices
Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Remove-AzureRmRecoveryServicesVault                1.0.7      AzureRM.RecoveryServices
Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.0.7      AzureRM.RecoveryServices
Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.0.7      AzureRM.RecoveryServices
Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.0.0      AzureRM.RecoveryServices.Backup
```


Les tâches ci-après peuvent être automatisées avec PowerShell :

- Créer un coffre Recovery Services
- Sauvegarde ou protection de machines virtuelles Azure
- Déclencher une tâche de sauvegarde
- Surveiller une tâche de sauvegarde
- Restauration d’une machine virtuelle Azure

## Créer un coffre Recovery Services

> [AZURE.TIP] Pour les clients utilisant Azure Backup pour la première fois, vous devez enregistrer le fournisseur Azure Recovery Service à utiliser avec votre abonnement. Pour ce faire, exécutez la commande suivante : Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"

Vous pouvez créer un nouveau coffre Recovery Services à l’aide de l’applet de commande **New-AzureRmRecoveryServicesVault**. Le coffre Recovery Services constituant une ressource ARM, vous devez le placer dans un groupe de ressources. Dans une console Azure PowerShell avec élévation de privilèges, exécutez les commandes suivantes :

```
PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
```

Pour afficher une liste de tous les coffres Recovery Services dans un abonnement, utilisez l’applet de commande **Get-AzureRmRecoveryServicesVault**.

### Définir la redondance du stockage
Lorsque vous créez un coffre Recovery Services, spécifiez le type de redondance de stockage à utiliser : stockage localement redondant (LRS) ou stockage géo-redondant (GRS). L’exemple suivant montre que l’option BackupStorageRedundancy pour testVault a la valeur GeoRedundant.

```
PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```


> [AZURE.TIP] De nombreuses applets de commande Azure Backup nécessitent l’objet coffre Recovery Services en tant qu’entrée. Pour cette raison, il est pratique de stocker l’objet coffre Recovery Services de sauvegarde dans une variable.


## Sauvegarde des machines virtuelles Azure

Avant d’activer la protection sur une machine virtuelle, vous devez définir le contexte du coffre. Le contexte est appliqué à toutes les applets de commande suivantes.

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name testvault | Set-AzureRmRecoveryServicesVaultContext
```

### Création d’une stratégie de protection

Lorsque vous créez un coffre, il est fourni avec une stratégie par défaut. Cette stratégie déclenche une opération de sauvegarde chaque jour à 21h30. L’instantané de sauvegarde est conservé pendant 30 jours. Vous pouvez utiliser la stratégie par défaut pour protéger rapidement votre machine virtuelle et modifier la stratégie ultérieurement avec différents détails.

Pour afficher la liste des stratégies disponibles dans le coffre, utilisez l’applet de commande Get-AzureRmRecoveryServicesBackupProtectionPolicy :

```
PS C:\WINDOWS\system32> get-AzureRMRecoveryServicesBackupProtectionPolicy -WorkloadType AzureVM
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [AZURE.NOTE] Le fuseau horaire du champ BackupTime dans PowerShell est UTC. Toutefois, lorsque l’heure de sauvegarde s’affiche dans le portail Azure, elle est alignée sur votre fuseau horaire.

Une stratégie de protection de la sauvegarde est associée à au moins une stratégie de rétention. La stratégie de rétention définit la durée de conservation d’un point de restauration avec Azure Backup. Utilisez Get-AzureRmRecoveryServicesBackupRetentionPolicyObject pour afficher la stratégie de rétention par défaut. De même, vous pouvez obtenir la stratégie de planification par défaut à l’aide de Get-AzureRmRecoveryServicesBackupSchedulePolicyObject. Les objets de stratégie de rétention et de planification sont utilisés comme entrées dans l’applet de commande New-AzureRmRecoveryServicesBackupProtectionPolicy.

Une stratégie de protection de la sauvegarde définit quand et à quelle fréquence la sauvegarde d’un élément doit être effectuée. La nouvelle applet de commande New-AzureRmRecoveryServicesBackupProtectionPolicy crée un objet PowerShell contenant des informations sur la stratégie de sauvegarde. La stratégie de sauvegarde est utilisée comme entrée dans l’applet de commande Enable-AzureRmRecoveryServicesBackupProtection.

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\>  $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\>  New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType AzureVM -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### Activer la protection

L’activation de la protection implique deux objets : l’élément et la stratégie. Les deux objets sont requis pour activer la protection sur le coffre. Une fois que la stratégie a été associée à l’élément, le flux de travail de la sauvegarde est déclenché à l’heure planifiée dans la planification de la stratégie.

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Pour les machines virtuelles ASM

```
PS C:\>  $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\>  Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### Modifier une stratégie de protection

Pour modifier la stratégie, modifiez l’objet BackupSchedulePolicyObject ou BackupRetentionPolicy et modifiez la stratégie à l’aide de Set-AzureRmRecoveryServicesBackupProtectionPolicy

L’exemple suivant modifie le nombre de rétention à 365.

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRMRecoveryServicesBackupProtectionPolicy -Name NewPolicy
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy  $RetPol
```

## Exécuter une sauvegarde initiale

La planification de sauvegarde déclenche une sauvegarde intégrale lors de la première sauvegarde de l’élément. Pour les sauvegardes suivantes, la sauvegarde est une copie incrémentielle. Si vous voulez forcer l’exécution de la sauvegarde initiale à un moment déterminé, voire immédiatement, utilisez l’applet de commande Backup-AzureRmRecoveryServicesBackupItem :

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "V2VM";
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM";
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item;
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Backup               InProgress            4/23/2016 5:00:30 PM            cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [AZURE.NOTE: Le fuseau horaire des champs StartTime et EndTime dans PowerShell est UTC. Toutefois, lorsque l’heure s’affiche dans le portail Azure, elle est alignée sur votre fuseau horaire.

## Surveillance d’une tâche de sauvegarde

La plupart des opérations longues dans Azure Backup sont reproduites en tant que tâche. Cela permet de suivre facilement la progression sans avoir à garder le portail Azure ouvert en permanence.

Pour connaître l’état récent d’une tâche en cours de traitement, utilisez l’applet de commande Get-AzureRMRecoveryservicesBackupJob.

```
PS C:\ > $joblist = Get-AzureRMRecoveryservicesBackupJob –Status InProgress
PS C:\ > $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Au lieu d’interroger ces tâches pour connaître leur état d’avancement, ce qui implique du code supplémentaire inutile, il est plus simple d’utiliser l’applet de commande Wait-AzureRmRecoveryServicesBackupJob. Lorsqu’elle est utilisée dans un script, l’applet de commande suspend l’exécution jusqu’à la fin de la tâche ou jusqu’à ce que la valeur spécifiée pour l’expiration du délai soit atteinte.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## Restauration d’une machine virtuelle Azure

Pour restaurer les données de sauvegarde, vous devez identifier l’élément sauvegardé et le point de restauration contenant les données ponctuelles. Ces informations sont fournies à l’applet de commande Restore-AzureRMRecoveryServicesBackupItem pour lancer une restauration des données du coffre vers le compte du client.

### Sélection de la machine virtuelle

Pour obtenir l’objet PowerShell permettant d’identifier l’élément à restaurer, vous devez commencer au niveau du conteneur dans le coffre et descendre dans la hiérarchie d’objets. Pour sélectionner le conteneur qui représente la machine virtuelle, utilisez l'applet de commande Get-AzureRmRecoveryServicesBackupContainer et dirigez-la vers l'applet de commande Get-AzureRmRecoveryServicesBackupItem.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType AzureVM –Status Registered -Name 'V2VM'
PS C:\> $backupitem=Get-AzureRmRecoveryServicesBackupItem –Container $namedContainer  –WorkloadType "AzureVM"
```

### Choisir un point de récupération

Vous pouvez maintenant répertorier tous les points de restauration pour l’élément de sauvegarde via l’applet de commande Get-AzureRMRecoveryServicesBackupRecoveryPoint et choisir le point de récupération à restaurer. En général, les utilisateurs choisissent le point AppConsistent le plus récent dans la liste.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRMRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
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

La variable $rp est un tableau de points de récupération pour l’élément de sauvegarde sélectionné et triés par ordre chronologique inverse, le dernier point de récupération étant fixé sur l’index 0. Utilisez l'indexation de tableau PowerShell standard pour sélectionner le point de récupération. Par exemple : $rp[0] sélectionnera le point de récupération le plus récent.

### Restauration de disques

Il existe une différence essentielle entre les opérations de restauration effectuées par le biais du portail Azure et celles effectuées via Azure PowerShell. Avec PowerShell, l’opération de restauration se limite à la restauration des disques et informations de configuration à partir d’un point de restauration. Il n’y a pas de création de machine virtuelle.

> [AZURE.WARNING] L’applet de commande Restore-AzureRMRecoveryServicesBackupItem ne crée pas une machine virtuelle. Elle restaure uniquement les disques dans le compte de stockage spécifié. Cela est différent de ce qui se produit dans le portail Azure.

```
PS C:\> $restorejob = Restore-AzureRMRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName DestAccount
 -StorageAccountResourceGroupName DestRG
PS C:\> $restorejob
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Restore               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Vous pouvez obtenir les détails de l’opération de restauration à l’aide de l’applet de commande Get-AzureRmRecoveryServicesBackupJobDetails une fois la tâche de restauration terminée. La propriété JobDetails contient les informations nécessaires pour régénérer la machine virtuelle.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails
```

## Inscription de Windows Server ou de DPM dans un coffre Recovery Services

Une fois que vous avez créé un coffre Recovery Services, téléchargez le dernier agent et les informations d’identification de coffre et stockez-les dans un emplacement pratique comme C:\\Téléchargements.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
PS C:\> $credsfilename
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

Sur le serveur DPM ou Windows Server, exécutez l’applet de commande [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) pour inscrire l’ordinateur auprès du coffre.

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration-VaultCredentials $cred -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

<!---HONumber=AcomDC_0518_2016-->