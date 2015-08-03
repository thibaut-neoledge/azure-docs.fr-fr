<properties
	pageTitle="Déploiement et gestion d’une sauvegarde pour un serveur/client Windows à l’aide d’Azure PowerShell | Microsoft Azure"
	description="Découvrez comment déployer et gérer Azure Backup à l’aide d’Azure PowerShell"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="07/17/2015" ms.author="aashishr"; "jimpark"/>


# Déploiement et gestion d’une sauvegarde vers Azure pour un serveur/client Windows à l’aide d’Azure PowerShell
Cet article décrit l’utilisation d’Azure PowerShell pour configurer Azure Backup sur un client ou serveur Windows, ainsi que la gestion des sauvegardes et de la récupération.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## Installation et inscription
Les tâches de configuration et d'inscription suivantes peuvent être automatisées avec Azure PowerShell :

- Installation de l'agent Azure Backup
- Inscription auprès du service Azure Backup
- Mise en réseau

### Installation de l'agent Azure Backup
Avant d’installer l'agent Azure Backup, vous devez avoir téléchargé le programme d’installation sur le serveur Windows. Vous pouvez obtenir la dernière version du programme d’installation sur le [Centre de téléchargement de Microsoft](http://aka.ms/azurebackup_agent). Enregistrez le programme d’installation dans un emplacement auquel vous pouvez accéder facilement, par exemple *C:\Téléchargements*.

Pour installer l’agent, exécutez la commande suivante dans une console Azure PowerShell avec des privilèges élevés :

```
PS C:\> MARSAgentInstaller.exe /q
```

Cette opération installe l’agent avec les options par défaut. L’installation s’effectue en arrière-plan et prend quelques minutes. Si vous ne spécifiez pas l’option */nu*, la fenêtre **Windows Update** s’ouvrira à la fin de l’installation pour rechercher des mises à jour. Une fois installé, l’agent apparaît dans la liste des programmes installés.

Pour afficher la liste des programmes installés, cliquez sur **Panneau de configuration** > **Programmes** > **Programmes et fonctionnalités**.

![Agent installé](./media/backup-client-automation/installed-agent-listing.png)

#### Options d’installation

Pour afficher toutes les options disponibles via la ligne de commande, utilisez la commande suivante :

```
PS C:\> MARSAgentInstaller.exe /?
```

Les options disponibles incluent :

| Option | Détails | Default |
| ---- | ----- | ----- |
| /q | Installation silencieuse | - | | /p:"emplacement" | Chemin du dossier d’installation de l’agent Azure Backup. | C:\Program Files\Microsoft Azure Recovery Services Agent | | /s:"emplacement" | Chemin du dossier du cache de l’agent Azure Backup. | C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch | | /m | Abonnement à Microsoft Update | - | | /nu | Ne pas rechercher les mises à jour après l’installation | - | | /d | Désinstalle Microsoft Azure Recovery Services Agent | - | | /ph | Adresse de l’hôte proxy | - | | /po | Numéro de port de l’hôte proxy | - | | /pu | Nom d’utilisateur de l’hôte proxy | - | | /pw | Mot de passe du proxy | - |


### Inscription auprès du service Azure Backup
Avant de pouvoir vous inscrire auprès du service Azure Backup, vous devez vous assurer que les [conditions préalables](backup-try-azure-backup-in-10-mins.md) sont remplies. Vous devez respecter les consignes suivantes :

- Avoir un abonnement Azure valide
- Créer un coffre de sauvegarde
- Télécharger les informations d'identification de coffre et les stocker dans un emplacement pratique (comme *C:\Téléchargements*). Les informations d’identification du coffre peuvent également être renommées à votre convenance. 
L’inscription de la machine auprès du coffre s’effectue l’aide de la cmdlet [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) :

```
PS C:\> Start-OBRegistration -VaultCredentials "C:\Downloads\register.vaultcredentials" -Confirm:$false

CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : test-vault
Region              : Australia East

Machine registration succeeded.
```

> [AZURE.IMPORTANT]N’utilisez pas de chemins relatifs pour spécifier le fichier des informations d’identification du coffre. Vous devez fournir un chemin absolu dans la cmdlet.

### Mise en réseau
Lorsque l’ordinateur Windows accède à Internet via un serveur proxy, les paramètres proxy peuvent également être fournis à l’agent. Dans cet exemple, il n’y a aucun serveur proxy. Nous effaçons donc explicitement toutes informations concernant un proxy.

L’utilisation de la bande passante peut également être contrôlée avec les options ```work hour bandwidth``` et ```non-work hour bandwidth```, certains jours de la semaine.

La définition des détails sur le proxy et la bande passante s’effectue à l’aide de la cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) :

```
PS C:\> Set-OBMachineSetting -NoProxy
Server properties updated successfully.

PS C:\> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

### Paramètres de chiffrement
Les données sauvegardées envoyées à Azure Backup sont chiffrées pour garantir leur confidentialité. Le mot de passe du chiffrement est le « mot de passe » permettant de déchiffrer les données lors de la restauration.

```
PS C:\> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
Server properties updated successfully
```

> [AZURE.IMPORTANT]Conservez les informations de phrase secrète en lieu sûr après les avoir définies. Vous ne pourrez pas restaurer les données à partir d’Azure sans ce mot de passe.

## Sauvegarde des fichiers et dossiers
Toutes les sauvegardes de serveurs et clients Windows vers Azure Backup sont régies par une stratégie. Cette dernière comprend trois parties :

1. Une **planification de sauvegarde** qui spécifie quand les sauvegardes doivent être établies et synchronisées avec le service.
2. Une **planification de rétention** qui spécifie la durée de rétention des points de récupération dans Azure.
3. Une **spécification d'inclusion/exclusion de fichier** qui dicte ce qui doit être sauvegardé.

Dans ce document, comme nous automatisons la sauvegarde, nous supposons que rien n'a été configuré. Nous commençons par créer une stratégie de sauvegarde en utilisant l’applet de commande [New-OBPolicy](https://technet.microsoft.com/library/hh770416.aspx).

```
PS C:\> $newpolicy = New-OBPolicy
```

À ce stade, la stratégie est vide et les autres applets de commande sont nécessaires pour définir les éléments qui seront inclus ou exclus, le moment auquel les sauvegardes s'exécuteront et leur emplacement de stockage.

### Configuration de la planification de sauvegarde
La première des 3 parties d'une stratégie est la planification de sauvegarde, qui est créée à l'aide de l’applet de commande [New-OBSchedule](https://technet.microsoft.com/library/hh770401). La planification de sauvegarde définit le moment où les sauvegardes doivent être effectuées. Lors de la création d'une planification, vous devez spécifier 2 paramètres d'entrée :

- Les **jours de la semaine** où la sauvegarde doit s'exécuter. Vous pouvez exécuter le travail de sauvegarde une seule journée ou tous les jours de la semaine, ou une combinaison des deux.
- Les **heures** où la sauvegarde doit être exécutée. Vous pouvez définir jusqu'à 3 différentes heures pour le déclenchement de la sauvegarde.

Par exemple, vous pouvez configurer une stratégie de sauvegarde qui s'exécute à 16 h 00 chaque samedi et chaque dimanche.

```
PS C:\> $sched = New-OBSchedule -DaysofWeek Saturday, Sunday -TimesofDay 16:00
```

La planification de sauvegarde doit être associée à une stratégie à l'aide de l’applet de commande [Set-OBSchedule](https://technet.microsoft.com/library/hh770407).

```
PS C:\> Set-OBSchedule -Policy $newpolicy -Schedule $sched
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```
### Configuration d'une stratégie de rétention
La stratégie de rétention définit la durée de conservation des points de récupération créés à partir des travaux de sauvegarde. Lorsque vous créez une stratégie de rétention à l'aide de l’applet de commande [New-OBRetentionPolicy](https://technet.microsoft.com/library/hh770425), vous pouvez spécifier le nombre de jours pendant lesquels les points de récupération de sauvegarde doivent être conservés avec Azure Backup. L'exemple suivant définit une stratégie de rétention de 7 jours.

```
PS C:\> $retentionpolicy = New-OBRetentionPolicy -RetentionDays 7
```

> [AZURE.NOTE]Les applets de commande PowerShell ne prennent actuellement pas en charge la configuration de stratégies de rétention à long terme. Pour définir ces dernières, utilisez la console de l'interface utilisateur Azure Backup.

La stratégie de rétention doit être associée à la stratégie principale à l'aide de l'applet de commande [Set-OBRetentionPolicy](https://technet.microsoft.com/library/hh770405) :

```
PS C:\> Set-OBRetentionPolicy -Policy $newpolicy -RetentionPolicy $retentionpolicy

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```
### Inclusion et exclusion des fichiers à sauvegarder
Un objet ```OBFileSpec``` définit les fichiers à inclure et à exclure d'une sauvegarde. Il s'agit d'un ensemble de règles qui définissent l'étendue des fichiers et dossiers protégés sur un ordinateur. Vous pouvez avoir de nombreuses règles d'inclusion ou d’exclusion en fonction de vos besoins, et les associer à une stratégie. Lorsque vous créez un objet OBFileSpec, vous pouvez :

- Spécifier les fichiers et dossiers à inclure
- Spécifier les fichiers et dossiers à exclure
- Indiquer la sauvegarde récursive des données dans un dossier (ou) indiquer si seuls les fichiers de niveau supérieur du dossier spécifié doivent être sauvegardés

Dans le dernier cas, l’opération est effectuée à l’aide de l'indicateur -NonRecursive dans la commande New-OBFileSpec.

Dans l'exemple ci-dessous, nous sauvegardons les volumes C: et D: et excluons les fichiers binaires de système d'exploitation dans le dossier Windows et tous les dossiers temporaires. Pour cela, nous allons créer deux spécifications de fichiers à l'aide de l’applet de commande [New-OBFileSpec](https://technet.microsoft.com/library/hh770408) : une pour l’inclusion et une pour l’exclusion. Une fois que les spécifications de fichiers ont été créées, elles sont associées à la stratégie à l'aide de l’applet de commande [Add-OBFileSpec](https://technet.microsoft.com/library/hh770424).

```
PS C:\> $inclusions = New-OBFileSpec -FileSpec @("C:", "D:")

PS C:\> $exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude

PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $inclusions

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid


PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $exclusions

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### Application de la stratégie
L'objet de stratégie est à présent complet. Il est associé à une planification de sauvegarde, à une stratégie de rétention et à une liste d’inclusion/exclusion de fichiers. Cette stratégie peut maintenant être validée à des fins d’utilisation par Azure Backup. Avant d'appliquer la stratégie que vous venez de créer, assurez-vous qu'aucune stratégie de sauvegarde existante n’est associée au serveur à l'aide de l’applet de commande [Remove-OBPolicy](https://technet.microsoft.com/library/hh770415). Lors de la suppression de la stratégie, vous êtes invité à confirmer l'opération. Pour ignorer la confirmation, utilisez l’indicateur ```-Confirm:$false``` avec l'applet de commande.

```
PS C:\> Get-OBPolicy | Remove-OBPolicy
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

La validation de l'objet de stratégie s'effectue à l'aide de l’applet de commande [Set-OBPolicy](https://technet.microsoft.com/library/hh770421). Une confirmation vous est également demandée. Pour ignorer la confirmation, utilisez l’indicateur ```-Confirm:$false``` avec l'applet de commande.

```
PS C:\> Set-OBPolicy -Policy $newpolicy
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : {DataSource DatasourceId:4508156004108672185 Name:C:\ FileSpec:FileSpec FileSpec:C:\ IsExclude:False IsRecursive:True ,FileSpec FileSpec:C:\windows IsExclude:True IsRecursive:True ,FileSpec FileSpec:C:\temp IsExclude:True IsRecursive:True
              , DataSource
              DatasourceId:4508156005178868542
              Name:D:\
              FileSpec:FileSpec
              FileSpec:D:\
              IsExclude:False
              IsRecursive:True

              }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

Vous pouvez afficher les détails de la stratégie de sauvegarde existante à l'aide de l’applet de commande [Get-OBPolicy](https://technet.microsoft.com/library/hh770406). Vous pouvez afficher plus de détails à l'aide de l’applet de commande [Get-OBSchedule](https://technet.microsoft.com/library/hh770423) pour la planification de sauvegarde et de l’applet de commande [Get-OBRetentionPolicy](https://technet.microsoft.com/library/hh770427) pour les stratégies de rétention.

```
PS C:\> Get-OBPolicy | Get-OBSchedule
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a ScheduleRunDays : {Saturday, Sunday} ScheduleRunTimes : {16:00:00} State : Existing
PS C:\> Get-OBPolicy | Get-OBRetentionPolicy
RetentionDays : 7 RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e State : Existing
PS C:\> Get-OBPolicy | Get-OBFileSpec
FileName : * FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\ FileSpec : D:\ IsExclude : False IsRecursive : True
FileName : * FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\ FileSpec : C:\ IsExclude : False IsRecursive : True
FileName : * FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows FileSpec : C:\windows IsExclude : True IsRecursive : True
FileName : * FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp FileSpec : C:\temp IsExclude : True IsRecursive : True
```

### Exécution d'une sauvegarde ad hoc
Une fois qu'une stratégie de sauvegarde a été définie, les sauvegardes ont lieu selon la planification indiquée. Le déclenchement d'une sauvegarde ad hoc est également possible à l'aide de l’applet de commande [Start-OBBackup](https://technet.microsoft.com/library/hh770426) :

```
PS C:\> Get-OBPolicy | Start-OBBackup
Taking snapshot of volumes... Preparing storage... Estimating size of backup items... Estimating size of backup items... Transferring data... Verifying backup... Job completed. The backup operation completed successfully.
```

## Restauration des données à partir d'Azure Backup
Cette section vous guide tout au long des étapes d'automatisation de la récupération des données à partir d’Azure Backup. Cette opération implique les étapes suivantes :

1. Sélection du volume source
2. Choix d’un point de sauvegarde à restaurer
3. Choix d’un élément à restaurer
4. Déclenchement du processus de restauration

### Sélection du volume source
Pour restaurer un élément à partir d’Azure Backup, vous devez d'abord identifier la source associée. Étant donné que nous exécutons les commandes dans le contexte d'un serveur ou d’un client Windows, l'ordinateur est déjà identifié. L'étape suivante pour identifier la source consiste à identifier le volume qui la contient. Vous pouvez récupérer la liste des volumes ou des sources en cours de sauvegarde à partir de cet ordinateur en exécutant l’applet de commande [Get-OBRecoverableSource](https://technet.microsoft.com/library/hh770410). Cette commande renvoie un tableau de toutes les sources sauvegardées à partir de ce serveur/client.

```
PS C:\> $source = Get-OBRecoverableSource
PS C:\> $source FriendlyName : C:\ RecoverySourceName : C:\ ServerName : myserver.microsoft.com
FriendlyName : D:\ RecoverySourceName : D:\ ServerName : myserver.microsoft.com
```

### Choix d’un point de sauvegarde à restaurer
Vous pouvez récupérer la liste des points de sauvegarde en exécutant l’applet de commande [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) avec les paramètres appropriés. Dans notre exemple, nous allons sélectionner le dernier point de sauvegarde du volume source *D:* et l'utiliser pour récupérer un fichier spécifique.

```
PS C:\> $rps = Get-OBRecoverableItem -Source $source[1]
IsDir : False ItemNameFriendly : D:\ ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\ LocalMountPoint : D:\ MountPointName : D:\ Name : D:\ PointInTime : 18-Jun-15 6:41:52 AM ServerName : myserver.microsoft.com ItemSize : ItemLastModifiedTime :
IsDir : False ItemNameFriendly : D:\ ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\ LocalMountPoint : D:\ MountPointName : D:\ Name : D:\ PointInTime : 17-Jun-15 6:31:31 AM ServerName : myserver.microsoft.com ItemSize : ItemLastModifiedTime :
```
L'objet ```$rps``` est un tableau de points de sauvegarde. Le premier élément est le point le plus récent et le Nième élément est le point le plus ancien. Pour choisir le point le plus récent, nous allons utiliser ```$rps[0]```.

### Choix d’un élément à restaurer
Pour identifier le fichier ou dossier exact à restaurer, utilisez de manière récursive l’applet de commande [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx). De cette façon, la hiérarchie de dossiers est accessible uniquement à l'aide de ```Get-OBRecoverableItem```.

Dans cet exemple, si vous souhaitez restaurer le fichier *finances.xls*, nous pouvons le référencer à l'aide de l'objet ```$filesFolders[1]```.

```
PS C:\> $filesFolders = Get-OBRecoverableItem $rps[0]
PS C:\> $filesFolders IsDir : True ItemNameFriendly : D:\MyData\ ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\ LocalMountPoint : D:\ MountPointName : D:\ Name : MyData PointInTime : 18-Jun-15 6:41:52 AM ServerName : myserver.microsoft.com ItemSize : ItemLastModifiedTime : 15-Jun-15 8:49:29 AM
PS C:\> $filesFolders = Get-OBRecoverableItem $filesFolders[0]
PS C:\> $filesFolders IsDir : False ItemNameFriendly : D:\MyData\screenshot.oxps ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\screenshot.oxps LocalMountPoint : D:\ MountPointName : D:\ Name : screenshot.oxps PointInTime : 18-Jun-15 6:41:52 AM ServerName : myserver.microsoft.com ItemSize : 228313 ItemLastModifiedTime : 21-Jun-14 6:45:09 AM
IsDir : False ItemNameFriendly : D:\MyData\finances.xls ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\finances.xls LocalMountPoint : D:\ MountPointName : D:\ Name : finances.xls PointInTime : 18-Jun-15 6:41:52 AM ServerName : myserver.microsoft.com ItemSize : 96256 ItemLastModifiedTime : 21-Jun-14 6:43:02 AM
```

Vous pouvez également rechercher des éléments à restaurer à l'aide de l’applet de commande ```Get-OBRecoverableItem```. Dans notre exemple, pour rechercher le fichier *finances.xls*, nous pouvons trouver le fichier en exécutant la commande suivante :

```
PS C:\> $item = Get-OBRecoverableItem -RecoveryPoint $rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### Déclenchement du processus de restauration
Pour déclencher le processus de restauration, nous devons d'abord spécifier les options de récupération. Pour ce faire, utilisez l’applet de commande [New-OBRecoveryOption](https://technet.microsoft.com/library/hh770417.aspx) . Dans le cadre de cet exemple, supposons que vous souhaitez restaurer les fichiers dans *C:\temp*. Supposons également que vous souhaitez ignorer les fichiers qui existent déjà dans le dossier de destination *C:\temp*. Pour créer une telle option de récupération, utilisez la commande suivante :

```
PS C:\> $recovery_option = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Déclenchez à présent la restauration à l'aide de la commande [Start-OBRecovery](https://technet.microsoft.com/library/hh770402.aspx) dans l’```$item``` sélectionné à partir de la sortie de l’applet de commande ```Get-OBRecoverableItem``` :

```
PS C:\> Start-OBRecovery -RecoverableItem $item -RecoveryOption $recover_option Estimating size of backup items... Estimating size of backup items... Estimating size of backup items... Estimating size of backup items... Job completed. The recovery operation completed successfully.
```


## Désinstallation de l’agent Azure Backup
La désinstallation de l’agent Azure Backup peut être effectuée à l’aide de la commande suivante :

```
PS C:\> .\MARSAgentInstaller.exe /d /q
```

La désinstallation des fichiers binaires de l'agent de l'ordinateur a certaines conséquences à prendre en compte :

- Elle supprime le filtre de fichier de l'ordinateur et le suivi des modifications est arrêté.
- Toutes les informations de stratégie sont supprimées de l'ordinateur, mais elles continuent à être stockées dans le service.
- Toutes les planifications de sauvegarde sont supprimées, et aucune autre sauvegarde n'est effectuée.

Cependant, les données stockées dans Azure sont conservées selon la stratégie de rétention que vous avez définie. Les points plus anciens deviennent automatiquement obsolètes.

## Gestion à distance
Toute la gestion concernant l’agent Azure Backup, les stratégies et les sources de données peut être effectuée à distance via Azure PowerShell. L’ordinateur qui sera géré à distance doit être correctement préparé.

Par défaut, le service WinRM est configuré pour un démarrage manuel. Le type de démarrage doit être défini sur *Automatique* et le service devrait être démarré. Pour vérifier que le service WinRM est exécuté, la valeur de la propriété État devrait être défini sur *En cours d’exécution*.

```
PS C:\> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

Azure PowerShell doit être configuré pour l'accès distant.

```
PS C:\> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:\> Set-ExecutionPolicy unrestricted -force
```

L’ordinateur peut maintenant être géré à distance, en commençant par l’installation de l’agent. Par exemple, le script suivant copie et installe l’agent sur l’ordinateur distant.

```
PS C:\> $dloc = "\REMOTESERVER01\c$\Windows\Temp"
PS C:\> $agent = "\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:\> $args = "/q"
PS C:\> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:\> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:\> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```

## Étapes suivantes
Pour en savoir plus sur Azure Backup pour client/serveur Windows, consultez la rubrique [Présentation d’Azure Backup](backup-introduction-to-azure-backup.md)

<!---HONumber=July15_HO4-->