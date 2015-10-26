<properties
	pageTitle="Protéger les machines virtuelles d’un site VMM vers l’autre avec PowerShell et Microsoft Azure Resource Manager"
	description="Automatiser la protection entre deux site VMM locaux et Azure utilisant PowerShell et Azure Resource Manager."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="08/26/2015"
	ms.author="raynew"/>
	

#  Site VMM à site VMM avec PowerShell et Azure Resource Manager


## Vue d’ensemble

Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d'urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles dans divers scénarios de déploiement. Pour obtenir la liste complète des scénarios de déploiement, consultez la [Vue d’ensemble d’Azure Site Recovery](site-recovery-overview.md).

Azure PowerShell est un module fournissant des cmdlets pour gérer Azure via Windows PowerShell. Il peut fonctionner avec deux types de modules - le module Azure Profile, ou le module Azure Resource Manager (ARM).

Cet article décrit comment utiliser Windows PowerShell ® avec ARM pour déployer Azure Site Recovery et configurer et organiser la protection de machine virtuelle entre deux sites VMM. Des machines virtuelles s’exécutant sur les serveurs hôtes Hyper-V et situées dans des clouds privés VMM sur un site principal répliqueront et basculeront sur un site VMM secondaire en utilisant Hyper-V Replica.

Vous n’avez pas besoin d’être un expert de PowerShell pour utiliser cet article, mais vous devez tout de même connaître les concepts de base, tels que les modules, les applets de commande et les sessions. Pour plus d’informations sur Windows PowerShell, consultez [Prise en main d’Azure PowerShell](http://technet.microsoft.com/library/hh857337.aspx). - Apprenez-en davantage avec [Utilisation d’Azure PowerShell avec Azure Resource Manager](powershell-azure-resource-manager.md).

Cet article indique les conditions prérequises pour le scénario et montre comment configurer Azure PowerShell pour qu’il fonctionne avec Site Recovery, créer un Archivage Site Recovery, installer le fournisseur Azure Site Recovery sur les serveurs VMM source et cible, inscrire ces derniers dans le coffre, configurer les paramètres de protection des clouds VMM qui seront appliqués à toutes les machines virtuelles protégées, puis activer la protection pour ces machines virtuelles. Pour finir, vous pourrez tester le basculement pour vous assurer que tout fonctionne comme prévu.


## Avant de commencer

Assurez-vous que les conditions préalables sont remplies :

- Vous aurez besoin d’un compte [Microsoft Azure](http://azure.microsoft.com/). Vous aurez besoin d’un compte [Microsoft Azure](http://azure.microsoft.com/). Vous pouvez commencer avec une [version d'évaluation gratuite](pricing/free-trial/). Vous pouvez également consulter la [Tarification Azure Site Recovery Manager](http://azure.microsoft.com/pricing/details/site-recovery/).
- Vous aurez besoin d’un serveur VMM dans les sites principaux et secondaires s’exécutant sur System Center 2012 R2.
- Chaque serveur VMM doit avoir au moins un cloud qui contient :
	- un ou plusieurs groupes hôtes VMM ;
	- un ou plusieurs serveurs hôtes Hyper-V ou clusters dans chaque groupe hôte ;
	- une ou plusieurs machines virtuelles sur le serveur Hyper-V source.
	- Pour en savoir plus sur la configuration des clouds VMM :

		- [Nouveautés sur le cloud privé dans System Center 2012 R2 VMM](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=dfgvHAmYryA) et [VMM 2012 et les clouds](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html).
		- [Configuration de la structure de cloud VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
		- [Création d'un cloud privé dans VMM](https://technet.microsoft.com/library/jj860425.aspx) et [Procédure pas à pas : création de clouds privés avec System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).
- Un ou plusieurs serveurs Hyper-V exécutant au moins Windows Server 2012 avec le rôle Hyper-V et les dernières mises à jour doivent être installées. Le serveur ou le cluster doit être inclus dans un nuage VMM.
- Si vous utilisez Hyper-V dans un cluster, notez que le service Broker du cluster n'est pas créé automatiquement si vous avez un cluster basé sur des adresses IP statiques. Vous devez configurer manuellement le service Broker du cluster. Pour obtenir des instructions, consultez la section [Configurer le service Broker de réplication Hyper-V](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).

	- Vous aurez besoin d’Azure PowerShell. Assurez-vous que vous exécutez Azure PowerShell version 0.9.6 ou ultérieure. Lisez [Installation et configuration d’Azure PowerShell](powershell-install-configure.md). 
	- L’installation d’Azure PowerShell entraîne celle d’une console personnalisée. Vous pouvez exécuter les commandes PowerShell à partir de cette console ou de n’importe quel autre programme hôte, tels que Windows PowerShell ISE.

## Étape 1 : Configurer PowerShell


1. Ouvrez une console PowerShell et exécutez la commande suivante pour basculer sur le module ARM :

    `Switch-AzureMode AzureResourceManager`

3. Exécutez maintenant cette commande pour ajouter votre compte Azure à la session PowerShell. Les applets de commande vous invitent à saisir les informations d’identification de connexion de votre compte.

    `Add-AzureAccount`

	Notez que si vous êtes partenaire fournisseur de services cryptographiques travaillant pour le compte d’un client, vous devrez spécifier le client en tant que client lorsque vous ajoutez le compte Azure :

    `Add-AzureAccount-Tenant "customer"`

5. Un compte peut compter plusieurs abonnements et vous devez donc associer l’abonnement que vous souhaitez utiliser avec le compte.

    `Select-AzureSubscription -SubscriptionName $SubscriptionName`

6. Si vous utilisez des applets de commande de récupération de site pour la première fois dans l’abonnement, vous devez enregistrer le fournisseur de Site Recovery :

    `Register-AzureProvider -ProviderNamespace Microsoft.SiteRecovery`

## Étape 2 : Configurer l’archivage Site Recovery

1. Si vous ne disposez pas actuellement d’un coffre Site Recovery, vous devez en créer un en exécutant l’applet de commande [New-AzureSiteRecoveryVault](https://msdn.microsoft.com/library/azure/dn954225.aspx) :

    `New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
    $vault = Get-AzureSiteRecoveryVault -Name $VaultName;`

## Étape 3 : Génération d’une clé d'inscription du coffre

1. Exécuter l’applet de commande [Get-AzureSiteRecoveryVaultSettingsFile](https://msdn.microsoft.com/library/azure/dn850404.aspx) pour obtenir une clé d’inscription du coffre. Vous avez besoin de cette clé pour inscrire les serveurs VMM dans le coffre :

    `$VaultSettingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;`

## Étape 4 : Installation du fournisseur Azure Site Recovery

1. Téléchargez le fichier d’installation du fournisseur à partir de la page de démarrage rapide dans le coffre Site Recovery.
2. Sur chaque serveur VMM, créez un dossier à l’aide de cette commande :

    `pushd C:\ASR`

3. Exécutez cette commande pour extraire les fichiers du fournisseur téléchargé :

    `AzureSiteRecoveryProvider.exe /x:. /q`

4. Installez le fournisseur en exécutant :

    `.\SetupDr.exe /i` `$installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
	do
	{
                $isNotInstalled = $true;
                if(Test-Path $installationRegPath)
                {
                                $isNotInstalled = $false;
                }
	}While($isNotInstalled)`

5. Attendez la fin de l’installation, puis enregistrez le serveur dans le coffre :

    `$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
	pushd $BinPath
	$encryptionFilePath = "C:\temp"
	.\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice`

## Étape 5 : Définition du contexte de coffre

1. Exécutez l’applet de commande Get-AzureSiteRecoveryVault pour vous assurer que toutes les commandes s’exécutent sous le coffre spécifique :

    `$Vault = Get-AzureSiteRecoveryVault -ResouceGroupName $ResourceGroupName | where { $_.Name -eq $VaultName}`

2. Télécharger les paramètres de coffre :

    `$VaultSettingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Vault $Vault -Path $OutputPathForSettingsFile`

3. Pour vous assurer que les applets de commande s’exécutent sous le coffre, exécutez :

    `Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSetingsFile.FilePath`

## Étape 3 : Configuration des paramètres de protection de cloud

Une fois que le serveur VMM est inscrit dans le coffre, vous pouvez configurer les paramètres de protection de cloud qui seront appliquées à tous les ordinateurs virtuels sur les hôtes Hyper-V situés dans des clouds sur le serveur VMM inscrit.

1. Créer un conteneur dans le coffre pour les clouds principaux et secondaires :

    `$PrimaryContainer = Get-AzureSiteRecoveryProtectionContainer -FriendlyName  $PrimaryCloudName` `$$RecoveryContainer = Get-AzureSiteRecoveryProtectionContainer -FriendlyName  $RecoveryCloudName`

2. configurer les paramètres de protection à appliquer aux clouds :

    `New-AzureSiteRecoveryProtectionProfile -Name $ProtectionProfileName -ReplicationProvider HyperVReplica -ReplicationMethod Online -ReplicationFrequencyInSeconds 30 -RecoveryPoints 1 -ApplicationConsistentSnapshotFrequencyInHours 0 -ReplicationPort 8083 -Authentication Kerberos -AllowReplicaDeletion`

3. Démarrer une tâche afin d’associer les conteneurs de cloud et les paramètres de protection de cloud :

    `Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $ProtectionProfile -PrimaryProtectionContainer $PrimaryContainer -RecoveryProtectionContainer $RecoveryContainer`


## Étape 4 : Activation de la protection de machine virtuelle

Activer la protection des machines virtuelles dans les clouds VMM :

1. Accédez à la machine virtuelle que vous souhaitez protéger :

    `$VM = Get-AzureSiteRecoveryProtectionEntity -ProtectionContainer $PrimaryContainer -FriendlyName $VMName `

2. Activer la protection de la machine virtuelle :

    `Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $VM -Protection Enable`


## Étape 5 : Exécution d’un test de basculement

1.	Sélectionnez la machine virtuelle que vous souhaitez basculer :

    `$VM = Get-AzureSiteRecoveryProtectionEntity -ProtectionContainer $PrimaryContainer -FriendlyName  $VMName`

2. Exécution d’un test de basculement :

    `$ currentJob = Start-AzureSiteRecoveryTestFailoverJob -ProtectionEntity $VM -Direction PrimaryToRecovery`

3. Vérifiez que la machine virtuelle qui a basculé apparaît dans le site secondaire et achevez le basculement :

    `Resume-AzureSiteRecoveryJob -Id $currentJob.Name`


## Étapes suivantes

Pour les questions et les commentaires relatifs à ce scénario, visitez le [forum Site Recovery](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr/)

<!---HONumber=Oct15_HO3-->