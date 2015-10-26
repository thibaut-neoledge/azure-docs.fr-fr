<properties
	pageTitle="Déploiement et gestion d’une sauvegarde pour les machines virtuelles Azure à l’aide de PowerShell | Microsoft Azure"
	description="Découvrez comment déployer et gérer Azure Backup à l’aide de PowerShell"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/01/2015" ms.author="aashishr";"trinadhk" />


# Déploiement et gestion de la sauvegarde pour les machines virtuelles Azure à l’aide de PowerShell
Cet article vous montre comment utiliser Azure PowerShell pour la sauvegarde et la restauration des machines virtuelles IaaS de Azure.

## Concepts
[Découvrez la sauvegarde de machines virtuelles IaaS Azure](backup-azure-vms-introduction.md) dans la documentation Azure Backup. L’article couvre les notions fondamentales sur la raison pour laquelle vous devez sauvegarder votre machine virtuelle, les conditions requises et les restrictions.

Pour pouvoir utiliser efficacement PowerShell, il est nécessaire de comprendre la hiérarchie d’objets et par où commencer.

![Hiérarchie des objets](./media/backup-azure-vms-automation/object-hierarchy.png)

Les 2 processus les plus importants permettent la protection des machines virtuelles et la restauration des données à partir d’un point de restauration. L’objectif de cet article est de vous apprendre à travailler avec les applets de commande PowerShell pour permettre ces deux scénarios.


## Installation et inscription
Pour commencer :

1. [Téléchargez la dernière version de PowerShell](https://github.com/Azure/azure-powershell/releases) (version minimale requise : 1.0.0)

2. Activez les applets de commande Azure Backup en passant en mode *AzureResourceManager* via l’applet de commande **Switch-AzureMode** :

```
PS C:\> Switch-AzureMode AzureResourceManager
```

Les tâches de configuration et d’inscription ci-après peuvent être automatisées avec PowerShell :

- Créer un coffre de sauvegarde
- Inscription des machines virtuelles auprès du service Azure Backup

### Créer un coffre de sauvegarde

> [AZURE.WARNING]Pour les clients utilisant Azure Backup pour la première fois, vous devez enregistrer le fournisseur Azure Backup à utiliser avec votre abonnement. Pour cela, exécutez la commande suivante : Register-AzureProvider -ProviderNamespace "Microsoft.Backup"

Vous pouvez créer un coffre de sauvegarde en utilisant l’applet de commande **New-AzureRMBackupVault**. Le coffre de sauvegarde constituant une ressource ARM, vous devez le placer dans un groupe de ressources. Dans une console Azure PowerShell avec élévation de privilèges, exécutez les commandes suivantes :

```
PS C:\> New-AzureResourceGroup –Name “test-rg” –Region “West US”
PS C:\> $backupvault = New-AzureRMBackupVault –ResourceGroupName “test-rg” –Name “test-vault” –Region “West US” –Storage GeoRedundant
```

Vous pouvez obtenir la liste de tous les coffres de sauvegarde d’un abonnement donné à l’aide de l’applet de commande **Get-AzureRMBackupVault**.

> [AZURE.NOTE]Il est pratique de stocker l’objet du coffre de sauvegarde dans une variable. L’objet coffre est nécessaire comme entrée de nombreuses applets de commande Azure Backup.


### Enregistrement des machines virtuelles
La première étape de la configuration de la sauvegarde avec Azure Backup consiste à enregistrer votre ordinateur ou machine virtuelle avec un coffre Azure Backup. L’applet de commande **Register-AzureRMBackupContainer** prend les informations d’entrée d’une machine virtuelle IaaS Azure et les enregistre dans le coffre spécifié. L’opération d’enregistrement associe la machine virtuelle Azure avec le coffre de sauvegarde et effectue le suivi de la machine virtuelle tout au long du cycle de vie de la sauvegarde.

L’enregistrement de votre machine virtuelle auprès du service Azure Backup crée un objet conteneur de niveau supérieur. Un conteneur contient généralement plusieurs éléments qui peuvent être sauvegardés, mais dans le cas de machines virtuelles, il n’y a qu’un élément de sauvegarde pour le conteneur.

```
PS C:\> $registerjob = Register-AzureRMBackupContainer -Vault $backupvault -Name "testvm" -ServiceName "testvm"
```

## Sauvegarde des machines virtuelles Azure

### Création d’une stratégie de protection
Il n’est pas obligatoire de créer une nouvelle stratégie de protection pour démarrer la sauvegarde de vos machines virtuelles. Le coffre est fourni avec une stratégie par défaut qui peut être utilisée pour activer la protection, puis modifiée ultérieurement avec les bonnes informations. Vous pouvez obtenir la liste des stratégies disponibles dans le coffre à l’aide de l’applet de commande **Get-AzureRMBackupProtectionPolicy** :

```
PS C:\> Get-AzureRMBackupProtectionPolicy -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DefaultPolicy             AzureVM            Daily              26-Aug-15 12:30:00 AM
```

> [AZURE.NOTE]Le fuseau horaire du champ BackupTime dans PowerShell est UTC. Toutefois, lorsque l'heure de sauvegarde s’affiche dans le portail Azure, le fuseau horaire est aligné sur votre système local, tout comme le décalage UTC.

Une stratégie de sauvegarde est associée à au moins une stratégie de rétention. La stratégie de rétention définit la durée de conservation d’un point de restauration avec Azure Backup. L’applet de commande **New-AzureRMBackupRetentionPolicy** crée des objets PowerShell qui contiennent des informations sur la stratégie de rétention. Ces objets de stratégie de rétention sont utilisés comme entrées dans l’applet de commande *New-AzureRMBackupProtectionPolicy* ou directement dans le cas de l’applet de commande *Enable-AzureRMBackupProtection*.

Une stratégie de sauvegarde définit quand et à quelle fréquence la sauvegarde d’un élément doit être effectuée. L’applet de commande **New-AzureRMBackupProtectionPolicy** crée un objet PowerShell qui contient des informations sur la stratégie de sauvegarde. La stratégie de sauvegarde est utilisée comme entrée dans l’applet de commande *Enable-AzureRMBackupProtection*.

```
PS C:\> $Daily = New-AzureRMBackupRetentionPolicyObject -DailyRetention -Retention 30
PS C:\> $newpolicy = New-AzureRMBackupProtectionPolicy -Name DailyBackup01 -Type AzureVM -Daily -BackupTime ([datetime]"3:30 PM") -RetentionPolicy ($Daily) -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DailyBackup01             AzureVM            Daily              01-Sep-15 3:30:00 PM
```

### Activer la protection
L’activation de la protection implique deux objets : l’élément et la stratégie, et les deux doivent appartenir au même coffre. Une fois que la stratégie a été associée à l’élément, le flux de travail de la sauvegarde se lancera à l’heure planifiée.

```
PS C:\> Get-AzureRMBackupContainer -Type AzureVM -Status Registered -Vault $backupvault | Get-AzureRMBackupItem | Enable-AzureRMBackupProtection -Policy $newpolicy
```

### Sauvegarde initiale
La planification de sauvegarde se charge d’effectuer la copie initiale complète de l’élément et la copie incrémentielle pour les sauvegardes suivantes. Cependant, si vous voulez forcer l’exécution de la sauvegarde à un moment déterminé, voire immédiatement, utilisez l’applet de commande **Backup-AzureRMBackupItem** :

```
PS C:\> $container = Get-AzureRMBackupContainer -Vault $backupvault -type AzureVM -name "testvm"
PS C:\> $backupjob = Get-AzureRMBackupItem -Container $container | Backup-AzureRMBackupItem
PS C:\> $backupjob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

> [AZURE.NOTE]Le fuseau horaire des champs StartTime et EndTime affichés dans PowerShell est UTC. Toutefois, lorsque des informations similaires s'affichent dans le portail Azure, le fuseau horaire est aligné sur l'horloge de votre système local.

### Surveillance d’une tâche de sauvegarde
La plupart des opérations longues dans Azure Backup sont reproduites en tant que tâche. Cela permet de suivre facilement la progression sans avoir à garder le portail Azure ouvert en permanence.

Pour obtenir le dernier état d’une tâche en cours, utilisez l’applet de commande **Get-AzureRMBackupJob**.

```
PS C:\> $joblist = Get-AzureRMBackupJob -Vault $backupvault -Status InProgress
PS C:\> $joblist[0]

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

Au lieu d’interroger ces tâches pour connaître leur état d’avancement, ce qui implique du code supplémentaire inutile, il est plus simple d’utiliser l’applet de commande **Wait-AzureRMBackupJob**. Lorsqu’elle est utilisée dans un script, l’applet de commande suspend l’exécution jusqu’à la fin de la tâche ou jusqu’à ce que la valeur spécifiée pour l’expiration du délai soit atteinte.

```
PS C:\> Wait-AzureRMBackupJob -Job $joblist[0] -Timeout 43200
```


## Restauration d’une machine virtuelle Azure

Pour restaurer les données de sauvegarde, vous devez identifier l’élément sauvegardé et le point de restauration contenant les données ponctuelles. Ces informations sont fournies à l’applet de commande Restore-AzureRMBackupItem pour lancer une restauration des données du coffre vers le compte du client.

### Sélection de la machine virtuelle

Pour obtenir l’objet PowerShell permettant d’identifier l’élément à restaurer, vous devez commencer au niveau du conteneur dans le coffre et descendre dans la hiérarchie d’objets. Pour sélectionner le conteneur qui représente la machine virtuelle, utilisez l’applet de commande **Get-AzureRMBackupContainer** et dirigez-la vers l’applet de commande **Get-AzureRMBackupItem**.

```
PS C:\> $backupitem = Get-AzureRMBackupContainer -Vault $backupvault -Type AzureVM -name "testvm" | Get-AzureRMBackupItem
```

### Choisir un point de récupération

Vous pouvez maintenant répertorier tous les points de restauration pour l’élément de sauvegarde via l’applet de commande **Get-AzureRMBackupRecoveryPoint** et choisir le point de récupération à restaurer. En général, les utilisateurs choisissent le point *AppConsistent* le plus récent dans la liste.

```
PS C:\> $rp =  Get-AzureRMBackupRecoveryPoint -Item $backupitem
PS C:\> $rp

RecoveryPointId    RecoveryPointType  RecoveryPointTime      ContainerName
---------------    -----------------  -----------------      -------------
15273496567119     AppConsistent      01-Sep-15 12:27:38 PM  iaasvmcontainer;testvm;testv...
```

La variable ```$rp``` est un tableau de points de récupération pour l’élément de sauvegarde sélectionné, triés par ordre chronologique inverse, le dernier point de récupération est fixé sur l'index 0. Utilisez l'indexation de tableau PowerShell standard pour sélectionner le point de récupération. Par exemple : ```$rp[0]``` sélectionnera le dernier point de récupération.

### Restauration de disques

Il existe une différence essentielle entre les opérations de restauration effectuées par le biais du portail Azure et celles effectuées via Azure PowerShell. Avec PowerShell, l’opération de restauration se limite à la restauration des disques et informations de configuration à partir d’un point de restauration. Il n’y a pas de création de machine virtuelle.

> [AZURE.WARNING]L’applet de commande Restore-AzureRMBackupItem ne crée pas une machine virtuelle. Elle restaure uniquement les disques dans le compte de stockage spécifié. Vous ne constaterez pas le même comportement avec le portail Azure.

```
PS C:\> $restorejob = Restore-AzureRMBackupItem -StorageAccountName "DestAccount" -RecoveryPoint $rp[0]
PS C:\> $restorejob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Restore         InProgress      01-Sep-15 1:14:01 PM   01-Jan-01 12:00:00 AM
```

Vous pouvez obtenir les détails de l’opération de restauration à l’aide de l’applet de commande **Get-AzureRMBackupJobDetails** une fois la tâche de restauration terminée. La propriété *ErrorDetails* contient les informations nécessaires pour régénérer la machine virtuelle.

```
PS C:\> $restorejob = Get-AzureRMBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRMBackupJobDetails -Job $restorejob
```

### Création de la machine virtuelle

La création de la machine virtuelle à partir de disques restaurés peut être effectuée à l’aide d’applets de commande Azure ServiceManager PowerShell plus anciennes, des nouveaux modèles Azure ResourceManager ou même à l’aide du portail Azure. Dans un exemple rapide, nous allons montrer comment y parvenir via les applets de commande Azure ServiceManager.

```
 $properties  = $details.Properties

 $storageAccountName = $properties["TargetStorageAccountName"]
 $containerName = $properties["TargetContainerName"]
 $blobName = $properties["TargetBlobName"]

 Switch-AzureMode AzureServiceManagement

 $keys = Get-AzureStorageKey -StorageAccountName $storageAccountName
 $storageAccountKey = $keys.Primary
 $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey


 $destination_path = "C:\Users\admin\Desktop\vmconfig.xml"
 Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path -Context $storageContext


 $obj = [xml](Get-Content $destination_path)
 $pvr = $obj.PersistentVMRole
 $os = $pvr.OSVirtualHardDisk
 $dds = $pvr.DataVirtualHardDisks
 $osDisk = Add-AzureDisk -MediaLocation $os.MediaLink -OS $os.OS -DiskName "panbhaosdisk"
 $vm = New-AzureVMConfig -Name $pvr.RoleName -InstanceSize $pvr.RoleSize -DiskName $osDisk.DiskName

 if (!($dds -eq $null))
 {
	 foreach($d in $dds.DataVirtualHardDisk)
 	 {
		 $lun = 0;
		 if(!($d.Lun -eq $null))
		 {
	 		 $lun = $d.Lun
		 }
		 $name = "panbhadataDisk" + $lun
     Add-AzureDisk -DiskName $name -MediaLocation $d.MediaLink
     $vm | Add-AzureDataDisk -Import -DiskName $name -LUN $lun
	}
}

New-AzureVM -ServiceName "panbhasample" -Location "SouthEast Asia" -VM $vm
```

Pour plus d’informations sur la création d’une machine virtuelle à partir des disques restaurés, découvrez les applets de commande suivantes :

- [Add-AzureDisk](https://msdn.microsoft.com/library/azure/dn495252.aspx)
- [New-AzureVMConfig](https://msdn.microsoft.com/library/azure/dn495159.aspx)
- [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)

## Exemples de code

### 1\. Obtenir l'état d'achèvement des sous-tâches de travail

Pour suivre l'état d'achèvement de sous-tâches individuelles, vous pouvez utiliser l’applet de commande **Get-AzureRMBackupJobDetails** :

```
PS C:\> $details = Get-AzureRMBackupJobDetails -JobId $backupjob.InstanceId -Vault $backupvault
PS C:\> $details.SubTasks

Name                                                        Status
----                                                        ------
Take Snapshot                                               Completed
Transfer data to Backup vault                               InProgress
```

### 2\. Création d’un rapport quotidien/hebdomadaire des travaux de sauvegarde

En général, les administrateurs souhaitent connaître les travaux de sauvegarde exécutés au cours des dernières 24 heures ainsi que l'état de ces travaux de sauvegarde. En outre, la quantité de données transférées offre aux administrateurs une manière d'évaluer leur utilisation mensuelle de données. Le script ci-dessous extrait les données brutes à partir du service de sauvegarde Azure et affiche les informations sur la console PowerShell.

```
param(  [Parameter(Mandatory=$True,Position=1)]
        [string]$backupvaultname,

        [Parameter(Mandatory=$False,Position=2)]
        [int]$numberofdays = 7)


#Initialize variables
$DAILYBACKUPSTATS = @()
$backupvault = Get-AzureRMBackupVault -Name $backupvaultname
$enddate = ([datetime]::Today).AddDays(1)
$startdate = ([datetime]::Today)

for( $i = 1; $i -le $numberofdays; $i++ )
{
    # We query one day at a time because pulling 7 days of data might be too much
    $dailyjoblist = Get-AzureRMBackupJob -Vault $backupvault -From $startdate -To $enddate -Type AzureVM -Operation Backup
    Write-Progress -Activity "Getting job information for the last $numberofdays days" -Status "Day -$i" -PercentComplete ([int]([decimal]$i*100/$numberofdays))

    foreach( $job in $dailyjoblist )
    {
        #Extract the information for the reports
        $newstatsobj = New-Object System.Object
        $newstatsobj | Add-Member -type NoteProperty -name Date -value $startdate
        $newstatsobj | Add-Member -type NoteProperty -name VMName -value $job.WorkloadName
        $newstatsobj | Add-Member -type NoteProperty -name Duration -value $job.Duration
        $newstatsobj | Add-Member -type NoteProperty -name Status -value $job.Status

        $details = Get-AzureRMBackupJobDetails -Job $job
        $newstatsobj | Add-Member -type NoteProperty -name BackupSize -value $details.Properties["Backup Size"]
        $DAILYBACKUPSTATS += $newstatsobj
    }

    $enddate = $enddate.AddDays(-1)
    $startdate = $startdate.AddDays(-1)
}

$DAILYBACKUPSTATS | Out-GridView
```

Si vous souhaitez ajouter des fonctionnalités graphiques à ce rapport, consultez le blog TechNet sur [Charting with PowerShell](http://blogs.technet.com/b/richard_macdonald/archive/2009/04/28/3231887.aspx)

<!---HONumber=Oct15_HO3-->