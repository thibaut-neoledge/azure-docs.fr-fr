<properties
	pageTitle="Automatisation de la protection entre un site local VMM et Azure à l’aide de PowerShell"
	description="Automatisation du déploiement d'Azure Site Recovery à l'aide de PowerShell."
	services="site-recovery"
	documentationCenter=""
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/08/2015"
	ms.author="lauraa"/>

#  Déploiement d'Azure Site Recovery avec PowerShell
Windows PowerShell® est un interpréteur de ligne de commande et un langage de script basé sur des tâches, conçu spécialement pour l'administration système. L'utilisation d'applets de commande PowerShell pour Azure Site Recovery est prise en charge entre un site Hyper-V géré par VMM et Azure.

## Vue d'ensemble

Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d'urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles dans divers scénarios de déploiement. Pour obtenir la liste complète des scénarios de déploiement, consultez la [Vue d'ensemble d'Azure Site Recovery](site-recovery-overview.md).

Cet article vous montre comment utiliser PowerShell pour automatiser les tâches courantes de déploiement d'Azure Site Recovery, y compris l’orchestration et l’automatisation de la protection des charges de travail exécutées sur des machines virtuelles, sur des serveurs hôtes Hyper-V situés dans des clouds privés VMM. Dans ce scénario, les machines virtuelles sont répliquées d'un site VMM principal vers Azure à l'aide de Hyper-V Replica.

L’article indique les conditions prérequises pour le scénario et montre comment configurer un coffre Récupération de sites, installer le fournisseur Azure Site Recovery sur le serveur VMM source, inscrire le serveur dans le coffre, ajouter un compte de stockage Azure, installer l'agent Azure Recovery Services sur les serveurs hôtes Hyper-V, configurer les paramètres de protection des clouds VMM qui seront appliqués à toutes les machines virtuelles protégées, puis activer la protection pour ces machines virtuelles. Pour finir, vous pourrez tester le basculement pour vous assurer que tout fonctionne comme prévu.

Si vous rencontrez des problèmes pour mettre en œuvre ce scénario, posez vos questions sur le [Forum Azure Recovery Services](http://go.microsoft.com/fwlink/?LinkId=313628).


## Avant de commencer

Assurez-vous que les conditions préalables sont remplies :
### Conditions préalables pour Azure

- Vous aurez besoin d’un compte [Microsoft Azure](http://azure.microsoft.com/). Si vous n'en possédez pas, commencez avec une [version d'évaluation gratuite](http://aka.ms/try-azure). Vous pouvez aussi consulter la [Tarification Azure Site Recovery Manager](http://go.microsoft.com/fwlink/?LinkId=378268).
- Vous aurez besoin d'un compte de stockage Azure pour stocker les données répliquées dans Azure. La géo-réplication doit être activée pour ce compte. Ce dernier doit se trouver dans la même région que le service Azure Site Recovery et être associé au même abonnement. Pour en savoir plus sur la configuration du stockage Azure, consultez la page [Introduction à Microsoft Azure Storage](http://go.microsoft.com/fwlink/?LinkId=398704).
- Vous devez vous assurer que les machines virtuelles que vous souhaitez protéger sont conformes aux exigences Azure. Pour plus d'informations, consultez la section [Prise en charge des machines virtuelles](https://msdn.microsoft.com/library/azure/dn469078.aspx#BKMK_E2A).

### Configuration requise pour VMM
- Vous aurez besoin d'un serveur VMM exécuté sur System Center 2012 R2.
- Tout serveur VMM contenant des machines virtuelles à protéger doit exécuter le fournisseur Azure Site Recovery. Celui-ci est installé lors du déploiement d'Azure Site Recovery.
- Vous aurez besoin d'au moins un cloud sur le serveur VMM que vous souhaitez protéger. Le coud doit contenir :
	- un ou plusieurs groupes hôtes VMM ;
	- un ou plusieurs serveurs hôtes Hyper-V ou clusters dans chaque groupe hôte ;
	- une ou plusieurs machines virtuelles sur le serveur Hyper-V source. Les machines virtuelles doivent être de la génération 1.
- Pour en savoir plus sur la configuration des clouds VMM :
	- Lisez la documentation sur les clouds privés VMM dans les sections [Nouveautés sur le cloud privé dans System Center 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) et [VMM 2012 et les clouds](http://go.microsoft.com/fwlink/?LinkId=324956).
	- En savoir plus sur la [Configuration de l'infrastructure de cloud VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
	- Une fois vos éléments d'infrastructure de cloud en place, consultez la documentation sur la création de clouds privés dans les sections [Création d'un cloud privé dans VMM](http://go.microsoft.com/fwlink/?LinkId=324953) et [Procédure pas à pas : création de clouds privés avec System Center 2012 SP1 VMM](http://go.microsoft.com/fwlink/?LinkId=324954).

### Conditions préalables liées à Hyper-V

- Les serveurs hôtes Hyper-V doivent exécuter au moins Windows Server 2012 avec le rôle Hyper-V et les dernières mises à jour doivent être installées.
- Si vous utilisez Hyper-V dans un cluster, notez que le service Broker du cluster n'est pas créé automatiquement si vous avez un cluster basé sur des adresses IP statiques. Vous devez configurer manuellement le service Broker du cluster. Pour obtenir des instructions, consultez la section [Configurer le service Broker de réplication Hyper-V](http://go.microsoft.com/fwlink/?LinkId=403937).
- Tout cluster ou serveur hôte Hyper-V pour lequel vous souhaitez gérer la protection doit être inclus dans un cloud VMM.

### Conditions préalables liées au mappage réseau
Quand vous protégez des machines virtuelles dans Azure, le mappage réseau effectue un mappage entre les réseaux de machines virtuelles sur le serveur VMM source et les réseaux Azure cibles pour permettre ce qui suit :

- Toutes les machines qui basculent sur le même réseau peuvent se connecter les unes aux autres, quel que soit le plan de récupération auquel elles appartiennent.
- Si une passerelle réseau est configurée sur le réseau Azure cible, les machines virtuelles peuvent se connecter à d'autres machines virtuelles locales.
- Si vous ne configurez pas le mappage réseau, seules les machines virtuelles qui basculent dans le même plan de récupération pourront se connecter les unes aux autres après le basculement vers Azure.

Si vous souhaitez déployer le mappage réseau, les conditions suivantes doivent être remplies :

- Les machines virtuelles que vous souhaitez protéger sur le serveur VMM source doivent être connectées à un réseau de machines virtuelles. Ce réseau doit être lié à un réseau logique lui-même associé au cloud.
- Un réseau Azure auquel les machines virtuelles répliquées peuvent se connecter après le basculement. Vous sélectionnerez ce réseau au moment du basculement. Le réseau doit être dans la même région que votre abonnement Azure Site Recovery.
- En savoir plus sur le mappage réseau :
	- [Configuration de la mise en réseau logique dans VMM](http://go.microsoft.com/fwlink/?LinkId=386307)
	- [Configuration de réseaux de machines virtuelles et de passerelles dans VMM](http://go.microsoft.com/fwlink/?LinkId=386308)
	- [Configurer et analyser des réseaux virtuels dans Azure](http://go.microsoft.com/fwlink/?LinkId=402555)

###Conditions préalables pour PowerShell
Assurez-vous qu’Azure PowerShell est prêt à l’emploi. Si vous utilisez déjà PowerShell, vous devrez passer à la version 0.8.10 ou ultérieure. Pour plus d’informations sur la configuration de PowerShell, consultez la section [Installation et configuration d’Azure PowerShell](powershell-install-configure.md). Une fois PowerShell configuré, vous pouvez afficher toutes les applets de commande disponibles pour le service [ici](https://msdn.microsoft.com/library/dn850420.aspx).

Pour obtenir des conseils sur l’utilisation des applets de commande, par exemple la façon dont les valeurs de paramètres, les entrées et les sorties sont gérées dans Azure PowerShell, consultez la section [Prise en main des applets de commande Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## Étape 1 : Définition de l’abonnement 

Dans PowerShell, exécutez ces applets de commande :



			$UserName = "<user@live.com>"
	$Password = "<password>"
	$AzureSubscriptionName = "prod_sub1"

	$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
	$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $securePassword
	Add-AzureAccount -Credential $Cred;
	$AzureSubscription = Select-AzureSubscription -SubscriptionName $AzureSubscriptionName


Remplacez les éléments entre « < > » par vos informations spécifiques.

## Étape 2 : Création d’un coffre Site Recovery

Dans PowerShell, remplacez les éléments entre « < > » par vos informations spécifiques, et exécutez ces commandes :

```

	$VaultName = "<testvault123>"
	$VaultGeo  = "<Southeast Asia>"
	$OutputPathForSettingsFile = "<c:>"

```


```
	New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
	$vault = Get-AzureSiteRecoveryVault -Name $VaultName;

```

## Étape 3 : Génération d’une clé d'inscription du coffre

Générez une clé d'inscription dans le coffre. Une fois que vous aurez téléchargé et installé le fournisseur Azure Site Recovery sur le serveur VMM, vous utiliserez cette clé pour inscrire le serveur VMM dans le coffre.

1.	Récupérez le fichier de configuration du coffre et définissez le contexte :
	
	```
	
		$VaultName = "<testvault123>"
		$VaultGeo  = "<Southeast Asia>"
		$OutputPathForSettingsFile = "<c:>"
	
		$VaultSetingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;
	
	```
	
2.	Définissez le contexte du coffre en exécutant les commandes suivantes :
	
	```	
		$VaultSettingFilePath = $vaultSetingsFile.FilePath 
		$VaultContext = Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSettingFilePath -ErrorAction Stop
```

## Étape 4 : Installation du fournisseur Azure Site Recovery

1.	Sur la machine VMM, créez un répertoire en exécutant la commande suivante :
	
	```
	
		pushd C:\ASR\
	
	```
	
2. Extrayez les fichiers à l'aide du fournisseur téléchargé en exécutant la commande suivante
	
	```
	
		AzureSiteRecoveryProvider.exe /x:. /q
	
	```
	
3. Installez le fournisseur à l’aide de la commande suivante :
	
	```
	
	.\SetupDr.exe /i
	
	```
	
	```
	
	$installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
	do
	{
	                $isNotInstalled = $true;
	                if(Test-Path $installationRegPath)
	                {
	                                $isNotInstalled = $false;
	                }
	}While($isNotInstalled)
	
	```
	
	Attendez que l'installation se termine.
	
4. Inscrivez le serveur dans le coffre à l'aide de la commande suivante :
	
	```
	
	$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
	pushd $BinPath
	$encryptionFilePath = "C:\temp"
	.\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
	
	```
	
## Étape 5 : Création d’un compte de stockage Azure

Si vous n'avez pas de compte de stockage Azure, créez un compte avec activation de la géo-réplication en exécutant la commande suivante :

```

$StorageAccountName = "teststorageacc1"
$StorageAccountGeo  = "Southeast Asia"

New-AzureStorageAccount -StorageAccountName $StorageAccountName -Label $StorageAccountName -Location $StorageAccountGeo;

```

Le compte de stockage doit se trouver dans la même région que le service Azure Site Recovery et être associé au même abonnement.


## Étape 6 : Installation de l'agent Azure Recovery Services

À partir du portail Azure, installez l'agent Azure Recovery Services sur chaque serveur hôte Hyper-V situé dans les clouds VMM que vous souhaitez protéger.

Exécutez la commande suivante sur l’ensemble des hôtes VMM :

```

	marsagentinstaller.exe /q /nu

```


## Étape 7 : Configuration des paramètres de protection de cloud

1.	Créez un profil de protection cloud pour Azure en exécutant la commande suivante :
	
	```
	
	$ReplicationFrequencyInSeconds = "300";
	$ProfileResult = New-AzureSiteRecoveryProtectionProfileObject -ReplicationProvider 	HyperVReplica -RecoveryAzureSubscription $AzureSubscriptionName `
	-RecoveryAzureStorageAccount $StorageAccountName -ReplicationFrequencyInSeconds 	$ReplicationFrequencyInSeconds;
	
	```
	
2.	Récupérez un conteneur de protection en exécutant les commandes suivantes :
	
	```
	
		$PrimaryCloud = "testcloud"
		$protectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $PrimaryCloud;	
	
	```
	
3.	Lancez l'association du conteneur de protection avec le cloud :
	
	```
	
		$associationJob = Start-AzureSiteRecoveryProtectionProfileAssociationJob -	ProtectionProfile $profileResult -PrimaryProtectionContainer $protectionContainer;		
	
	```
	
4.	Une fois la tâche terminée, exécutez la commande suivante :

			$job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
			if($job -eq $null -or $job.StateDescription -ne "Completed")
			{
				$isJobLeftForProcessing = $true;
			}
5. Une fois la tâche terminée, exécutez la commande suivante :

		if($isJobLeftForProcessing)
			{
			Start-Sleep -Seconds 60
			}
				}While($isJobLeftForProcessing)
	
	
Pour vérifier que l'opération est terminée, suivez les étapes décrites dans la section [Suivi de l'activité](#monitor).

## Étape 8 : Configuration du mappage réseau
Avant de commencer le mappage réseau, vérifiez que les machines virtuelles sur le serveur VMM source sont connectées à un réseau de machines virtuelles. En outre, vous devez créer un ou plusieurs réseaux virtuels Azure. Notez que plusieurs réseaux de machines virtuelles peuvent être mappés à un seul réseau Azure.

Notez que si le réseau cible a plusieurs sous-réseaux et que l'un d'entre eux a le même nom que le sous-réseau où se trouve la machine virtuelle source, la machine virtuelle de réplication sera connectée à ce sous-réseau cible après le basculement. S’il n’existe aucun sous-réseau cible avec un nom correspondant, la machine virtuelle sera connectée au premier sous-réseau du réseau.

La première commande récupère les serveurs pour le coffre Azure Site Recovery actuel. La commande stocke les serveurs Microsoft Azure Site Recovery dans la variable tableau $Servers.



	$Servers = Get-AzureSiteRecoveryServer


La deuxième commande récupère le réseau Site Recovery pour le premier serveur du tableau $Servers. La commande stocke les réseaux dans la variable $Networks.

```

	$Networks = Get-AzureSiteRecoveryNetwork -Server $Servers[0]

```

La troisième commande extrait vos abonnements Azure à l'aide de l'applet de commande Get-AzureSubscription, puis stocke cette valeur dans la variable $Subscriptions.

```

	$Subscriptions = Get-AzureSubscription

```

La quatrième commande récupère les réseaux virtuels Azure à l'aide de l'applet de commande Get-AzureVNetSite, puis stocke cette valeur dans la variable $AzureVmNetworks.

```

	$AzureVmNetworks = Get-AzureVNetSite

```

L'applet de commande finale crée un mappage entre le réseau principal et le réseau de la machine virtuelle Azure. L'applet de commande fixe le réseau principal comme premier élément de $Networks. L'applet de commande fixe un réseau de machine virtuelle comme premier élément de $AzureVmNetworks à l'aide de son ID. La commande inclut l’identifiant de votre abonnement Azure.

```

PS C:> New-AzureSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureSubscriptionId $Subscriptions[0].SubscriptionId -AzureVMNetworkId $AzureVmNetworks[0].Id

```

## Étape 9 : Activation de la protection des machines virtuelles

Dès lors que les serveurs, les clouds et les réseaux ont été configurés correctement, vous pouvez activer la protection pour les machines virtuelles du cloud. Notez les points suivants :

Les machines virtuelles doivent répondre aux exigences liées à Azure. Pour en savoir plus sur ces dernières, consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=402602">Configuration requise et prise en charge</a> dans le guide de planification.

Pour activer la protection, vous devez définir les propriétés du système d'exploitation et du disque du système d'exploitation pour la machine virtuelle. Lorsque vous créez une machine virtuelle dans VMM à l'aide d'un modèle de machine virtuelle, vous pouvez définir la propriété. Vous pouvez également définir ces propriétés pour des machines virtuelles existantes dans les onglets **Général** et **Configuration matérielle** des propriétés de la machine virtuelle. Si vous ne définissez pas ces propriétés dans VMM, vous pourrez les configurer dans le portail Azure Site Recovery.


	
1.	Afin d’activer la protection, exécutez la commande suivante pour récupérer le conteneur de protection :
		
	```
	
	$ProtectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $CloudName
	
	```
	
2. Récupérez l’entité de protection (machine virtuelle) en exécutant les commandes suivantes :
		
	```
	
	$protectionEntity = Get-AzureSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer
		
		```
			
3. Activez la récupération d’urgence de la machine virtuelle en exécutant la commande suivante :

	
	$jobResult = Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Protection Enable -Force
	

## Tester votre déploiement

Pour tester votre déploiement, vous pouvez exécuter un test de basculement pour une seule machine virtuelle, ou créer un plan de récupération comportant plusieurs machines virtuelles et exécuter sur lui un test de basculement. Il simule votre mécanisme de basculement et de récupération dans un réseau isolé. Notez les points suivants :

- Si vous voulez vous connecter à la machine virtuelle dans Azure avec le Bureau à distance après le basculement, activez Connexion Bureau à distance sur la machine virtuelle avant d’exécuter le test de basculement.
- Après le basculement, vous utiliserez une adresse IP publique pour vous connecter à la machine virtuelle dans Azure avec le Bureau à distance. Dans ce cas, assurez-vous qu'aucune de vos stratégies de domaine ne vous empêche de vous connecter à une machine virtuelle avec une adresse publique.

Pour vérifier que l'opération est terminée, suivez les étapes décrites dans la section [Suivi de l'activité](#monitor).

### Créer un plan de récupération

1. Créez un fichier .xml comme modèle pour votre plan de récupération en utilisant les données ci-dessous, puis enregistrez-le sous « C:\\RPTemplatePath.xml ».
2. Modifiez l'Id, Name, PrimaryServerId et SecondaryServerId du nœud RecoveryPlan.
3. Modifiez le PrimaryProtectionEntityId du nœud ProtectionEntity (vmid pour VMM).
4. Vous pouvez ajouter davantage de machines virtuelles en ajoutant des nœuds ProtectionEntity.
	
	```
	
	<#
	<?xml version="1.0" encoding="utf-16"?>
	<RecoveryPlan Id="d0323b26-5be2-471b-addc-0a8742796610" Name="rp-test" 	PrimaryServerId="9350a530-d5af-435b-9f2b-b941b5d9fcd5" 	SecondaryServerId="21a9403c-6ec1-44f2-b744-b4e50b792387" Description="" 	Version="V2014_07">
	  <Actions />
	  <ActionGroups>
	    <ShutdownAllActionGroup Id="ShutdownAllActionGroup">
	      <PreActionSequence />
	      <PostActionSequence />
	    </ShutdownAllActionGroup>
	    <FailoverAllActionGroup Id="FailoverAllActionGroup">
	      <PreActionSequence />
	      <PostActionSequence />
	    </FailoverAllActionGroup>
	    <BootActionGroup Id="DefaultActionGroup">
	      <PreActionSequence />
	      <PostActionSequence />
	      <ProtectionEntity PrimaryProtectionEntityId="d4c8ce92-a613-4c63-9b03-	cf163cc36ef8" />
	    </BootActionGroup>
	  </ActionGroups>
	  <ActionGroupSequence>
	    <ActionGroup Id="ShutdownAllActionGroup" ActionId="ShutdownAllActionGroup" 	Before="FailoverAllActionGroup" />
	    <ActionGroup Id="FailoverAllActionGroup" ActionId="FailoverAllActionGroup" 	After="ShutdownAllActionGroup" Before="DefaultActionGroup" />
	    <ActionGroup Id="DefaultActionGroup" ActionId="DefaultActionGroup" After="FailoverAllActionGroup"/>
	  </ActionGroupSequence>
	</RecoveryPlan>
	#>
	
	```
	
4. Remplissez les données du modèle :
	
	```
	
	$TemplatePath = "C:\RPTemplatePath.xml";
	
	```
	
5. Créez le RecoveryPlan :
	
	```
	
		$RPCreationJob = New-AzureSiteRecoveryRecoveryPlan -File $TemplatePath -WaitForCompletion;
	
	```
	
### Exécution d’un test de basculement

1. Récupérez l'objet RecoveryPlan en exécutant la commande suivante :
	
	```
	
		$RPObject = Get-AzureSiteRecoveryRecoveryPlan -Name $RPName;
	
	```
	
2. Lancez le test de basculement en exécutant la commande suivante :
	
	```
	
	$jobIDResult = Start-AzureSiteRecoveryTestFailoverJob -RecoveryPlan $RPObject -Direction PrimaryToRecovery;
	
	```
	
## <a name=monitor></a> Suivi de l'activité

Utilisez les commandes suivantes pour suivre l’activité. Vous devez attendre la fin du traitement entre les tâches.

```

Do
{
                $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
                Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
                if($job -eq $null -or $job.StateDescription -ne "Completed")
                {
                                $isJobLeftForProcessing = $true;
                }

```

```

if($isJobLeftForProcessing)
                {
                                Start-Sleep -Seconds 60
                }
}While($isJobLeftForProcessing)

```


##<a id="next" name="next" href="#next"></a>Étapes suivantes
<UL>

<LI>Pour plus d'informations sur les applets de commande PowerShell d’Azure Site Recovery, consultez l'article<a href="https://msdn.microsoft.com/library/dn850420.aspx"> ici</a>.

<LI>Pour planifier et déployer Azure Site Recovery dans un environnement de production complet, consultez le <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Guide de planification d'Azure Site Recovery</a> et le <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Guide de déploiement d'Azure Site Recovery</a>.</LI>

<LI>Pour toute question, visitez le <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Forum Azure Recovery Services</a>.</LI></UL>

<!---HONumber=July15_HO2-->