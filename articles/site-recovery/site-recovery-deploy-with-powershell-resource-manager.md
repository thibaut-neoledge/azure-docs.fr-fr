<properties
	pageTitle="Protéger les serveurs dans Azure à l’aide d’Azure PowerShell avec Azure Resource Manager | Microsoft Azure"
	description="Automatiser la protection des serveurs dans Azure avec Azure Site Recovery à l’aide de PowerShell et d’Azure Resource Manager."
	services="site-recovery"
	documentationCenter=""
	authors="bsiva"
	manager="abhiag"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="03/16/2016"
	ms.author="bsiva"/>

# Réplication de machines virtuelles Hyper-V en local dans Azure avec PowerShell et Azure Resource Manager.

> [AZURE.SELECTOR]
- [Portail Azure](site-recovery-hyper-v-site-to-azure.md)
- [PowerShell - ARM](site-recovery-deploy-with-powershell-resource-manager.md)
- [Portail Classic](site-recovery-hyper-v-site-to-azure-classic.md)



## Vue d'ensemble

Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d’urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles dans divers scénarios de déploiement. Pour obtenir la liste complète des scénarios de déploiement, consultez la [Vue d’ensemble d’Azure Site Recovery](site-recovery-overview.md).

Azure PowerShell est un module fournissant des cmdlets pour gérer Azure via Windows PowerShell. Il peut fonctionner avec deux types de modules : le module Azure Profile ou le module Azure Resource Manager.

Les applets de commande PowerShell de Site Recovery, disponibles avec Azure PowerShell pour Azure Resource Manager, vous permettent de protéger et récupérer vos serveurs dans Azure.

Cet article explique comment utiliser Windows PowerShell avec Azure Resource Manager pour déployer Site Recovery afin de configurer et organiser la protection de serveurs dans Azure. L’exemple utilisé dans cet article décrit de quelle manière assurer la protection, le basculement et la récupération de machines virtuelles sur un hôte Hyper-V dans Azure à l’aide d’Azure PowerShell avec Azure Resource Manager.

> [AZURE.NOTE] Actuellement, les applets de commande PowerShell de Site Recovery permettent d’effectuer les configurations suivantes : un site Virtual Machine Manager vers un autre, un site Virtual Machine Manager vers Azure et un site Hyper-V vers Azure.

Vous n’avez pas besoin d’être un expert de PowerShell pour utiliser cet article, mais vous devez tout de même connaître les concepts de base, tels que les modules, les applets de commande et les sessions. Pour plus d'informations sur Windows PowerShell, consultez la page [Prise en main de Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Vous pouvez également lire l’article [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md) pour en savoir plus.

> [AZURE.NOTE] Les partenaires Microsoft membres du programme Cloud Solution Provider (CSP) peuvent configurer et gérer la protection des serveurs de leurs clients sur leurs abonnements CSP respectifs (abonnements des locataires).

## Avant de commencer

Assurez-vous que les conditions préalables sont remplies :

- Un compte [Microsoft Azure](https://azure.microsoft.com/). Vous pouvez commencer avec une [version d'évaluation gratuite](pricing/free-trial/). Vous pouvez aussi consulter la [Tarification Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
- Azure PowerShell 1.0. Pour plus d’informations sur cette version et la méthode d’installation, consultez [Azure PowerShell 1.0](https://azure.microsoft.com/).
- Les modules [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) et [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/). Vous pouvez obtenir les dernières versions de ces modules à partir de la [galerie PowerShell](https://www.powershellgallery.com/)

Cet article explique comment utiliser Azure PowerShell avec Azure Resource Manager pour configurer et gérer la protection de vos serveurs. L’exemple utilisé dans cet article vous explique comment protéger une machine virtuelle exécutée sur un hôte Hyper-V sur Azure. Les conditions préalables suivantes sont spécifiques à cet exemple. Pour obtenir un aperçu plus détaillé des conditions requises pour les différents scénarios Site Recovery, reportez-vous à la documentation se rapportant au scénario applicable.

- Un hôte Hyper-V exécuté sous Windows Server 2012 R2 et hébergeant une ou plusieurs machines virtuelles.
- Des serveurs Hyper-V connectés à Internet, directement ou via un proxy.
- Les machines virtuelles à protéger doivent répondre à la [configuration requise pour les machines virtuelles](site-recovery-best-practices.md#virtual-machines).


## Étape 1 : Connexion à votre compte Azure


1. Ouvrez une console PowerShell et exécutez la commande suivante pour vous connecter à votre compte Azure. L’applet de commande permet d’afficher une page web qui vous demandera les informations d’identification de votre compte.

    	Login-AzureRmAccount

	Vous pouvez également inclure les informations d’identification de votre compte à l’applet de commande `Login-AzureRmAccount` en tant que paramètre `-Credential`.

	Si vous êtes partenaire CSP travaillant pour le compte d’un locataire, spécifiez le client en tant que locataire à l’aide de son ID locataire ou de son nom de domaine principal.

		Login-AzureRmAccount -Tenant "fabrikam.com"

2. Un compte peut compter plusieurs abonnements et vous devez donc associer l’abonnement que vous souhaitez utiliser avec le compte.

    	Select-AzureRmSubscription -SubscriptionName $SubscriptionName

3.  Utilisez les commandes suivantes pour vérifier que votre abonnement vous autorise à utiliser les fournisseurs Azure pour Recovery Services et Site Recovery :

	- `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
	-  `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

	Si le paramètre **RegistrationState** est défini sur **Inscrit** dans la sortie de ces commandes, vous pouvez passer à l’étape 2. Dans le cas contraire, vous devez inscrire le fournisseur manquant dans votre abonnement.

	Pour inscrire le fournisseur Azure pour Site Recovery, exécutez la commande suivante :

    	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery

	De même, si vous utilisez les applets de commande Recovery Services pour la première fois dans votre abonnement, vous devez inscrire le fournisseur Azure pour Recovery Services. Avant de procéder, activez l’accès au fournisseur Recovery Services sur votre abonnement en exécutant la commande suivante :

		Register-AzureRmProviderFeature -FeatureName betaAccess -ProviderNamespace Microsoft.RecoveryServices

	>[AZURE.TIP] Après l’exécution de cette commande, vous devrez peut-être patienter jusqu’à une heure pour pouvoir activer l’accès au fournisseur Recovery Services sur votre abonnement. Les tentatives d’inscription du fournisseur Recovery Services dans votre abonnement à l’aide de la commande `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices` risquent d’échouer dans l’intervalle. Dans ce cas, patientez une heure avant d’effectuer une nouvelle tentative.

	Une fois l’accès au fournisseur Recovery Services activé sur votre abonnement, inscrivez le fournisseur dans votre abonnement en exécutant la commande suivante :

		Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

	Vérifiez que les fournisseurs ont été correctement inscrits à l’aide des commandes suivantes : `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` et `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`.



## Étape 2 : Configuration du coffre Recovery Services

1. Créez un groupe de ressources Azure Resource Manager dans lequel vous allez créer le coffre, ou utilisez un groupe de ressources existant. Vous pouvez créer un groupe de ressources en utilisant la commande suivante :

		New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

	où la variable $ResourceGroupName contient le nom du groupe de ressources que vous souhaitez créer et la variable $Geo contient la région Azure dans laquelle créer le groupe de ressources (par exemple, « Sud du Brésil »).

	Vous pouvez obtenir une liste des groupes de ressources de votre abonnement à l’aide de l’applet de commande `Get-AzureRmResourceGroup`.

2. Créez un coffre Azure Recovery Services de la manière suivante :

		$vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

	Vous pouvez récupérer la liste des coffres existants à l’aide de l’applet de commande `Get-AzureRmRecoveryServicesVault`.

> [AZURE.NOTE] Si vous souhaitez effectuer des opérations sur des coffres Site Recovery créés à l’aide de la version classique du portail ou du module Azure Service Management PowerShell, vous pouvez récupérer la liste de ces coffres à l’aide de l’applet de commande `Get-AzureRmSiteRecoveryVault`. Il est recommandé de créer un coffre Recovery Services pour toutes les nouvelles opérations. Les coffres Site Recovery que vous avez créés précédemment sont pris en charge mais n’offrent pas les fonctionnalités les plus récentes.

## Étape 3 : Définition du contexte du coffre Recovery Services

1.  Définissez le contexte du coffre en exécutant la commande suivante :

		Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## Étape 4 : Création d’un site Hyper-V et génération d’une nouvelle clé d’inscription de coffre pour le site.

1. Créez un nouveau site Hyper-V comme suit :

		$sitename = "MySite"                #Specify site friendly name
		New-AzureRmSiteRecoverySite -Name $sitename

	Cette applet de commande démarre une tâche Site Recovery pour créer le site et renvoie un objet de tâche Site Recovery. Attendez que la tâche soit terminée, puis vérifiez son exécution.

	Vous pouvez récupérer l’objet de tâche et vérifier ainsi l’état actuel de la tâche à l’aide de l’applet de commande Get-AzureRmSiteRecoveryJob.
2. Générez et téléchargez une clé d’inscription pour le site comme suit :

		$SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
		Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path

	Copiez la clé téléchargée sur l’hôte Hyper-V. Vous avez besoin de la clé pour inscrire l’hôte Hyper-V sur le site.

## Étape 5 : Installation du fournisseur Azure Site Recovery et de l’agent Azure Recovery Services sur votre hôte Hyper-V

1. Téléchargez le programme d’installation de la dernière version du fournisseur sur le site de [Microsoft](https://aka.ms/downloaddra).

2. Exécutez le programme d’installation sur votre ordinateur hôte Hyper-V. À la fin de l’installation, passez à l’étape d’inscription.

3. À l’invite, renseignez la clé d’inscription que vous avez téléchargée et terminez l’inscription de l’hôte Hyper-V sur le site.

4. Vérifiez que l’hôte Hyper-V a bien été inscrit sur le site à l’aide de la commande suivante :

		$server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## Étape 6 : Création d’une stratégie de réplication et association de cette stratégie au conteneur de protection

1. Créez une stratégie de réplication comme suit :

		$ReplicationFrequencyInSeconds = "300";    	#options are 30,300,900
		$PolicyName = “replicapolicy”
		$Recoverypoints = 6            		#specify the number of recovery points
		$storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

		$PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

	Vérifiez la tâche renvoyée pour vous assurer que la stratégie de réplication a bien été créée.

	>[AZURE.IMPORTANT] Le compte de stockage spécifié doit se trouver dans la même région Azure que votre coffre Recovery Services et doit utiliser la fonction de géo-réplication.
	>
	> - Si le compte de stockage Recovery Services spécifié est de type Azure Storage (Classic), le basculement des machines protégées aura pour effet de restaurer la machine dans Azure IaaS (Classic).
	> - Si le compte de stockage Recovery Services spécifié est de type Azure Storage (Azure Resource Manager), le basculement des machines protégées aura pour effet de restaurer la machine dans Azure IaaS (Azure Resource Manager).

2. Accédez au conteneur de protection correspondant au site comme suit :

		$protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3.	Lancez l’association du conteneur de protection avec la stratégie de réplication comme suit :

		$Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName
		$associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

	Attendez la fin de la tâche d’association et assurez-vous qu’elle a bien abouti.

##Étape 7 : Activation de la protection des machines virtuelles

1. Accédez à l’entité de protection correspondant à la machine virtuelle que vous souhaitez protéger comme suit :

		$VMFriendlyName = "Fabrikam-app"                    #Name of the VM
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

2. Commencez à protéger la machine virtuelle comme suit :

		$Ostype = "Windows"                                 # "Windows" or "Linux"
		$DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

	>[AZURE.IMPORTANT] Le compte de stockage spécifié doit se trouver dans la même région Azure que votre coffre Recovery Services et doit utiliser la fonction de géo-réplication.
	>
	> - Si le compte de stockage Recovery Services spécifié est de type Azure Storage (Classic), le basculement des machines protégées aura pour effet de restaurer la machine dans Azure IaaS (Classic).
	> - Si le compte de stockage Recovery Services spécifié est de type Azure Storage (Azure Resource Manager), le basculement des machines protégées aura pour effet de restaurer la machine dans Azure IaaS (Azure Resource Manager).

	> Si la machine virtuelle que vous protégez possède plusieurs disques, spécifiez le disque de système d’exploitation à l’aide du paramètre *OSDiskName*.

3. Attendez que les machines virtuelles basculent à l’état protégé après la réplication initiale. Cette opération prendra un certain temps compte tenu de certains facteurs, notamment la quantité de données à répliquer et la bande passante en amont disponible pour Azure. Les paramètres State et StateDescription de la tâche seront actualisés comme suit une fois la machine virtuelle protégée.

		PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

		PS C:\> $DRjob | Select-Object -ExpandProperty State
		Succeeded

		PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
		Completed

4. Mettez à jour les propriétés de récupération tels que la taille du rôle de machine virtuelle et le réseau Azure auquel attacher les cartes d’interface réseau de la machine virtuelle lors du basculement.

		PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

		PS C:\> $VMFriendlyName = "Fabrikam-App"

		PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

		PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

		PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

		PS C:\> $UpdateJob


		Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
		ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
		                   s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
		Type             : Microsoft.RecoveryServices/vaults/replicationJobs
		JobType          : UpdateVmProperties
		DisplayName      : Update the virtual machine
		ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
		State            : Succeeded
		StateDescription : Completed
		StartTime        : 10-12-2015 17:55:53 +00:00
		EndTime          : 10-12-2015 17:55:54 +00:00
		TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
		TargetObjectType : ProtectionEntity
		TargetObjectName : Fabrikam-App
		AllowedActions   : {Restart}
		Tasks            : {UpdateVmPropertiesTask}
		Errors           : {}



## Étape 8 : Exécution d’un test de basculement

1. Exécutez une tâche de test de basculement, en procédant comme suit :

		$nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

		$TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id

2. Vérifiez que la machine virtuelle de test est bien créée dans Azure. (La tâche de test de basculement est suspendue après la création de la machine virtuelle de test dans Azure. La tâche se termine en effaçant les artefacts créés lors de la reprise de la tâche, comme illustré à l’étape suivante.)

3. Terminez le test de basculement en procédant comme suit :

    	$TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

<!---HONumber=AcomDC_0518_2016-->