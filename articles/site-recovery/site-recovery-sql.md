---
title: "Répliquer des applications avec SQL Server et Azure Site Recovery | Microsoft Docs"
description: "Cet article décrit comment répliquer SQL Server à l’aide d’Azure Site Recovery et des fonctionnalités de récupération de SQL Server."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: 9ea73dd91c9637692bbc3d6d2aa97fbed7ae500d
ms.openlocfilehash: 79c110031a47f1bdb78f4acfcadd7bff1e909807
ms.lasthandoff: 02/23/2017


---
# <a name="protect-sql-server-using-sql-server-disaster-recovery-and-azure-site-recovery"></a>Protéger SQL Server à l’aide de la récupération d’urgence SQL Server et d’Azure Site Recovery

Cet article décrit comment protéger le serveur SQL Server principal d’une application en combinant les technologies de continuité d’activité et de récupération d’urgence (BCDR) de SQL Server et [Azure Site Recovery](site-recovery-overview.md).

Avant de commencer, assurez-vous que vous comprenez les fonctionnalités de récupération d’urgence de SQL Server, notamment le clustering de basculement, les groupes de disponibilité AlwaysOn, la mise en miroir de bases de données et la copie des journaux de transaction.


## <a name="sql-server-deployments"></a>Déploiements SQL Server

De nombreuses charges de travail utilisent SQL Server comme base, qui peut être intégré avec des applications telles que SharePoint, Dynamics et SAP pour mettre en œuvre des services de données.  SQL Server peut être déployé de plusieurs façons :

* **SQL Server autonome**: le serveur SQL et toutes les bases de données sont hébergés sur un seul ordinateur (physique ou une machine virtuelle). Quand le serveur est virtualisé, le cluster hôte est utilisé pour la haute disponibilité locale. La haute disponibilité pour le niveau invité n’est pas mise en œuvre.
* **Instances de clustering de basculement SQL Server (AlwaysOn FCI)** : au moins deux nœuds exécutant des instances SQL Server avec des disques partagés sont configurés dans un cluster de basculement Windows. Si un nœud est inactif, le cluster peut basculer SQL Server vers une autre instance. Cette configuration est généralement utilisée pour mettre en œuvre la haute disponibilité sur un site principal. Ce type de déploiement ne protège pas contre la défaillance ou une panne dans la couche de stockage partagé. Un disque partagé peut être mis en œuvre avec iSCSI, Fibre Channel ou VHDx partagé.
* **Groupes de disponibilité SQL AlwaysOn** : au moins deux nœuds sont configurés dans un cluster sans partage avec des bases de données SQL Server configurées dans un groupe de disponibilité avec réplication synchrone et basculement automatique.

 Cet article s’appuie sur les technologies de récupération d’urgence SQL natives suivantes pour mettre en œuvre la récupération des bases de données vers un site distant :

* Groupes de disponibilité SQL AlwaysOn, pour la récupération d’urgence pour SQL Server éditions 2012 ou 2014 Enterprise.
* Mise en miroir de base de données SQL en mode haute sécurité pour SQL Server édition Standard (toute version) ou SQL Server 2008 R2.

## <a name="site-recovery-support"></a>Prise en charge de Site Recovery

### <a name="supported-scenarios"></a>Scénarios pris en charge
Site Recovery peut protéger SQL Server comme décrit dans le tableau ci-dessous.

**Scénario** | **Vers un site secondaire** | **Vers Azure**
--- | --- | ---
**Hyper-V** | Oui | Oui
**VMware** | Oui | Oui
**Serveur physique** | Oui | Oui

### <a name="supported-sql-server-versions"></a>Versions de SQL Server prises en charge
Les scénarios de cet article prennent en charge les versions de SQL Server suivantes :

* SQL Server 2014 Entreprise et Standard
* SQL Server 2012 Entreprise et Standard
* SQL Server 2008 R2 Entreprise et Standard

### <a name="supported-sql-server-integration"></a>Intégration de SQL Server prise en charge

Azure Site Recovery peut être intégré aux technologies BCDR SQL Server natives répertoriées dans le tableau pour fournir une solution de récupération d’urgence.

**Fonctionnalité** | **Détails** | **SQL Server** |
--- | --- | ---
**Groupe de disponibilité AlwaysOn** | Plusieurs instances autonomes de SQL Server s’exécutent chacune dans un cluster de basculement qui comporte plusieurs nœuds.<br/><br/>Les bases de données peuvent être regroupées dans des groupes de basculement qui peuvent être copiés (mis en miroir) sur des instances de SQL Server, pour éviter tout stockage partagé.<br/><br/>Cette fonction assure une récupération d'urgence entre un site primaire et un ou plusieurs sites secondaires. Il est possible de configurer deux nœuds dans un cluster sans partage avec les bases de données SQL Server configurées dans un groupe de disponibilité avec réplication synchrone et basculement automatique. | SQL Server 2014 et 2012 édition Enterprise
**Cluster de basculement (instance de cluster de basculement AlwaysOn)** | SQL Server tire parti de la fonction de cluster de basculement Windows pour assurer la haute disponibilité des charges de travail SQL Server locales.<br/><br/>Les nœuds qui exécutent des instances de SQL Server avec des disques partagés sont configurés dans un cluster de basculement. Si une instance est arrêtée, le cluster bascule vers un autre.<br/><br/>Le cluster ne protège pas contre les défaillances ou les pannes en stockage partagé. Le disque partagé peut être implémenté avec iSCSI, Fibre Channel ou VHDX partagé. | Éditions SQL Server Enterprise<br/><br/>Éditions SQL Server Standard (limitée à deux nœuds)
**Mise en miroir de base de données (mode haute sécurité)** | Protège une base de données sur une seule copie secondaire. Disponible dans les modes de réplication haute sécurité (synchrone) et hautes performances (asynchrone). Cluster de basculement non requis. | SQL Server 2008 R2<br/><br/>SQL Server Enterprise (toutes les éditions)
**Serveur SQL autonome** | SQL Server et la base de données sont hébergés sur un seul serveur (physique ou virtuel). Le cluster hôte est utilisé pour la haute disponibilité, si le serveur est virtuel. Aucune haute disponibilité pour le niveau invité. | Édition Enterprise ou Standard

## <a name="deployment-recommendations"></a>Recommandations concernant le déploiement

Ce tableau récapitule nos recommandations pour intégrer les technologies BCDR de SQL Server à Site Recovery.

| **Version** | **Édition** | **Déploiement** | **Local à local** | **Local vers Azure** |
| --- | --- | --- | --- | --- |
| SQL Server 2014 ou 2012 |Entreprise |Instance de cluster de basculement |Groupes de disponibilité AlwaysOn |Groupes de disponibilité AlwaysOn |
|| Entreprise |Groupes de disponibilité AlwaysOn pour la haute disponibilité |Groupes de disponibilité AlwaysOn |Groupes de disponibilité AlwaysOn | |
|| Standard |Instance de cluster de basculement (FCI) |Réplication Site Recovery avec miroir local |Réplication Site Recovery avec miroir local | |
|| Enterprise ou Standard |Standalone |Réplication de la récupération de sites |Réplication de la récupération de sites | |
| SQL Server 2008 R2 |Enterprise ou Standard |Instance de cluster de basculement (FCI) |Réplication Site Recovery avec miroir local |Réplication Site Recovery avec miroir local |
|| Enterprise ou Standard |Standalone |Réplication de la récupération de sites |Réplication de la récupération de sites | |
| SQL Server (toute version) |Enterprise ou Standard |Instance de cluster de basculement - application DTC |Réplication de la récupération de sites |Non pris en charge |

## <a name="deployment-prerequisites"></a>Conditions préalables au déploiement

* Un déploiement local de SQL Server exécutant une version prise en charge de SQL Server. En général, il est également nécessaire de configurer Active Directory pour votre serveur SQL.
* La configuration requise pour le scénario que vous souhaitez déployer. En savoir plus sur les conditions de prise en charge pour la [réplication vers Azure](site-recovery-support-matrix-to-azure.md) et [en local](site-recovery-support-matrix.md), ainsi que sur les [conditions préalables au déploiement](site-recovery-prereq.md).
* Pour configurer la récupération dans Azure, exécutez l’outil [Azure Virtual Machine Readiness Assessment](http://www.microsoft.com/download/details.aspx?id=40898) sur vos machines virtuelles SQL Server pour vous assurer qu’elles sont compatibles avec Azure et Site Recovery.

## <a name="set-up-active-directory"></a>Configurer Active Directory

Configurez Active Directory sur le site de récupération secondaire afin que SQL Server fonctionne correctement.

* **Petite entreprise** : si vous avez un petit nombre d’applications et un seul contrôleur de domaine pour le site local et que vous souhaitez basculer l’ensemble du site, nous vous recommandons d’utiliser Site Recovery pour répliquer le contrôleur de domaine sur le centre de données secondaire ou sur Azure.
* **Moyenne ou grande entreprise** : si vous avez un grand nombre d’applications et une forêt Active Directory et que vous souhaitez effectuer un basculement par application ou charge de travail, nous vous recommandons de configurer un contrôleur de domaine supplémentaire dans le centre de données secondaire ou dans Azure. Si vous utilisez des groupes de disponibilité AlwaysOn pour effectuer une récupération sur un site distant, nous vous recommandons de configurer un contrôleur de domaine supplémentaire sur le site secondaire ou dans Azure qui sera utilisé pour l’instance SQL Server récupérée.

Les instructions fournies dans cet article supposent qu’un contrôleur de domaine est disponible sur le site secondaire. [ici](site-recovery-active-directory.md) .

## <a name="integrate-with-sql-server-alwayson-for-replication-to-azure-classic-portal-with-a-vmmconfiguration-server"></a>Intégration avec SQL Server AlwaysOn pour la réplication vers Azure (portail Azure Classic avec un serveur VMM/de configuration)


Site Recovery prend en charge SQL AlwaysOn en mode natif. Si vous avez créé un groupe de disponibilité SQL avec une machine virtuelle Azure configurée comme emplacement secondaire, vous pouvez utiliser Site Recovery pour gérer le basculement des groupes de disponibilité.

> [!NOTE]
> Pour le moment, cette fonctionnalité n’est disponible que dans la version préliminaire. Elle est disponible lorsque le site principal possède des serveurs hôtes Hyper-V gérés dans des clouds VMM System Center, ou lorsque vous avez configuré la [réplication VMware](site-recovery-vmware-to-azure.md). La fonctionnalité n’est actuellement pas disponible dans le nouveau portail Azure. Si vous utilisez le nouveau portail Azure, suivez les étapes de [cette section](site-recovery-sql.md#integrate-with-sql-server-alwayson-for-replication-to-azure-azure-portalclassic-portal-with-no-vmmconfiguration-server).
>
>


#### <a name="before-you-start"></a>Avant de commencer

Voici ce dont vous avez besoin pour intégrer SQL AlwaysOn à Site Recovery :

* Un serveur SQL Server local (serveur autonome ou cluster de basculement)
* Une ou plusieurs machines virtuelles Azure sur lesquelles est installé SQL Server.
* Un groupe de disponibilité SQL Server configuré entre un serveur SQL Server local et celui exécuté dans Azure.
* Une communication à distance PowerShell activée sur le serveur SQL Server local. Le serveur VMM ou de configuration doit pouvoir effectuer des appels PowerShell distants vers la machine SQL Server.
* Un compte d’utilisateur doit être ajouté à la machine SQL Server locale. Ajoutez un groupe SQL Server disposant au minimum des autorisations suivantes :
  * ALTER AVAILABILITY GROUP : autorisations [ici](https://msdn.microsoft.com/library/hh231018.aspx) et [ici](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
  * ALTER DATABASE : autorisations [ici](https://msdn.microsoft.com/library/ff877956.aspx#Security)
* Si vous utilisez un serveur VMM, un compte d’identification doit être créé sur le serveur VMM.
- Si vous utilisez VMware, un compte doit être créé sur le serveur de configuration à l’aide du fichier CSPSConfigtool.exe.
* Le module SQL PS doit être installé sur les serveurs SQL Server exécutés en local et sur les machines virtuelles Azure.
* L’agent de machine virtuelle doit être installé sur les machines virtuelles Azure.
* NTAUTHORITY\System doit comporter les autorisations suivantes sur le serveur SQL Server exécuté sur les machines virtuelles Azure.
  * ALTER AVAILABILITY GROUP : autorisations [ici](https://msdn.microsoft.com/library/hh231018.aspx) et [ici](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
  * ALTER DATABASE : autorisations [ici](https://msdn.microsoft.com/library/ff877956.aspx#Security)

### <a name="add-a-sql-server"></a>Ajouter un serveur SQL Server
1. Cliquez sur **Ajouter un serveur SQL** pour ajouter un nouveau serveur SQL Server.

    ![Ajouter un serveur SQL](./media/site-recovery-sql/add-sql.png)
2. Sous **Configurer les paramètres SQL** > **Nom**, indiquez un nom convivial pour le serveur SQL Server.
3. Sous **SQL Server (nom de domaine complet)**, indiquez le nom de domaine complet du serveur SQL Server source que vous souhaitez ajouter. Si le serveur SQL Server est installé dans un cluster de basculement, indiquez le nom de domaine complet du cluster et non celui des nœuds de cluster.  
4. Sous **Instance SQL Server**, choisissez l’instance par défaut ou indiquez le nom de l’instance personnalisée.
5. Sous **Serveur de gestion**, sélectionnez un serveur VMM ou un serveur de configuration enregistré dans le coffre. Site Recovery utilise ce serveur pour communiquer avec le serveur SQL Server.
6. Sous **Compte d’identification**, indiquez le nom du compte d’identification VMM ou du compte de serveur de configuration. Ce compte est utilisé pour accéder au serveur SQL Server et doit être doté des autorisations Lecture et Basculement pour les groupes de disponibilité présents sur la machine SQL Server.

    ![Boîte de dialogue Ajouter un serveur SQL](./media/site-recovery-sql/add-sql-dialog.png)

Une fois ajouté, le serveur SQL Server apparaît sous l’onglet **Serveurs SQL**.

![Liste des serveurs SQL Server](./media/site-recovery-sql/sql-server-list.png)

### <a name="add-a-sql-availability-group"></a>Ajouter un groupe de disponibilité SQL

1. Dans le serveur SQL Server que vous avez ajouté, cliquez sur **Ajouter un groupe de disponibilité SQL**.

    ![Ajouter un groupe de disponibilité SQL](./media/site-recovery-sql/add-sqlag.png)
2. Le groupe de disponibilité peut être répliqué sur une ou plusieurs machines virtuelles Azure. Lorsque vous ajoutez le groupe, vous devez fournir le nom et l’abonnement de la machine virtuelle Azure vers laquelle vous souhaitez que Site Recovery bascule le groupe.

    ![Boîte de dialogue Ajouter un groupe de disponibilité SQL](./media/site-recovery-sql/add-sqlag-dialog.png)
3. Dans cet exemple, le groupe de disponibilité DB1-AG devient Principal sur la machine virtuelle SQLAGVM2 exécutée au sein de l’abonnement DevTesting2 sur un basculement.

> [!NOTE]
> Seuls les groupes de disponibilité ayant le statut Principal sur le serveur SQL Server ajouté peuvent être ajoutés à Site Recovery. Si vous avez défini un groupe de disponibilité comme Principal sur le serveur SQL Server ou si vous avez ajouté d’autres groupes de disponibilité au serveur SQL Server une fois celui-ci ajouté, actualisez le groupe sur le serveur SQL Server.
>
>

### <a name="create-a-recovery-plan"></a>Créer un plan de récupération

Créez un plan de récupération à l’aide des machines virtuelles et des groupes de disponibilité. Sélectionnez le serveur VMM ou le serveur de configuration comme source et Azure comme cible.

![Créer un plan de récupération](./media/site-recovery-sql/create-rp1.png)

![Créer un plan de récupération](./media/site-recovery-sql/create-rp2.png)

Dans l’exemple, l’application Sharepoint est composée de trois machines virtuelles qui utilisent un groupe de disponibilité SQL comme serveur principal. Dans ce plan de récupération, nous pouvons sélectionner à la fois le groupe de disponibilité et les machines virtuelles de l’application. Vous pouvez personnaliser davantage le plan de récupération en déplaçant les machines virtuelles vers d’autres groupes de basculement pour définir l’ordre de basculement. Le groupe de disponibilité est toujours basculé en premier, car il est utilisé comme serveur principal de l’application.

![Personnaliser un plan de récupération](./media/site-recovery-sql/customize-rp.png)

### <a name="failover"></a>Basculement

Une fois le groupe de disponibilité ajouté à un plan de récupération, différentes options de basculement sont disponibles.

**Type de basculement** | **Détails**
--- | ---
**Basculement planifié** | Un basculement planifié implique un basculement sans perte de données. Pour cela, le mode du groupe de disponibilité SQL est défini sur Synchrone, puis un basculement est déclenché pour définir le groupe de disponibilité comme Principal sur la machine virtuelle fournie au moment de l’ajout du groupe de disponibilité à Site Recovery. Une fois le basculement terminé, le mode de disponibilité est défini sur la valeur qu’il avait avant le déclenchement du basculement planifié.
**Basculement non planifié** | Un basculement non planifié peut entraîner une perte de données. Lors du déclenchement d’un basculement non planifié, le mode de disponibilité du groupe n’est pas modifié. Le groupe de disponibilité est défini comme Principal sur la machine virtuelle fournie au moment de l’ajout du groupe de disponibilité à Site Recovery. Une fois que le basculement non planifié est terminé et que le serveur local exécutant SQL Server est à nouveau disponible, la réplication inverse doit être déclenchée sur le groupe de disponibilité. Cette action est disponible dans **Serveurs SQL Server** > **Groupe de disponibilité SQL** et non dans le plan de récupération.
**Test de basculement** |Le test de basculement pour le groupe de disponibilité SQL n’est pas pris en charge. Si vous déclenchez un test de basculement, le basculement est ignoré pour le groupe de disponibilité.

Essayez les options de basculement ci-dessous.

**Option** | **Détails**
--- | ---
**Option 1 :** | 1. Effectuez un test de basculement de l'application et des couches frontales.<br/><br/>2. Mettez à jour la couche applicative pour accéder à la copie du réplica en lecture seule et effectuez un test en lecture seule de l'application.
**Option 2 :** | 1. Créez une copie de l’instance de machine virtuelle SQL Server répliquée (à l’aide du clone VMM pour la sauvegarde Azure ou de site à site) et copiez-la dans un réseau de test.<br/><br/> 2. Testez le basculement à l'aide du plan de récupération.

### <a name="fail-back"></a>Effectuer une restauration automatique

Si vous voulez définir de nouveau le groupe de disponibilité comme principal sur le serveur SQL Server local, déclenchez un basculement planifié sur le plan de récupération et choisissez le sens de Microsoft Azure vers le serveur local.

> [!NOTE]
> Après un basculement non planifié, la réplication inverse doit être déclenchée sur le groupe de disponibilité pour reprendre la réplication.  La réplication est interrompue jusqu’à ce que cette opération soit terminée.
>
>

## <a name="integrate-with-sql-server-alwayson-for-replication-to-azure-azure-portalclassic-portal-with-no-vmmconfiguration-server"></a>Intégration avec SQL Server AlwaysOn pour la réplication vers Azure (portail Azure/Azure Classic sans serveur VMM/de configuration)

Ces instructions sont pertinentes en cas d’intégration avec des groupes de disponibilité SQL Server sur le nouveau portail Azure, ou sur le portail Azure Classic si vous n’utilisez pas de serveur VMM ou de configuration. Dans ce scénario, les runbooks Azure Automation peuvent être utilisés pour configurer un basculement de groupes de disponibilité SQL par script.

Voici ce que vous devez faire :

1. Créez un fichier local pour un script qui bascule un groupe de disponibilité. Cet exemple de script spécifie le chemin d’accès au groupe de disponibilité sur le réplica Azure et le bascule vers cette instance de réplica. Ce script s’exécute sur la machine virtuelle SQL Server répliquée par l’intermédiaire de l’extension de script personnalisé.

        ``Param(
           [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force``

2. Chargez le script dans un objet blob d’un compte de stockage Azure. Utilisez cet exemple :

        ``$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
        Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context``

3. Créez un runbook Azure Automation pour appeler les scripts sur la machine virtuelle SQL Server répliquée dans Azure. Pour ce faire, utilisez cet exemple de script. [cliquez ici](site-recovery-runbook-automation.md) .

4. Lorsque vous créez un plan de récupération pour l’application, ajoutez une étape de script « démarrage avant le groupe 1 » qui exécute le runbook d’automatisation pour basculer vers le groupe de disponibilité.


5. SQL AlwaysOn ne prend pas en charge le test de basculement de manière native. Par conséquent, nous vous recommandons de :
    1. Configurer la [Sauvegarde Azure](../backup/backup-azure-vms.md) sur la machine virtuelle qui héberge le réplica du groupe de disponibilité dans Azure.
    1. Avant de déclencher le test de basculement du plan de récupération, récupérez la machine virtuelle à partir de la sauvegarde effectuée à l’étape précédente.
    1. Effectuer un test de basculement du plan de récupération.


> [!NOTE]
> Le script ci-dessous suppose que le groupe de disponibilité SQL est hébergé sur une machine virtuelle Azure classique et que le nom de la machine virtuelle restaurée à l’étape&2; est SQLAzureVM-Test. Modifiez le script en fonction du nom que vous utilisez pour la machine virtuelle récupérée.
>
>


     ``workflow SQLAvailabilityGroupFailover
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
                    Write-output "tfo"

                    Write-Output "Creating ILB"
                    Add-AzureInternalLoadBalancer -InternalLoadBalancerName SQLAGILB -SubnetName Subnet-1 -ServiceName SQLAzureVM-Test -StaticVNetIPAddress #IP
                    Write-Output "ILB Created"

                    #Update the script with name of the virtual machine recovered using Azure Backup
                    Write-Output "Adding SQL AG Endpoint"
                    Get-AzureVM -ServiceName "SQLAzureVM-Test" -Name "SQLAzureVM-Test"| Add-AzureEndpoint -Name sqlag -LBSetName sqlagset -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName SQLAGILB | Update-AzureVM

                    Write-Output "Added Endpoint"

                    $VM = Get-AzureVM -Name "SQLAzureVM-Test" -ServiceName "SQLAzureVM-Test"

                    Write-Output "UnInstalling custom script extension"
                    Set-AzureVMCustomScriptExtension -Uninstall -ReferenceName CustomScriptExtension -VM $VM |Update-AzureVM
                    Write-Output "Installing custom script extension"
                    Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $vm -Publisher Microsoft.Compute -Version 1.*| Update-AzureVM   

                    Write-output "Starting AG Failover"
                    Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument "-Path sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag"  | Update-AzureVM
                    Write-output "Completed AG Failover"
                }
          else
                {
                Write-output "pfo/ufo";
                #Get the SQL Azure Replica VM.
                #Update the script to use the name of your VM and Cloud Service
                $VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     

                Write-Output "Installing custom script extension"
                #Install the Custom Script Extension on teh SQL Replica VM
                Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.*| Update-AzureVM;

                Write-output "Starting AG Failover";
                #Execute the SQL Failover script
                #Pass the SQL AG path as the argument.

                $AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";

                Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;

                Write-output "Completed AG Failover";

                }

         }
     }``

## <a name="integrate-with-sql-server-alwayson-for-replication-to-a-secondary-on-premises-site"></a>Intégration avec SQL Server AlwaysOn pour la réplication vers un site local secondaire

Si le serveur SQL Server utilise des groupes de disponibilité pour la haute disponibilité (ou une instance de cluster de basculement), nous vous recommandons d’utiliser également les groupes de disponibilité sur le site de récupération. Notez que ceci vaut pour les applications qui n’utilisent pas les transactions distribuées.

1. [Configurez les bases de données](https://msdn.microsoft.com/library/hh213078.aspx) dans des groupes de disponibilité.
2. Créez un réseau virtuel sur le site secondaire.
3. Configurez une connexion VPN de site à site entre le réseau virtuel et le site principal.
4. Créez une machine virtuelle sur le site de récupération et installez-y SQL Server.
5. Étendez les groupes de disponibilité AlwaysOn existants à la nouvelle machine virtuelle SQL Server. Configurez cette instance SQL Server comme une copie de réplica asynchrone.
6. Créez un écouteur de groupe de disponibilité ou modifiez l'écouteur existant pour inclure l’ordinateur virtuel de réplica asynchrone.
7. Vérifiez que la batterie de serveurs d’application est configurée pour utiliser l’écouteur. Si elle est configurée pour utiliser le nom du serveur de base de données, mettez-la à jour pour qu’elle utilise l’écouteur, afin de ne pas avoir à la reconfigurer après le basculement.

Pour les applications qui utilisent des transactions distribuées, nous vous recommandons de déployer Site Recovery avec la [réplication SAN](site-recovery-vmm-san.md) ou la [réplication de site à site avec serveur physique/VMware](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Considérations concernant le plan de récupération
1. Ajoutez cet exemple de script à la bibliothèque VMM sur les sites principal et secondaire.

        ``Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force``
2. Lorsque vous créez un plan de récupération pour l’application, ajoutez une étape de script « démarrage avant le groupe 1 » qui exécute le script de basculement des groupes de disponibilité.

## <a name="protect-a-standalone-sql-server"></a>Protéger un serveur SQL Server autonome

Dans ce scénario, nous vous recommandons d’utiliser la réplication Site Recovery pour protéger la machine SQL Server. La procédure varie selon que SQL Server est configuré comme une machine virtuelle ou un serveur physique, et que vous souhaitez répliquer sur Azure ou un site local secondaire. En savoir plus sur les [scénarios Site Recovery](site-recovery-overview.md).

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>Protéger un cluster SQL Server (Standard Edition/Windows Server 2008 R2)

Pour un cluster exécutant SQL Server Standard Edition ou SQL Server 2008 R2, nous vous recommandons d’utiliser la réplication Site Recovery pour protéger SQL Server.

### <a name="on-premises-to-on-premises"></a>Local vers local

* Si l’application utilise des transactions distribuées, nous vous recommandons de déployer [Site Recovery avec la réplication SAN](site-recovery-vmm-san.md) pour un environnement Hyper-V ou [serveur physique/VMware vers VMware](site-recovery-vmware-to-vmware.md) pour un environnement VMware.
* Pour les applications non-DTC, utilisez l’approche ci-dessus pour récupérer le cluster comme un serveur autonome en exploitant une mise en miroir de base de données locale haute sécurité.

### <a name="on-premises-to-azure"></a>Local vers Azure

Site Recovery ne prend pas en charge le cluster invité lors de la réplication vers Azure. De même, SQL Server n’inclut aucune solution de récupération d'urgence à faible coût dans l’édition Standard. Dans ce scénario, nous vous recommandons de protéger le cluster SQL Server local sur un serveur SQL Server autonome et de le récupérer dans Azure.

1. Configurez une instance SQL Server autonome supplémentaire sur le site local.
2. Configurez l’instance comme une copie miroir des bases de données que vous souhaitez protéger. Configurez la mise en miroir en mode haute sécurité.
3. Configurez Site Recovery sur le site local pour un environnement [Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou de [machines virtuelles VMware/serveurs physiques](site-recovery-vmware-to-azure-classic.md).
4. Utilisez la réplication Site Recovery pour répliquer la nouvelle instance SQL Server sur Azure. Si cette copie miroir de haute sécurité est synchronisée avec le cluster principal, elle est répliquée sur Azure à l’aide de la réplication Site Recovery.


![Cluster standard](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)

### <a name="failback-considerations"></a>Considérations en matière de restauration automatique

Pour les clusters SQL Server Standard, la restauration automatique après un basculement non planifié nécessite une sauvegarde et une restauration SQL Server à partir de l’instance miroir sur le cluster d’origine, puis le rétablissement de la copie miroir.

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus](site-recovery-components.md) sur l’architecture de Site Recovery.

