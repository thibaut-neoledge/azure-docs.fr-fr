<properties 
	pageTitle="Récupération d'urgence avec SQL Server et Azure Site Recovery" 
	description="Azure Site Recovery coordonne la réplication, le basculement et la récupération de SQL Server sur un site local secondaire ou sur Azure." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/09/2015" 
	ms.author="raynew"/>


# Récupération d'urgence avec SQL Server et Azure Site Recovery 

Azure Site Recovery est un service Azure qui met en œuvre la stratégie de continuité des activités et de récupération d'urgence (ou BCDR pour Business Continuity and Disaster Recovery) de votre entreprise en coordonnant la réplication, le basculement et la récupération d’ordinateurs virtuels et de serveurs physiques. Site Recovery prend en charge plusieurs mécanismes de réplication qui protègent de manière cohérente la réplication et le basculement d’ordinateurs vers Azure ou un centre de données secondaire. Pour une présentation de tous les scénarios de déploiement, consultez [Présentation d’Azure Site Recovery](site-recovery-overview.md).

 Cet article décrit comment protéger le serveur SQL Server principal d'une application en combinant les technologies BCDR de SQL Server et Azure Site Recovery. Vous devez avoir une bonne compréhension des fonctionnalités BCDR de SQL Server BCDR (cluster de basculement, groupes de disponibilité AlwaysOn, mise en miroir de la base de données, envoi de journaux) et d’Azure Site Recovery avant de déployer les scénarios décrits dans cet article.



## Vue d'ensemble

De nombreuses charges de travail utilisent SQL Server comme base. Les applications SharePoint, Dynamics et SAP utilisent SQL Server pour mettre en œuvre des services de données. Les fonctionnalités de haute disponibilité et de récupération d'urgence de SQL Server, comme les groupes de disponibilité SQL Server, sont utilisées pour protéger et récupérer des bases de données SQL Server. Déploiement d'applications SQL Server dans les configurations suivantes


1. SQL Server autonome : le serveur SQL et toutes les bases de données sont hébergés sur un seul ordinateur (physique ou une machine virtuelle). Lorsqu'il est virtualisé, le clustering d'hôtes est utilisé pour la haute disponibilité (HA) locale. Aucune haute disponibilité de niveau Invité n'est mise en œuvre. 
2.	Les instances de Clustering de basculement SQL Server (également appelées Always ON FC) : dans cette configuration, 2 ou plusieurs nœuds d'instances SQL Server avec des disques partagés sont configurés dans un cluster de basculement Windows. Si l'une des instances de cluster de basculement SQL est défaillante, le cluster peut basculer vers une autre instance SQL. Ce paramétrage est généralement utilisé pour la haute disponibilité sur le site principal. Cela ne protège pas contre la défaillance ou une panne dans la couche de stockage partagé. Le disque partagé peut être mis en œuvre à l'aide d'iSCSI, de Fibre Channel ou de VHDx partagé.
3.	Groupes de disponibilité SQL Always ON : dans ce cas de figure, 2 nœuds sont configurés dans un cluster sans partage avec des bases de données SQL Server configurées dans un groupe de disponibilité avec réplication synchrone et basculement automatique.

SQL Server fournit également des technologies de récupération d'urgence natives dans les éditions Enterprise pour la récupération des bases de données vers un site distant. Le cas échéant, nous exploiterons et intégrerons les technologies de récupération d'urgence SQL natives suivantes pour la création d'un plan de récupération d'urgence basé sur Azure Site Recovery


1. Groupe de disponibilité SQL Always à partir de la récupération d'urgence pour SQL éditions 2012 ou 2014 Enterprise 
2.	La mise en miroir de base de données SQL en mode haute sécurité pour SQL server standard (toute version) ou SQL server 2008 R2



La récupération de sites peut protéger le serveur SQL exécuté sur une machine virtuelle Hyper-V, une machine virtuelle VMware ou un serveur physique.

 |**Local vers local** | **Local vers Azure** 
---|---|---
**Hyper-V** | Oui | Oui
**VMware** | Oui | Oui 
**Serveur physique** | Oui | Oui


## Prise en charge et intégration

Les versions et éditions de SQL Server suivantes s'appliquent à cet article :


- SQL Server 2014 Enterprise et Standard
- SQL Server 2012 Enterprise et Standard
- SQL Server 2008 R2 Enterprise et Standard


La récupération de sites peut être intégrée aux technologies BCDR SQL Server natives résumées dans le tableau ci-dessous pour fournir une solution de récupération d'urgence.

**Fonctionnalité** |**Détails** | **Version de SQL Server** 
---|---|---
**Groupe de disponibilité AlwaysOn** | <p>Plusieurs instances autonomes de SQL Server s’exécutent dans un cluster de basculement qui comporte plusieurs nœuds.</p> <p>Les bases de données peuvent être regroupées dans des groupes de basculement qui peuvent être copiés (mis en miroir) sur des instances de SQL Server, pour éviter tout stockage partagé.</p> <p>Cette fonction assure une récupération d'urgence entre un site principal et un ou plusieurs sites secondaires. Il est possible de configurer deux nœuds dans un cluster sans partage avec les bases de données SQL Server configurées dans un groupe de disponibilité avec réplication synchrone et basculement automatique.</p> | Édition SQL Server 2014/2012 Enterprise
**Cluster de basculement (instance de cluster de basculement AlwaysOn)** | <p>SQL Server exploite les clusters de basculement Windows pour une haute disponibilité des charges de travail SQL Server locales. </p><p>Les nœuds qui exécutent des instances de SQL Server avec des disques partagés sont configurés dans un cluster de basculement. Si une instance est arrêtée, le cluster bascule vers un autre.</p> <p>Le cluster ne protège pas contre les défaillances ou les pannes en stockage partagé. Le disque partagé peut être implémenté avec iSCSI, Fibre Channel ou VHDX partagé.</p> | Éditions SQL Server Enterprise</p> <p> Éditions SQL Server Standard (limitée à deux nœuds)
**Mise en miroir de base de données (mode haute sécurité)** | Protège une base de données sur une seule copie secondaire. Disponible dans les modes de réplication haute sécurité (synchrone) et hautes performances (asynchrone). Cluster de basculement non requis. | <p>SQL Server 2008 R2</p><p>SQL Server Enterprise (toutes les éditions)</p>
**Serveur SQL autonome** | SQL Server et la base de données sont hébergés sur un seul serveur (physique ou virtuel). Le cluster hôte est utilisé pour la haute disponibilité, si le serveur est virtuel. Aucune haute disponibilité pour le niveau invité. | Édition Enterprise ou Standard




Le tableau suivant récapitule nos recommandations pour intégrer les technologies BCDR de SQL Server dans le déploiement d’Azure Site Recovery.

**Version** |**Édition** | **Déploiement** | **Local à local** | **Local vers Azure** 
---|---|---|---|---
SQL Server 2014 ou 2012 | Entreprise | Instance de cluster de basculement | Groupes de disponibilité AlwaysOn | Groupes de disponibilité AlwaysOn
 | Entreprise | Groupes de disponibilité AlwaysOn pour la haute disponibilité | Groupes de disponibilité AlwaysOn | Groupes de disponibilité AlwaysOn
 | Standard | Instance de cluster de basculement (FCI) | Réplication Site Recovery avec miroir local | Réplication Site Recovery avec miroir local
 | Enterprise ou Standard | Standalone | Réplication de la récupération de sites | Réplication de la récupération de sites 
SQL Server 2008 R2 | Enterprise ou Standard | Instance de cluster de basculement (FCI) | Réplication Site Recovery avec miroir local | Réplication Site Recovery avec miroir local
 | Enterprise ou Standard | Standalone | Réplication de la récupération de sites | Réplication de la récupération de sites
SQL Server (toute version) | Enterprise ou Standard | Instance de cluster de basculement - application DTC | Réplication de la récupération de sites | Non pris en charge

## Conditions préalables au déploiement

Voici ce dont vous avez besoin pour commencer :


- Un déploiement local de SQL Server exécutant une version prise en charge de SQL Server. En général, il faut également Active Directory pour votre serveur SQL.
- La configuration requise pour le scénario que vous souhaitez déployer. Vous trouverez le détail de la configuration requise dans chaque article de déploiement. Ces informations sont répertoriées dans [Présentation d’Azure Site Recovery](site-recovery-overview.md) avec des liens.
- Si vous souhaitez configurer la récupération dans Azure, vous devez exécuter l’outil [Azure Virtual Machine Readiness Assessment](http://www.microsoft.com/download/details.aspx?id=40898) sur vos ordinateurs virtuels SQL Server pour vous assurer qu'ils sont compatibles avec Azure et Site Recovery.


## Configurer la protection d'AD

Vous avez besoin d’Active Directory sur le site de récupération secondaire pour que SQL Server fonctionne correctement. Deux cas de figure peuvent se présenter :

- **Petite entreprise** : si vous avez un petit nombre d'applications et un seul contrôleur de domaine pour le site local et que vous souhaitez basculer l'ensemble du site, nous vous recommandons d'utiliser Site Recovery pour répliquer le contrôleur de domaine sur le centre de données secondaire ou Azure.

- **Moyenne ou grande entreprise** : si vous avez un grand nombre d'applications, que vous exécutez une forêt Active Directory et que vous souhaitez effectuer un basculement par application ou charge de travail, nous vous recommandons de configurer un contrôleur de domaine supplémentaire dans le centre de données secondaire ou dans Azure. Notez que si vous utilisez des groupes de disponibilité AlwaysOn pour effectuer une récupération sur un site distant, nous vous recommandons de configurer un contrôleur de domaine supplémentaire sur le site secondaire ou sur Azure, à utiliser pour l'instance SQL Server récupérée.

Les instructions fournies dans ce document supposent qu'un contrôleur de domaine est disponible sur l'emplacement secondaire. Vous pouvez vous référer au guide de la solution de récupération d'urgence AD DR solution guidance [ici](http://aka.ms/asr-ad).

##Configuration de la protection de groupes de disponibilité SQL AlwaysOn

### Local vers Azure

Azure Site Recovery (ASR) prend en charge SQL AlwaysOn en mode natif. Si vous avez créé un groupe de disponibilité SQL avec une machine virtuelle Azure configurée comme « Secondaire », vous pouvez utiliser ASR pour gérer le basculement des groupes de disponibilité.

Cette fonctionnalité est actuellement en version préliminaire et disponible lorsque le centre de données principal est géré par System Center Virtual Machine Manager (VMM).

#### Environnements gérés par le serveur VMM
Si vous accédez à l’intérieur d’un coffre ASR, vous devez voir un onglet pour les serveurs SQL sous l’onglet Éléments protégés.

![Éléments protégés](./media/site-recovery-sql/protected-items.png)

Voici la procédure permettant d’intégrer SQL AlwaysOn à ASR.

##### Configuration requise
- Un serveur SQL Server local sur un serveur autonome ou un cluster de basculement 
- Une ou plusieurs machines virtuelles Azure sur lesquelles est installé SQL Server.
- Groupe de disponibilité SQL configuré entre le serveur SQL Server local et celui exécuté dans Azure
- La communication à distance PowerShell doit être activée sur le serveur SQL Server local. Le serveur VMM doit pouvoir effectuer des appels PowerShell vers le serveur SQL Server.
- Sur le serveur SQL Server local, un compte d’utilisateur doit être ajouté dans les groupes d’utilisateurs SQL avec au minimum les autorisations suivantes.
	- MODIFIER LE GROUPE DE DISPONIBILITÉ - [référence 1](https://msdn.microsoft.com/fr-FR/library/hh231018.aspx), [référence 2](https://msdn.microsoft.com/fr-FR/library/ff878601.aspx#Anchor_3)
	- MODIFIER LA BASE DE DONNÉES - [référence 1](https://msdn.microsoft.com/fr-FR/library/ff877956.aspx#Security)
- Un compte d’identification doit être créé sur le serveur VMM du compte à l’étape précédente.
- Le module SQL PS doit être installé sur les serveurs SQL Server exécutés en local et sur des machines virtuelles Azure.
- L’agent de machine virtuelle doit être installé sur les machines virtuelles exécutées dans Azure.
- NTAUTHORITY\\System doit comporter les autorisations suivantes sur le serveur SQL Server exécuté sur les machines virtuelles dans Azure.
	- MODIFIER LE GROUPE DE DISPONIBILITÉ - [référence 1](https://msdn.microsoft.com/fr-FR/library/hh231018.aspx), [référence 2](https://msdn.microsoft.com/fr-FR/library/ff878601.aspx#Anchor_3)
	- MODIFIER LA BASE DE DONNÉES - [référence 1](https://msdn.microsoft.com/fr-FR/library/ff877956.aspx#Security)

##### Ajout d’un serveur SQL Server

Cliquez sur Ajouter un serveur SQL pour ajouter un nouveau serveur SQL Server.

![Ajouter un serveur SQL](./media/site-recovery-sql/add-sql.png)

Indiquez les informations relatives au serveur SQL Server et à VMM, ainsi que les informations d’identification à utiliser pour gérer le serveur SQL Server.

![Boîte de dialogue Ajouter un serveur SQL](./media/site-recovery-sql/add-sql-dialog.png)

###### Paramètres
1. Nom : nom convivial que vous voulez indiquer pour faire référence à ce serveur SQL Server
2. SQL Server (FQDN) : nom de domaine complet (FQDN) du serveur SQL Server source que vous souhaitez ajouter. Si le serveur SQL Server est installé sur un cluster de basculement, indiquez le nom de domaine complet du cluster et non celui des nœuds de cluster. 
3. Instance SQL Server : choisissez l’instance SQL par défaut ou indiquez le nom de l’instance SQL personnalisée.
4. Serveur VMM  sélectionnez l’un des serveurs VMM déjà inscrits auprès d’Azure Site Recovery (ASR). ASR utilisera ce serveur VMM pour communiquer avec le serveur SQL Server.
5. COMPTE D’IDENTIFICATION : indiquez le nom de l’un comptes d’identification créés sur le serveur VMM sélectionné ci-dessus. Ce compte d’identification sera utilisé pour accéder au serveur SQL Server et devra être doté des autorisations Lecture et Basculement sur les groupes de disponibilité présents sur le serveur SQL Server. 

Une fois ajouté, le serveur SQL Server doit apparaître sous l’onglet Serveurs SQL Server.

![Liste des serveurs SQL Server](./media/site-recovery-sql/sql-server-list.png)

##### Ajout d’un groupe de disponibilité SQL

Une fois le serveur SQL Server ajouté, l’étape suivante consiste à ajouter le groupe de disponibilité à ASR. Pour cela, descendez dans la hiérarchie du serveur SQL Server ajouté à l’étape précédente et cliquez sur Ajouter un groupe de disponibilité SQL.

![Ajouter un groupe de disponibilité SQL](./media/site-recovery-sql/add-sqlag.png)

Le groupe de disponibilité SQL peut répliquer sur une ou plusieurs machines virtuelles dans Azure. Lors de l’ajout d’un groupe de disponibilité SQL, vous devez fournir le nom et l’abonnement de la machine virtuelle Azure sur laquelle vous voulez que ASR bascule le groupe de disponibilité.

![Boîte de dialogue Ajouter un groupe de disponibilité SQL](./media/site-recovery-sql/add-sqlag-dialog.png)

Dans l’exemple ci-dessus, le groupe de disponibilité DB1-AG devient Principal sur la machine virtuelle SQLAGVM2 exécutée au sein de l’abonnement DevTesting2 sur un basculement.

>[AZURE.NOTE]Seuls les groupes de disponibilité ayant le statut Principal sur le serveur SQL Server ajouté à l’étape précédente peuvent être ajoutés à ASR. Si vous avez défini un groupe de disponibilité comme principal sur le serveur SQL Server ou si vous avez ajouté d’autres groupes de disponibilité au serveur SQL Server une fois celui-ci ajouté, actualisez-le à l’aide de l’option Actualiser figurant dans le serveur SQL Server.

#### Création d’un plan de récupération

L’étape suivante consiste à créer un plan de récupération à l’aide des machines virtuelles et des groupes de disponibilité. Sélectionnez le serveur VMM utilisé à l’étape 1 comme source, et Microsoft Azure comme cible.

![Créer un plan de récupération](./media/site-recovery-sql/create-rp1.png)

![Créer un plan de récupération](./media/site-recovery-sql/create-rp2.png)

Dans l’exemple, l’application Sharepoint est composée de 3 machines virtuelles qui utilisent un groupe de disponibilité SQL comme serveur principal. Dans ce plan de récupération, vous pouvez sélectionner à la fois le groupe de disponibilité et la machine virtuelle qui composent l’application.

Vous pouvez personnaliser davantage le plan de récupération en déplaçant les machines virtuelles vers d’autres groupes de basculement pour définir l’ordre du basculement. Le groupe de disponibilité est toujours basculé en premier, étant donné qu’il peut être utilisé comme serveur principal d’une application.

![Personnaliser un plan de récupération](./media/site-recovery-sql/customize-rp.png)

#### Basculement

Différentes options de basculement sont disponibles une fois le groupe de disponibilité ajouté à un plan de récupération.

##### Basculement planifié

Un basculement planifié implique un basculement sans perte de données. Pour cela, le mode de disponibilité du groupe de disponibilité SQL est d’abord défini sur Synchrone, puis un basculement est déclenché pour définir le groupe de disponibilité comme Principal sur la machine virtuelle fournie lors de l’ajout du groupe de disponibilité à ASR. Une fois le basculement terminé, le mode de disponibilité est défini sur la valeur qu’il avait avant le déclenchement du basculement planifié.

##### Basculement non planifié

Un basculement non planifié peut entraîner une perte de données. Lors du déclenchement du basculement non planifié, le mode de disponibilité du groupe de disponibilité n’est pas modifié. Il est défini sur primaire sur la machine virtuelle fournie lors de l’ajout du groupe de disponibilité à ASR. Une fois le basculement non planifié terminé, et le serveur local exécutant SQL Server de nouveau disponible, la réplication inverse doit être déclenchée sur le groupe de disponibilité. Notez que cette action n’est pas disponible sur le plan de récupération et peut être exécutée sur le groupe de disponibilité SQL sous l’onglet Serveurs SQL Server.

##### Test Failover
Le test de basculement pour le groupe de disponibilité SQL n’est pas pris en charge. Si vous déclenchez un test de basculement pour un plan de récupération contenant un groupe de disponibilité SQL, le basculement est ignoré pour le groupe de disponibilité.

En guise d'alternative, considérez ces options :

######Option 1 :



1. Effectuez un test de basculement de l'application et des couches frontales.

2. Mettez à jour la couche applicative pour accéder à la copie du réplica en lecture seule et effectuez un test en lecture seule de l'application.

######Option 2 :

1.	Créez une copie de l'instance d’ordinateur virtuel SQL Server répliquée (à l'aide de clone VMM pour la sauvegarde Azure ou de site à site), et copiez-la dans un réseau de test.
2.	Testez le basculement à l'aide du plan de récupération.

##### Restauration automatique

Si vous voulez définir de nouveau le groupe de disponibilité comme primaire sur le serveur SQL Server local, vous devez déclencher un basculement planifié sur le plan de récupération et choisir le sens de Microsoft Azure vers le serveur VMM local.

#### Réplication inverse

Après un basculement non planifié, la réplication inverse doit être déclenchée sur le groupe de disponibilité pour reprendre la réplication. La réplication est interrompue jusqu’à ce que cette opération soit terminée.


### Environnements non gérés par le serveur VMM

Pour les environnements qui ne sont pas gérés par un serveur VMM, les runbooks Azure Automation peuvent être utilisés pour configurer un basculement de groupes de disponibilité SQL par script. Pour configurer cela, procédez comme suit :


1.	Créez un fichier local pour le script qui bascule un groupe de disponibilité. Cet exemple de script spécifie le chemin d'accès au groupe de disponibilité sur le réplica Azure et le bascule vers cette instance de réplica. Ce script s’exécutera sur l’ordinateur virtuel du réplica SQL Server par l’intermédiaire de l'extension de script personnalisé.



    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2.	Chargez le script dans un objet blob d’un compte de stockage Azure. Utilisez cet exemple :

    	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
    	Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.	Créez un runbook Azure Automation pour appeler les scripts sur l'ordinateur virtuel du réplica SQL Server dans Azure. Pour ce faire, utilisez cet exemple de script. Pour en savoir plus sur l'utilisation de runbooks d'automatisation dans les plans de récupération, [cliquez ici](site-recovery-runbook-automation.md).

    	workflow SQLAvailabilityGroupFailover
    	{
    		param (
        		[Object]$RecoveryPlanContext
    		)

    		$Cred = Get-AutomationPSCredential -name 'AzureCredential'
	
    		#Connect to Azure
    		$AzureAccount = Add-AzureAccount -Credential $Cred
    		$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    		Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
    		InLineScript
    		{
     		#Update the script with name of your storage account, key and blob name
     		$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
     		$sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
     		Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
     		if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
       			{
           		#Skipping TFO in this version.
           		#We will update the script in a follow-up post with TFO support
           		Write-output "tfo: Skipping SQL Failover";
       			}
     		else
       			{
           		Write-output "pfo/ufo";
           		#Get the SQL Azure Replica VM.
           		#Update the script to use the name of your VM and Cloud Service
           		$VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
           		Write-Output "Installing custom script extension"
           		#Install the Custom Script Extension on teh SQL Replica VM
           		Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
           		Write-output "Starting AG Failover";
           		#Execute the SQL Failover script
           		#Pass the SQL AG path as the argument.
       
           		$AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
           		Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
           		Write-output "Completed AG Failover";

       			}
        
    		}
    	}

4.	Lorsque vous créez un plan de récupération pour l'application, ajoutez une étape de script « démarrage avant le groupe 1 » qui exécute le runbook d’automatisation pour basculer vers des groupes de disponibilité.

###Local vers local
Si le serveur SQL utilise des groupes de disponibilité pour la haute disponibilité ou une instance de cluster de basculement, nous vous recommandons d’utiliser également les groupes de disponibilité sur le site de récupération. Notez que ceci vaut pour les applications qui n’utilisent pas les transactions distribuées.


1. [Configurez les bases de données](https://msdn.microsoft.com/library/hh213078.aspx) dans des groupes de disponibilité.
2. Créez un réseau virtuel sur le site secondaire.
3. Configurez un VPN de site à site entre le nouveau réseau virtuel et le site primaire.
4. Créez un ordinateur virtuel sur le site de récupération et installez SQL Server dessus.
5. Étendez les groupes de disponibilité AlwaysOn existants au nouvel ordinateur virtuel SQL Server. Configurez cette instance SQL Server comme une copie de réplica asynchrone.
6. Créez un écouteur de groupe de disponibilité ou modifiez l'écouteur existant pour inclure l’ordinateur virtuel de réplica asynchrone.
7. Vérifiez que la batterie de serveurs d'application est configurée pour utiliser l'écouteur. Si elle est configurée pour utiliser le nom du serveur de base de données, mettez-la à jour pour qu’elle utilise l'écouteur, afin de ne pas avoir à la reconfigurer après le basculement.

Pour les applications qui utilisent des transactions distribuées, nous vous recommandons d'utiliser la [récupération de sites avec la réplication SAN](site-recovery-vmm-san.md) ou la [réplication de site à site VMWare](site-recovery-vmware-to-vmware.md).

####Considérations concernant le plan de récupération

1. Ajoutez cet exemple de script à la bibliothèque VMM sur les sites primaire et secondaire.

    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. Lorsque vous créez un plan de récupération pour l'application, ajoutez une étape de script « démarrage avant le groupe 1 » qui exécute le script qui bascule des groupes de disponibilité.



## Configuration de la protection d'un serveur SQL autonome


Dans cette configuration, nous vous recommandons d’utiliser la réplication Site Recovery pour protéger l'ordinateur SQL Server. La procédure varie selon que SQL Server est configuré comme un ordinateur virtuel ou un serveur physique, et que vous souhaitez répliquer sur Azure ou un site local secondaire. Obtenez des instructions pour tous les scénarios de déploiement dans [Présentation d’Azure Site Recovery](site-recovery-overview.md).


## Configuration de la protection du cluster SQL Server (Standard ou 2008 R2)

Pour un cluster exécutant SQL Server Standard Edition ou SQL Server 2008 R2, nous vous recommandons d’utiliser la réplication Site Recovery pour protéger SQL Server.

#### Local vers local

- Si l'application utilise des transactions distribuées, nous vous recommandons de déployer [la récupération de sites avec la réplication SAN](site-recovery-vmm-san.md) pour les environnements Hyper-V et [VMware vers VMware](site-recovery-vmware-to-vmware.md) pour les environnements VMware.

- Pour les applications non-DTC, tirez parti de l'approche ci-dessus pour récupérer le cluster comme un serveur autonome en exploitant une mise en miroir de base de données locale haute sécurité.

#### Local vers Azure

Site Recovery ne prend pas en charge le cluster invité lors de la réplication vers Azure. De même, SQL Server n’inclut aucune solution de récupération d'urgence à faible coût dans l’édition Standard. Nous vous recommandons de protéger le cluster SQL Server local sur un serveur SQL Server autonome et de le récupérer dans Azure.


1. Configurez une instance SQL Server autonome supplémentaire sur le site local.
2. Configurez cette instance comme une copie miroir des bases de données à protéger. Configurez la mise en miroir en mode haute sécurité.
3.	Configurez Site Recovery sur le site local en fonction de l'environnement ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou [VMware](site-recovery-vmware-to-azure.md)).
4.	Utilisez la réplication Site Recovery pour répliquer la nouvelle instance SQL Server sur Azure. Cette copie miroir de haute sécurité est synchronisée avec le cluster primaire, mais elle est répliquée sur Azure avec la réplication de la récupération de sites.

La figure suivante illustre cette configuration.

![Cluster standard](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)


### Considérations en matière de restauration automatique

Pour les clusters SQL standard, la restauration automatique après un basculement non planifié nécessite une sauvegarde SQL, une restauration à partir de l'instance miroir sur le cluster d'origine, puis le rétablissement de la copie miroir.










 

<!---HONumber=Nov15_HO3-->