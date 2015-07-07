<properties
	pageTitle="Sauvegarde Azure | Déployer et gérer une sauvegarde pour DPM à l’aide de Windows PowerShell | Microsoft Azure"
	description="Déployer et gérer une sauvegarde Azure pour Data Protection Manager (DPM) à l’aide de Windows PowerShell"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/17/2015"
	ms.author="jimpark"/>


# Déployer et gérer une sauvegarde vers Azure pour des serveurs Data Protection Manager (DPM) à l’aide de Windows PowerShell
Cet article décrit l’utilisation de l’interface de ligne de commande Windows PowerShell® pour configurer Azure Backup sur un serveur DPM, ainsi que la gestion des sauvegardes et des récupérations.

## Configuration de l’environnement Windows PowerShell
Avant de pouvoir utiliser Windows PowerShell pour gérer Azure Backup, vous devez disposer de la bonne version de Windows PowerShell et avoir correctement configuré les variables d’environnement.

Vérifiez que vous avez Windows PowerShell version 3.0 ou 4.0. Pour trouver la version de Windows PowerShell, tapez cette commande dans une invite de commandes Windows PowerShell/DPM.

```
$PSVersionTable
```

Le type d'information suivant s'affiche :

| Nom | Valeur |
| ---- | ----- |
| PSVersion | 3.0 |
| WSManStackVersion | 3.0 |
| SerializationVersion | 1.1.0.1 |
| CLRVersion | 4.0.30319.18444 |
| BuildVersion | 6.2.9200.16481 |
| PSCompatibleVersions | {1.0, 2.0, 3.0} |
| PSRemotingProtocolVersion | 2.2 |

Vérifiez que la valeur de **PSVersion** est 3.0 ou 4.0. Dans le cas contraire, consultez [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

## Installation et inscription
### Installation de l'agent Azure Backup sur un serveur DPM
Pour installer l'agent Azure Backup, vous devez avoir téléchargé le programme d’installation sur le serveur Windows. Vous pouvez obtenir la dernière version du programme d’installation sur le [Centre de téléchargement de Microsoft](aka.ms/azurebackup_agent). Enregistrez le programme d’installation dans un emplacement auquel vous pouvez accéder facilement, par exemple *C:*.

Pour installer l’agent, exécutez la commande suivante dans une console Windows PowerShell DPM avec des privilèges élevés :

```
PS C:> MARSAgentInstaller.exe /q
```

Cette opération installera l’agent avec les options par défaut. L’installation s’effectue en arrière-plan et prend quelques minutes. Si vous ne spécifiez pas l’option */nu*, la fenêtre Windows Update s’ouvrira à la fin de l’installation pour rechercher des mises à jour.

L’agent apparaîtra dans la liste des programmes installés. Pour afficher la liste des programmes installés, cliquez sur **Panneau de configuration** > **Programmes** > **Programmes et fonctionnalités**.

![Agent installé](./media/backup-dpm-automation/installed-agent-listing.png)

#### Options d’installation
Pour afficher toutes les options disponibles via la ligne de commande, utilisez la commande suivante :

```
PS C:> MARSAgentInstaller.exe /?
```

Les options disponibles incluent :

| Option | Détails | Default |
| ---- | ----- | ----- |
| /q | Installation silencieuse | - | | /p:"emplacement" | Chemin du dossier d’installation de l’agent Azure Backup. | C:\Program Files\Microsoft Azure Recovery Services Agent | | /s:"emplacement" | Chemin du dossier du cache de l’agent Azure Backup. | C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch | | /m | Abonnement à Microsoft Update | - | | /nu | Ne pas rechercher les mises à jour après l’installation | - | | /d | Désinstalle Microsoft Azure Recovery Services Agent | - | | /ph | Adresse de l’hôte proxy | - | | /po | Numéro de port de l’hôte proxy | - | | /pu | Nom d’utilisateur de l’hôte proxy | - | | /pw | Mot de passe du proxy | - |

### Inscription auprès du service Azure Backup
Avant de pouvoir vous inscrire auprès du service Azure Backup, vérifiez que les conditions préalables suivantes sont remplies : - Disposer d’un abonnement Azure valide - Créer un coffre de sauvegarde - Télécharger les informations d’identification du coffre et les stocker dans un emplacement pratique (par exemple *C:\Téléchargements*). Les informations d’identification du coffre peuvent également être renommées à votre convenance.

L’inscription de l’ordinateur auprès du coffre s’effectue l’aide de la cmdlet [Start-DPMCloudRegistration](https://technet.microsoft.com/library/jj612787) :

```
PS C:> Start-DPMCloudRegistration -DPMServerName "TestingServer" -VaultCredentialsFilePath "C:\DPMTESTVault_Friday, September 5, 2014.VaultCredentials"
```

Cette opération inscrira le serveur DPM intitulé « TestingServer » auprès de Microsoft Azure Vault à l’aide des informations d’identification de coffre situées dans *C:*.

> [AZURE.IMPORTANT]N’utilisez pas de chemins relatifs pour spécifier le fichier des informations d’identification du coffre. Vous devez fournir un chemin absolu dans la cmdlet.

### Configuration des options de sauvegarde DPM (paramètres d’abonnement) pour la sauvegarde en ligne
Une fois le serveur DPM inscrit pour la protection en ligne, il sera défini avec les paramètres d’abonnement par défaut qui spécifient différentes options de sauvegarde. Si vous voulez modifier les paramètres d’abonnement, vous devez obtenir le paramètre et le modifier selon vos exigences.

#### Obtention des paramètres d’abonnement existants pour le serveur DPM
Pour configurer les paramètres d’abonnement en ligne DPM, vous devez d’abord obtenir les paramètres existants à l’aide de la cmdlet [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793.aspx) :

```
$Setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Vous devez exécuter cette commande pour obtenir l’objet de paramètre d’abonnement permettant de configurer différentes options, puis d’enregistrer les paramètres. L’exécution de la commande pour un nouveau serveur DPM renverra les paramètres par défaut.

#### Modification des paramètres d’abonnement
Vous pouvez configurer les paramètres suivants pour l’abonnement à la sauvegarde en ligne DPM à l’aide de la cmdlet [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) :

##### Zone de transit
L’agent Azure Backup en cours d’exécution sur le serveur DPM a besoin d’un stockage temporaire pour les données restaurées à partir du cloud (zone de transit locale). Vous pouvez utiliser la commande ci-dessous pour configurer la zone de transit. Cette commande définira la zone de transit « C:\StagingArea » pour **$setting**, mais la configuration n’est pas appliquée au serveur DPM tant que vous n’avez pas validé les paramètres.

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -StagingAreaPath "C:\StagingArea"
```

> [AZURE.NOTE]Assurez-vous que le dossier spécifié dans la commande ci-dessus existe déjà. Sinon, vous recevrez un message d’erreur lors de l’enregistrement des paramètres d’abonnement

##### Mise en réseau
L’ordinateur DPM se connecte à Internet via un serveur proxy. Les paramètres du proxy peuvent être fournis à l’agent. Dans cet exemple, il n’y a aucun serveur proxy. Nous effaçons donc explicitement toutes informations concernant un proxy.

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -NoProxy
```

L’utilisation de la bande passante peut également être contrôlée avec les options *bande passante d’heure travaillée* et *bande passante d’heure non travaillée*, certains jours de la semaine. Dans cet exemple, nous ne fixons aucune limitation.

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -NoThrottle
```

##### Paramètres de chiffrement
Les données sauvegardées envoyées à Azure Backup sont chiffrées pour garantir leur confidentialité. Le mot de passe du chiffrement est le « mot de passe » permettant de déchiffrer les données lors de la restauration.

> [AZURE.IMPORTANT]Conservez ces informations en lieu sûr après les avoir définies.

Dans l'exemple ci-dessous, la première commande convertit la chaîne de mot de passe 123456789 en une chaîne sécurisée et assigne la chaîne sécurisée à la variable nommée $Passphrase. La seconde commande définit la chaîne sécurisée dans $Passphrase en tant que mot de passe pour le chiffrement des sauvegardes

```
PS C:> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force
```

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -EncryptionPassphrase $Passphrase
```

#### Enregistrement des paramètres d’abonnement
Vous devez valider les changements apportés au serveur DPM à l’aide de l’option*–Commit*

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -Commit
```

> [AZURE.NOTE]Les paramètres ne seront pas appliqués tant qu’ils n’ont pas été validés.

## Protection des données vers Azure Backup
Les étapes suivantes vous expliquent comment ajouter un serveur de production à DPM puis comment protéger les données sur disque et en ligne vers Azure. Dans les exemples, nous vous montrerons comment sauvegarder des fichiers et des dossiers. La logique peut facilement être étendue pour sauvegarder toute source de données prise en charge. Au préalable, vous devez configurer et inscrire le serveur DPM pour la protection en ligne.

Toutes vos sauvegardes DPM sont régies par un groupe de protection (PG) en quatre parties

1. **Membres du groupe** est une liste de tous les objets protégeables que vous souhaitez protéger dans le même PG. Par exemple, vous pouvez protéger les machines virtuelles de production d’un PG et tester les machines virtuelles d’un autre PG car leurs exigences de sauvegarde sont différentes.
2. **Méthode de protection des données** sert à spécifier l’emplacement où vous souhaitez protéger les données. Dans cet exemple, nous allons protéger les données sur disque et effectuer une protection en ligne sur le cloud Azure.
3. Une **planification de sauvegarde** qui spécifie le moment où les sauvegardes doivent être effectuées et la fréquence de synchronisation des données entre la copie du serveur DPM et le serveur de production.
4. Une **planification de rétention** qui spécifie la durée de rétention des points de récupération dans Azure.

### Créer un groupe de protection et sauvegarder les données à intervalles planifiés
#### Ajout d'un serveur de production à un serveur DPM
Avant de pouvoir sauvegarder les données sur un serveur de production, vous devez vérifier que l'agent DPM est installé sur le serveur de production et géré par DPM. Veuillez suivre les étapes [ici](https://technet.microsoft.com/library/bb870935.aspx) pour installer l'agent DPM et le configurer pour la sauvegarde par le serveur DPM approprié.

#### Création d’un groupe de protection (PG)
Dans cet article, comme nous automatisons la sauvegarde, nous supposerons que rien n'a été configuré. Nous commençons par créer un nouveau PG à l’aide de la cmdlet [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722.aspx).

```
PS C:> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Avant d’apporter des modifications au PG, nous devons passer en mode de modification à l'aide de la cmdlet [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713.aspx).

```
PS C:> $MPG = Get-ModifiableProtectionGroup $PG
```

Les cmdlets ci-dessus créent un PG intitulé « ProtectGroup01 », mais elles n’effectuent aucune sauvegarde pour l’instant. Vous devez définir les éléments à sauvegarder, l’heure d’exécution des sauvegardes et l’emplacement de stockage des sauvegardes.

### Ajout de membres au groupe de protection
Il existe plusieurs étapes pour ajouter une source de données au PG.

#### Obtenir le serveur de production (PS) que vous souhaitez sauvegarder
Vous pouvez extraire tous les PS pour lesquels l'agent DPM est installé et géré par le serveur DPM à l'aide de la cmdlet[Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600.aspx). Pour cet exemple, nous allons filtrer et configurer uniquement un PS intitulé « productionserver01 » pour la sauvegarde.

```
PS C:> $PS = Get-ProductionServer -DPMServerName "TestingServer" |where {($_.servername) –contains “productionserver01”
```

### Lancer une recherche pour obtenir la liste des sources de données sur le PS
Vous pouvez lancer une recherche sur l'ordinateur PS afin d’obtenir une liste de toutes les sources de données que DPM peut sauvegarder à l'aide de la cmdlet [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605.aspx). Pour cet exemple, vous pouvez filtrer la source de données « D:\ » sur le PS pour lequel vous souhaitez configurer la protection.

```
PS C:> $DS = Get-Datasource -ProductionServer $PS -Inquire | where { $_.Name -contains “D:\” }
```

#### Ajout de la source de données pour la protection
Vous pouvez ajouter la source de données au PG « ProtectGroup01 » que nous avons créé dans l'exemple ci-dessus à l'aide de la cmdlet [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx)

```
PS C:> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

### Sélection de la méthode de protection des données
Une fois que vous avez ajouté la source de données au PG, vous devez spécifier la méthode de protection. Dans ce cas, nous configurons le PG pour une protection à court terme sur disque et à une protection à long terme sur bande.

```
PS C:> Set-DPMProtectionType -ProtectionGroup $PG -ShortTerm Disk –LongTerm Online
```

### Spécifier les objectifs de point de récupération
#### Définir la plage de rétention
Vous pouvez définir la plage de rétention du PG à l'aide de la cmdlet [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762.aspx). La commande ci-dessous définit la *plage de rétention* et la *fréquence de synchronisation* pour le point de récupération sur disque.

```
PS C:> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequency 360
```

Pour les points de récupération en ligne, vous pouvez configurer des plages de rétention pour différents schémas GFS (par exemple :quotidien, hebdomadaire, mensuel et annuel). Dans cet exemple, nous créons un objet avec différentes plages de rétention, puis configurons la plage de rétention en ligne à l'aide de l'objet comme indiqué dans l'exemple ci-dessous.

```
PS C:> $RRlist = @()
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

#### Définir des planifications de sauvegarde
DPM définit automatiquement des planifications par défaut si vous spécifiez l'objectif de protection à l'aide de la cmdlet **Set-PolicyObjective**. Pour modifier les planifications par défaut, utilisez la cmdlet [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749.aspx) suivie de la cmdlet [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723.aspx).

```
PS C:> $onlineSch=Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTermOnline
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
```

#### Création d’un réplica
Si vous sauvegardez la source de données pour la première fois, DPM doit créer un réplica initial qui crée une copie de la source de données à protéger sur le volume du réplica DPM. Vous pouvez planifier la création d'un réplica automatiquement à un moment donné, ou manuellement à l'aide de la cmdlet [Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715.aspx).

```
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

#### Validation du groupe de protection
Enfin, vous devez valider les modifications avant que DPM ne puisse effectuer la sauvegarde selon la configuration de PG à l'aide de la cmdlet [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758.aspx).

```
PS C:> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## Obtention d'une liste de tous les points de récupération en ligne
Vous pouvez utiliser la cmdlet [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746.aspx) pour obtenir une liste de tous les points de récupération d’une source de données. Dans cet exemple, nous allons : - extraire tous les PG sur le serveur DPM qui seront stockés dans un tableau $PG - obtenir les sources de données correspondant au tableau $PG [0] - obtenir tous les points de récupération d’une source de données.

```
PS C:> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## Restaurer des données protégées sur Azure
Dans cet exemple, nous allons vous montrer comment restaurer une machine virtuelle Hyper-V à partir d'un point de récupération en ligne, mais les commandes peuvent facilement être étendues à n'importe quel type de source de données.

Vous devez d'abord créer une option de récupération à l'aide de la cmldet [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592.aspx). Pour l'exemple ci-dessous, nous allons récupérer une source de données Hyper-V vers un autre emplacement.

```
PS C:> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “c:\VMRecovery”
```

Une fois que vous avez configuré l'option de récupération, vous allez restaurer le point de récupération en ligne que vous avez extrait à la section [Obtenir une liste de tous les points de récupération en ligne](#Getting-a-list-of-all-Online-Recovery-Points) ci-dessus.

```
PS C:> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints -RecoveryOption $RecoveryOption
```
## Étapes suivantes
Pour en savoir plus sur Azure Backup pour DPM, consultez la rubrique [Présentation d’Azure DPM Backup](backup-azure-dpm-introduction.md)

<!---HONumber=62-->