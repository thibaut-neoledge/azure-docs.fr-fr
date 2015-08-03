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
	ms.date="06/03/2015" 
	ms.author="raynew"/>


# Récupération d'urgence avec SQL Server et Azure Site Recovery 

Azure Site Recovery est un service Azure qui met en œuvre la stratégie de continuité des activités et de récupération d'urgence (ou BCDR pour Business Continuity and Disaster Recovery) de votre entreprise en coordonnant la réplication, le basculement et la récupération d’ordinateurs virtuels et de serveurs physiques. Site Recovery prend en charge plusieurs mécanismes de réplication qui protègent de manière cohérente la réplication et le basculement d’ordinateurs vers Azure ou un centre de données secondaire. Pour une présentation de tous les scénarios de déploiement, consultez [Présentation d’Azure Site Recovery](site-recovery-overview.md).

 Cet article décrit comment protéger le serveur SQL Server principal d'une application en combinant les technologies BCDR de SQL Server et Azure Site Recovery. Vous devez avoir une bonne compréhension des fonctionnalités BCDR de SQL Server BCDR (cluster de basculement, groupes de disponibilité AlwaysOn, mise en miroir de la base de données, envoi de journaux) et d’Azure Site Recovery avant de déployer les scénarios décrits dans cet article.



## Vue d'ensemble

De nombreuses charges de travail utilisent SQL Server comme base. Les applications SharePoint, Dynamics et SAP utilisent SQL Server pour mettre en œuvre des services de données. Les fonctionnalités de haute disponibilité et de récupération d’urgence de SQL Server, comme les groupes de disponibilité SQL Server, protègent et récupèrent des bases de données SQL Server.

Azure Site Recovery peut protéger SQL Server exécuté comme un ordinateur virtuel Hyper-V, un ordinateur virtuel VMware ou un serveur physique.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td colspan = "2"><b>Hyper-V</b></td>
		<td colspan = "2"><b>VMware</b></td>
		<td colspan = "2"><b>Serveur physique</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Local vers local</td>
		<td>Local vers Azure</td>
		<td>Local vers local</td>
		<td>Local vers Azure</td>
		<td>Local vers local</td>
		<td>Local vers Azure</td>
    </tr>
	<tr align="left" valign="top">
		<td>Oui</td>
		<td>Oui</td>
		<td>Oui</td>
		<td>Bientôt disponible</td>
		<td>Oui</td>
		<td>Bientôt disponible</td>
    </tr>
    </tbody>
    </table>

## Prise en charge et intégration

Azure Site Recovery peut être intégré aux technologies BCDR SQL Server résumées dans le tableau pour fournir une solution de récupération d'urgence.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Fonctionnalité</b></td>
		<td><b>Détails</b></td>
		<td><b>Version de SQL Server</b></td>
    </tr>
    </tr><tr align="left" valign="top">
		<td><b>Groupes de disponibilité AlwaysOn</b></td>
		<td><p>Plusieurs instances autonomes de SQL Server s’exécutent dans un cluster de basculement qui comporte plusieurs nœuds.</p> <p>Les bases de données peuvent être regroupées dans des groupes de basculement qui peuvent être copiés (mis en miroir) sur des instances de SQL Server, pour éviter tout stockage partagé.</p> <p>Cette fonction assure une récupération d'urgence entre un site primaire et un ou plusieurs sites secondaires. Il est possible de configurer deux nœuds dans un cluster sans partage avec les bases de données SQL Server configurées dans un groupe de disponibilité avec réplication synchrone et basculement automatique.</p></td>
		<td>Édition SQL Server 2014/2012 Enterprise</td>
    </tr>
	<tr align="left" valign="top">
		<td><b>Cluster de basculement (instance de cluster de basculement AlwaysOn)</b></td>
		<td><p>SQL Server tire parti de la fonction de cluster de basculement Windows pour assurer la haute disponibilité des charges de travail SQL Server locales.</p><p>Les nœuds qui exécutent des instances de SQL Server avec des disques partagés sont configurés dans un cluster de basculement. Si une instance est arrêtée, le cluster bascule vers un autre.</p> <p>Le cluster ne protège pas contre les défaillances ou les pannes en stockage partagé. Le disque partagé peut être implémenté avec iSCSI, Fibre Channel ou VHDX partagé.</p></td>
		<td><p>Éditions SQL Server Enterprise</p> <p>Édition SQL Server Standard (limitée à deux nœuds)</p></td>
	<tr align="left" valign="top">
		<td><b>Mise en miroir de base de données en mode haute sécurité</b></td>
		<td>Protège une base de données sur une seule copie secondaire. Disponible dans les modes de réplication haute sécurité (synchrone) et hautes performances (asynchrone). Cluster de basculement non requis.</td>
		<td><p>SQL Server 2008 R2</p><p>SQL Server Enterprise (toutes les éditions)</p></td>
    </tr>
    </tr>
	<tr align="left" valign="top">
		<td><b>Serveur SQL autonome</b></td>
		<td>SQL Server et la base de données sont hébergés sur un seul serveur (physique ou virtuel). Le cluster hôte est utilisé pour la haute disponibilité, si le serveur est virtuel. Aucune haute disponibilité pour le niveau invité.</td>
		<td>Édition Enterprise ou Standard</td>
 
    </tbody>
    </table>

Le tableau suivant récapitule nos recommandations pour intégrer les technologies BCDR de SQL Server dans le déploiement d’Azure Site Recovery.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Version</b></td>
		<td><b>Édition</b></td>
		<td><b>Déploiement</b></td>
		<td><b>Local vers local</b></td>
		<td><b>Local vers Azure</b>&lt;/td
    </tr>
    <tr align="left" valign="top">
		<td rowspan = "3">SQL Server 2014 ou 2012</td>
		<td rowspan = "2">Entreprise</td>
		<td>Instance de cluster de basculement</td>
		<td>Groupes de disponibilité AlwaysOn</td>
		<td>Groupes de disponibilité AlwaysOn</td>
    </tr>
	<tr align="left" valign="top">
		<td>Groupes de disponibilité AlwaysOn pour la haute disponibilité</td>
		<td>Groupe de disponibilité AlwaysOn</td>
		<td>Groupe de disponibilité AlwaysOn</td>
    </tr>
	<tr align="left" valign="top">
		<td>Standard</td>
		<td>Instance de cluster de basculement</td>
		<td>Réplication Site Recovery avec miroir local</td>
		<td>Réplication Site Recovery avec miroir local</td>
    </tr>
	<tr align="left" valign="top">
		<td>Enterprise ou Standard</td>
		<td>Standalone</td>
		<td>Réplication Site Recovery avec miroir local</td>
		<td>Réplication Site Recovery avec miroir local</td>
    </tr>
	<tr align="left" valign="top">
		<td>SQL Server 2008 R2</td><td>Enterprise ou Standard</td>
		<td>Standalone</td>
		<td>Réplication Site Recovery avec miroir local</td>
		<td>Réplication Site Recovery avec miroir local</td>
    </tr>
    </tbody>
    </table>


## Conditions préalables au déploiement

Voici ce dont vous avez besoin pour commencer :


- Un déploiement local de SQL Server exécutant une version prise en charge de SQL Server. En général, il faut également Active Directory pour votre serveur SQL.
- La configuration requise pour le scénario que vous souhaitez déployer. Vous trouverez le détail de la configuration requise dans chaque article de déploiement. Ces informations sont répertoriées dans [Présentation d’Azure Site Recovery](site-recovery-overview.md) avec des liens.
- Si vous souhaitez configurer la récupération dans Azure, vous devez exécuter l’outil [Azure Virtual Machine Readiness Assessment](http://www.microsoft.com/download/details.aspx?id=40898) sur vos ordinateurs virtuels SQL Server pour vous assurer qu'ils sont compatibles avec Azure et Site Recovery.

## Configurer la protection

Vous devez effectuer deux étapes :

- Configurer Active Directory
- Configurer la protection d'un cluster SQL Server ou d’un serveur autonome SQL Server

### Configurer Active Directory

Vous avez besoin d’Active Directory sur le site de récupération secondaire pour que SQL Server fonctionne correctement. Deux cas de figure peuvent se présenter :

- **Petite entreprise** : si vous avez un petit nombre d'applications et un seul contrôleur de domaine pour le site local et que vous souhaitez basculer l'ensemble du site, nous vous recommandons d'utiliser Site Recovery pour répliquer le contrôleur de domaine sur le centre de données secondaire ou Azure.

- **Moyenne ou grande entreprise** : si vous avez un grand nombre d'applications, que vous exécutez une forêt Active Directory et que vous souhaitez effectuer un basculement par application ou charge de travail, nous vous recommandons de configurer un contrôleur de domaine supplémentaire dans le centre de données secondaire ou dans Azure. Notez que si vous utilisez des groupes de disponibilité AlwaysOn pour effectuer une récupération sur un site distant, nous vous recommandons de configurer un contrôleur de domaine supplémentaire sur le site secondaire ou sur Azure, à utiliser pour l'instance SQL Server récupérée.

Les instructions fournies dans ce document supposent qu'un contrôleur de domaine est disponible sur l'emplacement secondaire.

### Configurer la protection d'un serveur SQL Server autonome


Dans cette configuration, nous vous recommandons d’utiliser la réplication Site Recovery pour protéger l'ordinateur SQL Server. La procédure varie selon que SQL Server est configuré comme un ordinateur virtuel ou un serveur physique, et que vous souhaitez répliquer sur Azure ou un site local secondaire. Obtenez des instructions pour tous les scénarios de déploiement dans [Présentation d’Azure Site Recovery](site-recovery-overview.md).


### Configurer la protection pour le cluster SQL Server (Enterprise 2012 ou 2014)

Si le serveur SQL utilise des groupes de disponibilité pour la haute disponibilité ou une instance de cluster de basculement, nous vous recommandons d’utiliser également les groupes de disponibilité sur le site de récupération. Notez que ceci vaut pour les applications qui n’utilisent pas les transactions distribuées.

##### Local vers local

1. [Configurez les bases de données](https://msdn.microsoft.com/library/hh213078.aspx) dans des groupes de disponibilité.
2. Créez un réseau virtuel sur le site secondaire.
3. Configurez un VPN de site à site entre le nouveau réseau virtuel et le site primaire.
4. Créez un ordinateur virtuel sur le site de récupération et installez SQL Server dessus.
5. Étendez les groupes de disponibilité AlwaysOn existants au nouvel ordinateur virtuel SQL Server. Configurez cette instance SQL Server comme une copie de réplica asynchrone.
6. Créez un écouteur de groupe de disponibilité ou modifiez l'écouteur existant pour inclure l’ordinateur virtuel de réplica asynchrone.
7. Vérifiez que la batterie de serveurs d'application est configurée pour utiliser l'écouteur. Si elle est configurée pour utiliser le nom du serveur de base de données, mettez-la à jour pour qu’elle utilise l'écouteur, afin de ne pas avoir à la reconfigurer après le basculement.

#### Local vers Azure

Lorsque vous répliquez vers Azure, configurer plusieurs groupes de disponibilité est un défi car chaque groupe de disponibilité a besoin d'un écouteur dédié, et la configuration de chaque écouteur requiert un service cloud distinct. Nous vous recommandons de configurer un groupe de disponibilité avec toutes les bases de données incluses.

1. Créez un réseau virtuel Azure.
2. Configurez un VPN de site à site entre le site local et ce réseau.
3. Créez un ordinateur virtuel SQL Server Azure dans le réseau et configurez-le comme un réplica de groupe de disponibilité asynchrone. Si vous avez besoin d’une haute disponibilité pour la couche SQL Server après un basculement vers Azure, configurez deux copies de réplica asynchrone dans Azure.
4. Configurez un réplica du contrôleur de domaine dans le réseau virtuel.
5. Vérifiez que les extensions d'ordinateur virtuel sont activées sur l'ordinateur virtuel. Cela est nécessaire pour transmettre les scripts SQL Server à un plan de récupération.
6. Configurez un écouteur SQL Server pour le groupe de disponibilité à l'aide de la fonction d’équilibrage de charge interne d’Azure.
7. Configurez la couche applicative pour qu’elle accède à la couche de base de données à l’aide de l’écouteur. Pour les applications qui utilisent des transactions distribuées, nous vous recommandons d’utiliser Site Recovery avec la réplication SAN ou de réplication de site à site VMWare.

### Configurer la protection pour le cluster SQL Server (Standard ou 2008 R2)

Pour un cluster exécutant SQL Server Standard Edition ou SQL Server 2008 R2, nous vous recommandons d’utiliser la réplication Site Recovery pour protéger SQL Server.

#### Local vers local

- Pour un environnement Hyper-V, si l'application utilise des transactions distribuées, nous vous recommandons de déployer [Site Recovery avec la réplication SAN](site-recovery-vmm-san.md).

- Pour un environnement VMware, nous vous recommandons de déployer la protection [VMware à VMware](site-recovery-vmware-to-vmware.md).

#### Local vers Azure

Site Recovery ne prend pas en charge le cluster invité lors de la réplication vers Azure. De même, SQL Server n’inclut aucune solution de récupération d'urgence à faible coût dans l’édition Standard. Nous vous recommandons de protéger le cluster SQL Server local sur un serveur SQL Server autonome et de le récupérer dans Azure.


1. Configurez une instance SQL Server autonome supplémentaire dans le site local.
2. Configurez cette instance comme une copie miroir des bases de données à protéger. Configurez la mise en miroir en mode haute sécurité.
3.	Configurez Site Recovery sur le site local en fonction de l'environnement ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou [VMware](site-recovery-vmware-to-azure.md)).
4.	Utilisez la réplication Site Recovery pour répliquer la nouvelle instance SQL Server sur Azure. Cette copie miroir de haute sécurité est synchronisée avec le cluster primaire, mais elle est répliquée sur Azure avec la réplication Site Recovery.

La figure suivante illustre cette configuration.

![Cluster standard](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)



##Créer des plans de récupération

Les plans de récupération regroupent des ordinateurs qui doivent basculer ensemble. Pour en savoir plus, consultez [Plans de récupération](site-recovery-create-recovery-plans.md) et [Basculement](site-recovery-failover.md) avant de commencer.


### Créer un plan de récupération pour les clusters SQL Server (SQL Server 2012/2014 Enterprise)

#### Configurer des scripts SQL Server pour le basculement vers Azure

Dans ce scénario, nous utilisons des scripts personnalisés et Azure Automation pour configurer un basculement par script des groupes de disponibilité SQL Server.

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

#### Configurer des scripts SQL Server pour le basculement vers un site secondaire

1. Ajoutez cet exemple de script à la bibliothèque VMM sur les sites primaire et secondaire.

    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. Lorsque vous créez un plan de récupération pour l'application, ajoutez une étape de script « démarrage avant le groupe 1 » qui exécute le script qui bascule des groupes de disponibilité.

### Créer un plan de récupération pour les clusters SQL Server (Standard)

#### Configurer des scripts SQL Server pour le basculement vers Azure

1.	Créez un fichier local de script pour le basculement de la copie miroir de la base de données SQL Server. Utilisez cet exemple de script.

    	Param(
    	[string]$database
    	)
    	Import-module sqlps
    	Invoke-sqlcmd –query “ALTER DATABASE $database SET PARTNER FORCE_SERVICE_ALLOW_DATA_LOSS”

2.	Chargez le script dans un objet blob d’un compte de stockage Azure. Utilisez cet exemple de script.

    	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
    	Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.	Créez un runbook Azure Automation pour appeler le script sur l'ordinateur virtuel du réplica SQL Server dans Azure. Pour ce faire, utilisez cet exemple de script. Pour en savoir plus sur l'utilisation de runbooks d'automatisation dans les plans de récupération, [cliquez ici](site-recovery-runbook-automation.md). Auparavant, vérifiez que l'agent d’ordinateur virtuel s’exécute sur l’ordinateur virtuel SQL Server basculé.

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
     	$sasuri = New-AzureStorageBlobSASToken -Container "script-container" -Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
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



4. Ajoutez les étapes suivantes au plan de récupération pour basculer la couche SQL Server :

	- Pour un basculement planifié, ajoutez un script primaire qui arrête le cluster primaire après l’arrêt du groupe.
	- Ajoutez l’ordinateur virtuel contenant la copie miroir de la base de données SQL Server au plan de récupération, de préférence dans le premier groupe de démarrage.
3.	Ajoutez un script de post-basculement pour basculer vers la copie miroir stockée dans cet ordinateur virtuel, à l’aide du script d'automatisation ci-dessus. Notez que, comme le nom de l’instance de base de données va être modifié, il faudra reconfigurer la couche applicative pour qu’elle utilise la nouvelle base de données.


#### Configurer des scripts SQL Server pour le basculement vers un site secondaire

1.	Ajoutez cet exemple de script à la bibliothèque VMM sur les sites primaire et secondaire.

    	Param(
    	[string]$database
    	)
    	Import-module sqlps
    	Invoke-sqlcmd –query “ALTER DATABASE $database SET PARTNER FORCE_SERVICE_ALLOW_DATA_LOSS”

2.	Ajoutez l’ordinateur virtuel contenant la copie miroir de la base de données SQL Server au plan de récupération, de préférence dans le premier groupe de démarrage.
3.	Ajoutez un script de post-basculement pour basculer la copie miroir stockée dans cet ordinateur virtuel à l’aide du script VMM ci-dessus. Notez que, comme le nom de l’instance de base de données va être modifié, il faudra reconfigurer la couche applicative pour qu’elle utilise la nouvelle base de données.





## Considérations en matière de test de basculement

Si vous utilisez des groupes de disponibilité AlwaysOn, vous ne pouvez pas tester le basculement de la couche SQL Server. En guise d'alternative, considérez ces options :

- Option 1 :

	1. Effectuez un test de basculement de l'application et des couches frontales.
	2. Mettez à jour la couche applicative pour accéder à la copie du réplica en lecture seule et effectuez un test en lecture seule de l'application.

- Option 2 :
1.	Créez une copie de l'instance d’ordinateur virtuel SQL Server répliquée (à l'aide de clone VMM pour la sauvegarde Azure ou de site à site), et copiez-la dans un réseau de test.
2.	Testez le basculement à l'aide du plan de récupération.

## Considérations en matière de restauration automatique

Pour les clusters SQL standard, la restauration automatique après un basculement non planifié nécessite une sauvegarde SQL Server, une restauration à partir de l'instance miroir sur le cluster d'origine, puis le rétablissement de la copie miroir.





 

<!---HONumber=July15_HO4-->