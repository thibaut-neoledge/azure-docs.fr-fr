---
title: "Répliquer des applications avec SQL Server et Azure Site Recovery | Microsoft Docs"
description: "Cet article décrit comment répliquer SQL Server à l’aide d’Azure Site Recovery et des fonctionnalités de récupération de SQL Server."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 3b606aa6dc3b84ed80cd3cc5452bbe1da6c79a8b
ms.openlocfilehash: 2d55db297bcef2c5789cb33a8791cf2c787a0789


---
# <a name="protect-sql-server-with-sql-server-disaster-recovery-and-azure-site-recovery"></a>Protéger SQL Server avec la récupération d’urgence SQL Server et Azure Site Recovery
Le service Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d’urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles et de serveurs physiques. Les machines peuvent être répliquées vers Azure ou vers un centre de données local secondaire. Pour obtenir un rapide aperçu, consultez [Qu’est-ce qu’Azure Site Recovery ?](site-recovery-overview.md)

 Cet article décrit comment protéger le serveur SQL Server principal d’une application en combinant les technologies BCDR de SQL Server et Azure Site Recovery. Vous devez avoir une bonne compréhension des capacités de récupération d’urgence de SQL Server (cluster de basculement, groupes de disponibilité AlwaysOn, mise en miroir de la base de données, envoi de journaux) et d’Azure Site Recovery avant de déployer les scénarios décrits dans cet article.

## <a name="overview"></a>Vue d'ensemble
De nombreuses charges de travail utilisent SQL Server comme base. Les applications SharePoint, Dynamics et SAP utilisent SQL Server pour mettre en œuvre des services de données.  Les applications déploient SQL Server de plusieurs façons :

* **SQL Server autonome**: le serveur SQL et toutes les bases de données sont hébergés sur un seul ordinateur (physique ou une machine virtuelle). Quand le serveur est virtualisé, le cluster hôte est utilisé pour la haute disponibilité locale. Aucune haute disponibilité pour le niveau invité n’est implémentée.
* **Instances de clustering de basculement SQL Server (Always On FCI)**: deux ou plusieurs nœuds d’instances SQL Server avec des disques partagés sont configurés dans un cluster de basculement Windows. Si l’une des instances de cluster est défaillante, le cluster peut opérer un basculement vers une autre instance SQL Server. Ce paramétrage est généralement utilisé pour la haute disponibilité sur un site principal. Cela ne protège pas contre la défaillance ou une panne dans la couche de stockage partagé. Le disque partagé peut être mis en œuvre avec iSCSI, Fibre channel ou VHDx partagé.
* **Groupes de disponibilité SQL Always On**: dans ce cas de figure, deux nœuds sont configurés dans un cluster sans partage avec des bases de données SQL Server configurées dans un groupe de disponibilité avec réplication synchrone et basculement automatique.

Dans les éditions Enterprise, SQL Server fournit également des technologies de récupération d’urgence natives pour la récupération des bases de données vers un site distant. Dans cet article, nous allons exploiter et intégrer les technologies de récupération d’urgence SQL natives suivantes :

* Groupe de disponibilité SQL Always On pour la récupération d’urgence pour SQL Server éditions 2012 ou 2014 Enterprise
* Mise en miroir de base de données SQL en mode haute sécurité pour SQL Server édition Standard (toute version) ou pour SQL server 2008 R2

Site Recovery peut protéger SQL Server comme décrit dans le tableau ci-dessous.

| **Local vers local** | **Local vers Azure** |
| --- | --- | --- |
| **Hyper-V** |Oui |
| **VMware** |Oui |
| **Serveur physique** |Oui |

## <a name="support-and-integration"></a>Prise en charge et intégration
Les scénarios de cet article prennent en charge les versions SQL Server suivantes :

* SQL Server 2014 Entreprise et Standard
* SQL Server 2012 Entreprise et Standard
* SQL Server 2008 R2 Entreprise et Standard

La récupération de sites peut être intégrée aux technologies BCDR SQL Server natives résumées dans le tableau ci-dessous pour fournir une solution de récupération d’urgence.

| **Fonctionnalité** | **Détails** | **Version de SQL Server** |
| --- | --- | --- |
| **Groupe de disponibilité AlwaysOn** |Plusieurs instances autonomes de SQL Server s’exécutent chacune dans un cluster de basculement qui comporte plusieurs nœuds.<br/><br/>Les bases de données peuvent être regroupées dans des groupes de basculement qui peuvent être copiés (mis en miroir) sur des instances de SQL Server, pour éviter tout stockage partagé.<br/><br/>Cette fonction assure une récupération d'urgence entre un site primaire et un ou plusieurs sites secondaires. Il est possible de configurer deux nœuds dans un cluster sans partage avec les bases de données SQL Server configurées dans un groupe de disponibilité avec réplication synchrone et basculement automatique. |SQL Server 2014 et 2012 édition Enterprise |
| **Cluster de basculement (instance de cluster de basculement AlwaysOn)** |SQL Server tire parti de la fonction de cluster de basculement Windows pour assurer la haute disponibilité des charges de travail SQL Server locales.<br/><br/>Les nœuds qui exécutent des instances de SQL Server avec des disques partagés sont configurés dans un cluster de basculement. Si une instance est arrêtée, le cluster bascule vers un autre.<br/><br/>Le cluster ne protège pas contre les défaillances ou les pannes en stockage partagé. Le disque partagé peut être implémenté avec iSCSI, Fibre Channel ou VHDX partagé. |Éditions SQL Server Enterprise<br/><br/>Éditions SQL Server Standard (limitée à deux nœuds) |
| **Mise en miroir de base de données (mode haute sécurité)** |Protège une base de données sur une seule copie secondaire. Disponible dans les modes de réplication haute sécurité (synchrone) et hautes performances (asynchrone). Cluster de basculement non requis. |SQL Server 2008 R2<br/><br/>SQL Server Enterprise (toutes les éditions) |
| **Serveur SQL autonome** |SQL Server et la base de données sont hébergés sur un seul serveur (physique ou virtuel). Le cluster hôte est utilisé pour la haute disponibilité, si le serveur est virtuel. Aucune haute disponibilité pour le niveau invité. |Édition Enterprise ou Standard |

## <a name="deployment-recommendations"></a>Recommandations concernant le déploiement
Ce tableau récapitule nos recommandations pour intégrer les technologies BCDR de SQL Server à Site Recovery.

| **Version** | **Édition** | **Déploiement** | **Local à local** | **Local vers Azure** |
| --- | --- | --- | --- | --- |
| SQL Server 2014 ou 2012 |Entreprise |Instance de cluster de basculement |Groupes de disponibilité AlwaysOn |Groupes de disponibilité AlwaysOn |
| Entreprise |Groupes de disponibilité AlwaysOn pour la haute disponibilité |Groupes de disponibilité AlwaysOn |Groupes de disponibilité AlwaysOn | |
| Standard |Instance de cluster de basculement (FCI) |Réplication Site Recovery avec miroir local |Réplication Site Recovery avec miroir local | |
| Enterprise ou Standard |Standalone |Réplication de la récupération de sites |Réplication de la récupération de sites | |
| SQL Server 2008 R2 |Enterprise ou Standard |Instance de cluster de basculement (FCI) |Réplication Site Recovery avec miroir local |Réplication Site Recovery avec miroir local |
| Enterprise ou Standard |Standalone |Réplication de la récupération de sites |Réplication de la récupération de sites | |
| SQL Server (toute version) |Enterprise ou Standard |Instance de cluster de basculement - application DTC |Réplication de la récupération de sites |Non pris en charge |

## <a name="deployment-prerequisites"></a>Conditions préalables au déploiement
Voici ce dont vous avez besoin pour commencer :

* Un déploiement local de SQL Server exécutant une version prise en charge de SQL Server. En général, il faut également Active Directory pour votre serveur SQL.
* La configuration requise pour le scénario que vous souhaitez déployer. Vous trouverez le détail de la configuration requise dans chaque article de déploiement. Des liens vers ces articles sont fournis dans [Vue d’ensemble de Site Recovery](site-recovery-overview.md).
* Si vous souhaitez configurer la récupération dans Azure, vous devez exécuter l’outil [Azure Virtual Machine Readiness Assessment](http://www.microsoft.com/download/details.aspx?id=40898) sur vos ordinateurs virtuels SQL Server pour vous assurer qu'ils sont compatibles avec Azure et Site Recovery.

## <a name="set-up-active-directory"></a>Configurer Active Directory
Vous avez besoin d’Active Directory sur le site de récupération secondaire pour que SQL Server fonctionne correctement. Deux cas de figure peuvent se présenter :

* **Petite entreprise**: si vous avez un petit nombre d'applications et un seul contrôleur de domaine pour le site local et que vous souhaitez basculer l'ensemble du site, nous vous recommandons d'utiliser Site Recovery pour répliquer le contrôleur de domaine sur le centre de données secondaire ou Azure.
* **Moyenne ou grande entreprise**: si vous avez un grand nombre d'applications, que vous exécutez une forêt Active Directory et que vous souhaitez effectuer un basculement par application ou charge de travail, nous vous recommandons de configurer un contrôleur de domaine supplémentaire dans le centre de données secondaire ou dans Azure. Notez que si vous utilisez des groupes de disponibilité AlwaysOn pour effectuer une récupération sur un site distant, nous vous recommandons de configurer un contrôleur de domaine supplémentaire sur le site secondaire ou sur Azure, à utiliser pour l'instance SQL Server récupérée.

Les instructions fournies dans ce document supposent qu'un contrôleur de domaine est disponible sur l'emplacement secondaire. [ici](site-recovery-active-directory.md) .

## <a name="integrate-protection-with-sql-server-always-on-in-classic-azure-portal-on-premises-to-azure"></a>Intégrer la protection à SQL Server Always-On dans le portail Azure classique (localement vers Azure)
Site Recovery prend en charge SQL AlwaysOn en mode natif. Si vous avez créé un groupe de disponibilité SQL avec une machine virtuelle Azure configurée comme « Secondaire », vous pouvez utiliser Site Recovery pour gérer le basculement des groupes de disponibilité.

> [!NOTE]
> Cette fonctionnalité est actuellement en version préliminaire et disponible quand les serveurs hôtes Hyper-V du centre de données principal sont gérés dans des clouds VMM et quand la configuration VMware est gérée par un [Serveur de configuration](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites). Actuellement, cette fonctionnalité n’est pas disponible dans le nouveau portail Azure. Si vous utilisez le nouveau portail Azure, suivez les étapes de [cette section](site-recovery-sql.md#protect-machines-in-new-azure-portal-or-without-a-vmm-server-or-a-configuration-server-in-classic-azure-portal).
>
>

#### <a name="prerequisites"></a>Composants requis
Voici ce dont vous avez besoin pour intégrer SQL AlwaysOn à Site Recovery :

* Un serveur SQL Server local (serveur autonome ou cluster de basculement)
* Une ou plusieurs machines virtuelles Azure sur lesquelles est installé SQL Server.
* Groupe de disponibilité SQL configuré entre un serveur SQL Server local et celui exécuté dans Azure
* La communication à distance PowerShell doit être activée sur l’ordinateur SQL Server local. Le serveur VMM ou de configuration doit pouvoir effectuer des appels PowerShell distants vers le serveur SQL Server.
* Un compte d’utilisateur doit être ajouté sur le serveur SQL Server local, dans les groupes d’utilisateurs SQL avec au minimum les autorisations suivantes :
  * ALTER AVAILABILITY GROUP : autorisations [ici](https://msdn.microsoft.com/library/hh231018.aspx) et [ici](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
  * ALTER DATABASE : autorisations [ici](https://msdn.microsoft.com/library/ff877956.aspx#Security)
* Un compte d’identification doit être créé sur le serveur VMM ou un compte doit être créé sur le serveur de configuration à l’aide de CSPSConfigtool.exe pour l’utilisateur indiqué dans l’étape précédente
* Le module SQL PS doit être installé sur les serveurs SQL Server exécutés en local et sur des machines virtuelles Azure.
* L’agent de machine virtuelle doit être installé sur les machines virtuelles exécutées dans Azure.
* NTAUTHORITY\System doit comporter les autorisations suivantes sur le serveur SQL Server exécuté sur les machines virtuelles dans Azure :
  * ALTER AVAILABILITY GROUP : autorisations [ici](https://msdn.microsoft.com/library/hh231018.aspx) et [ici](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
  * ALTER DATABASE : autorisations [ici](https://msdn.microsoft.com/library/ff877956.aspx#Security)

#### <a name="step-1-add-a-sql-server"></a>Étape 1 : ajouter un serveur SQL Server
1. Cliquez sur **Ajouter un serveur SQL** pour ajouter un nouveau serveur SQL Server.

    ![Ajouter un serveur SQL](./media/site-recovery-sql/add-sql.png)
2. Dans **Configurer les paramètres SQL** > **Nom**, indiquez un nom convivial pour le serveur SQL Server.
3. **SQL Server (nom de domaine complet)** , indiquez le nom de domaine complet du serveur SQL Server source que vous souhaitez ajouter. Si le serveur SQL Server est installé sur un cluster de basculement, indiquez le nom de domaine complet du cluster et non celui des nœuds de cluster.  
4. Dans **Instance SQL Server** , choisissez l’instance par défaut ou indiquez le nom de l’instance personnalisée.
5. Dans **Serveur de gestion** , sélectionnez un serveur VMM ou un serveur de configuration inscrit dans le coffre Site Recovery. Site Recovery utilise ce serveur de gestion pour communiquer avec le serveur SQL Server.
6. Dans **Compte d’identification** , indiquez le nom d’un compte d’identification créé sur le serveur VMM spécifié ou du compte créé sur le serveur de configuration. Ce compte est utilisé pour accéder au serveur SQL Server et doit être doté des autorisations Lecture et Basculement sur les groupes de disponibilité présents sur l’ordinateur SQL Server.

    ![Boîte de dialogue Ajouter un serveur SQL](./media/site-recovery-sql/add-sql-dialog.png)

Une fois ajouté, le serveur SQL Server apparaît sous l’onglet **Serveurs SQL** .

![Liste des serveurs SQL Server](./media/site-recovery-sql/sql-server-list.png)

#### <a name="step-2-add-a-sql-availability-group"></a>Étape 2 : ajouter un groupe de disponibilité SQL
1. Une fois l’ordinateur SQL Server ajouté, l’étape suivante consiste à ajouter le groupe de disponibilité à Site Recovery. Pour cela, descendez dans la hiérarchie du serveur SQL Server ajouté à l’étape précédente et cliquez sur Ajouter un groupe de disponibilité SQL.

    ![Ajouter un groupe de disponibilité SQL](./media/site-recovery-sql/add-sqlag.png)
2. Le groupe de disponibilité SQL peut répliquer sur une ou plusieurs machines virtuelles dans Azure. Quand vous ajoutez un groupe de disponibilité SQL, vous devez fournir le nom et l’abonnement de la machine virtuelle Azure sur laquelle vous voulez que Site Recovery bascule le groupe de disponibilité.

    ![Boîte de dialogue Ajouter un groupe de disponibilité SQL](./media/site-recovery-sql/add-sqlag-dialog.png)
3. Dans l’exemple ci-dessus, le groupe de disponibilité DB1-AG devient Principal sur la machine virtuelle SQLAGVM2 exécutée au sein de l’abonnement DevTesting2 sur un basculement.

> [!NOTE]
> Seuls les groupes de disponibilité ayant le statut Principal sur le serveur SQL Server ajouté à l’étape précédente peuvent être ajoutés à Site Recovery. Si vous avez défini un groupe de disponibilité comme principal sur le serveur SQL Server ou si vous avez ajouté d’autres groupes de disponibilité au serveur SQL Server une fois celui-ci ajouté, actualisez-le à l’aide de l’option Actualiser figurant dans le serveur SQL Server.
>
>

#### <a name="step-3-create-a-recovery-plan"></a>Étape 3 : créer un plan de récupération
L’étape suivante consiste à créer un plan de récupération à l’aide des machines virtuelles et des groupes de disponibilité.
Sélectionnez le serveur VMM ou de configuration utilisé à l’étape&1; comme source, et Microsoft Azure comme cible.

![Créer un plan de récupération](./media/site-recovery-sql/create-rp1.png)

![Créer un plan de récupération](./media/site-recovery-sql/create-rp2.png)

Dans l’exemple, l’application Sharepoint est composée de 3 machines virtuelles qui utilisent un groupe de disponibilité SQL comme serveur principal. Dans ce plan de récupération, vous pouvez sélectionner à la fois le groupe de disponibilité et la machine virtuelle qui composent l’application.

Vous pouvez personnaliser davantage le plan de récupération en déplaçant les machines virtuelles vers d’autres groupes de basculement pour définir l’ordre du basculement. Le groupe de disponibilité est toujours basculé en premier, étant donné qu’il peut être utilisé comme serveur principal d’une application.

![Personnaliser un plan de récupération](./media/site-recovery-sql/customize-rp.png)

#### <a name="step-4--fail-over"></a>Étape 4 : Basculer
Différentes options de basculement sont disponibles une fois le groupe de disponibilité ajouté à un plan de récupération.

| Basculement | Détails |
| --- | --- |
| **Basculement planifié** |Un basculement planifié implique un basculement sans perte de données. Pour cela, le mode de disponibilité du groupe de disponibilité SQL est d’abord défini sur Synchrone, puis un basculement est déclenché pour définir le groupe de disponibilité comme Principal sur la machine virtuelle fournie au moment de l’ajout du groupe de disponibilité à Site Recovery. Une fois le basculement terminé, le mode de disponibilité est défini sur la valeur qu’il avait avant le déclenchement du basculement planifié. |
| **Basculement non planifié** |Un basculement non planifié peut entraîner une perte de données. Au moment du déclenchement du basculement non planifié, le mode de disponibilité du groupe de disponibilité n’est pas modifié. Il est défini sur principal sur la machine virtuelle fournie au moment de l’ajout du groupe de disponibilité à Site Recovery. Une fois le basculement non planifié terminé, et le serveur local exécutant SQL Server de nouveau disponible, la réplication inverse doit être déclenchée sur le groupe de disponibilité. Notez que cette action n’est pas disponible sur le plan de récupération et peut être exécutée sur le groupe de disponibilité SQL sous l’onglet Serveurs SQL Server. |
| **Test de basculement** |Le test de basculement pour le groupe de disponibilité SQL n’est pas pris en charge. Si vous déclenchez un test de basculement pour un plan de récupération contenant un groupe de disponibilité SQL, le basculement est ignoré pour le groupe de disponibilité. |

Considérez les options de basculement ci-dessous.

| Option | Détails |
| --- | --- |
| **Option 1 :** |1. Effectuez un test de basculement de l'application et des couches frontales.<br/><br/>2. Mettez à jour la couche applicative pour accéder à la copie du réplica en lecture seule et effectuez un test en lecture seule de l'application. |
| **Option 2 :** |1. Créez une copie de l'instance d’ordinateur virtuel SQL Server répliquée (à l'aide de clone VMM pour la sauvegarde Azure ou de site à site), et copiez-la dans un réseau de test.<br/><br/> 2. Testez le basculement à l'aide du plan de récupération. |

#### <a name="step-5-fail-back"></a>Étape 5 : effectuer une restauration automatique

Si vous voulez définir de nouveau le groupe de disponibilité comme principal sur le serveur SQL Server local, vous devez déclencher un basculement planifié sur le plan de récupération et choisir le sens de Microsoft Azure vers le serveur VMM local.

> [!NOTE]
> Après un basculement non planifié, la réplication inverse doit être déclenchée sur le groupe de disponibilité pour reprendre la réplication. La réplication est interrompue jusqu’à ce que cette opération soit terminée.
>
>

### <a name="protect-machines-in-new-azure-portal-or-without-a-vmm-server-or-a-configuration-server-in-classic-azure-portal"></a>Protéger les machines dans le nouveau portail Azure ou sans un serveur VMM ou un serveur de configuration dans le portail Azure classique
Pour les environnements qui ne sont pas gérés par un serveur VMM ou de configuration, les runbooks Azure Automation peuvent être utilisés pour configurer un basculement de groupes de disponibilité SQL par script. Pour configurer cela, procédez comme suit :

1. Créez un fichier local pour le script qui bascule un groupe de disponibilité. Cet exemple de script spécifie le chemin d'accès au groupe de disponibilité sur le réplica Azure et le bascule vers cette instance de réplica. Ce script s’exécutera sur l’ordinateur virtuel du réplica SQL Server par l’intermédiaire de l'extension de script personnalisé.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

1. Chargez le script dans un objet blob d’un compte de stockage Azure. Utilisez cet exemple :

        $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
        Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

1. Créez un runbook Azure Automation pour appeler les scripts sur l'ordinateur virtuel du réplica SQL Server dans Azure. Pour ce faire, utilisez cet exemple de script. [cliquez ici](site-recovery-runbook-automation.md) .

1. Lorsque vous créez un plan de récupération pour l’application, ajoutez une étape de script « démarrage avant le groupe 1 » qui exécute le runbook d’automatisation pour basculer vers le groupe de disponibilité.


1. **Test de basculement** : SQL AlwaysOn ne prend pas en charge le test de basculement de manière native. Par conséquent, la méthode recommandée est la suivante :
    1. Configurez la [Sauvegarde Azure](../backup/backup-azure-vms.md) sur la machine virtuelle qui héberge le réplica du groupe de disponibilité dans Azure.
    1. Avant de déclencher le test de basculement du plan de récupération, récupérez la machine virtuelle à partir de la sauvegarde effectuée à l’étape&1;
    1. Effectuer le test de basculement du plan de récupération


> [!NOTE]
> Le script ci-dessous suppose que le groupe de disponibilité SQL est hébergé sur une machine virtuelle Azure classique et que le nom de la machine virtuelle restaurée à l’étape&2; est SQLAzureVM-Test. Modifiez le script en fonction du nom que vous utilisez pour la machine virtuelle récupérée.
>
>


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
     }

## <a name="integrate-protection-with-sql-alwayson-on-premises-to-on-premises"></a>Intégrer la protection à SQL AlwaysOn (localement vers localement)
Si le serveur SQL utilise des groupes de disponibilité pour la haute disponibilité ou une instance de cluster de basculement, nous vous recommandons d’utiliser également les groupes de disponibilité sur le site de récupération. Notez que ceci vaut pour les applications qui n’utilisent pas les transactions distribuées.

1. [Configurez les bases de données](https://msdn.microsoft.com/library/hh213078.aspx) dans des groupes de disponibilité.
2. Créez un réseau virtuel sur le site secondaire.
3. Configurez un VPN de site à site entre le nouveau réseau virtuel et le site primaire.
4. Créez un ordinateur virtuel sur le site de récupération et installez SQL Server dessus.
5. Étendez les groupes de disponibilité AlwaysOn existants au nouvel ordinateur virtuel SQL Server. Configurez cette instance SQL Server comme une copie de réplica asynchrone.
6. Créez un écouteur de groupe de disponibilité ou modifiez l'écouteur existant pour inclure l’ordinateur virtuel de réplica asynchrone.
7. Vérifiez que la batterie de serveurs d'application est configurée pour utiliser l'écouteur. Si elle est configurée pour utiliser le nom du serveur de base de données, mettez-la à jour pour qu’elle utilise l'écouteur, afin de ne pas avoir à la reconfigurer après le basculement.

Pour les applications qui utilisent des transactions distribuées, nous vous recommandons d’utiliser [Site Recovery avec la réplication SAN](site-recovery-vmm-san.md) ou la [réplication de site à site avec serveur physique/VMware](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Considérations concernant le plan de récupération
1. Ajoutez cet exemple de script à la bibliothèque VMM sur les sites primaire et secondaire.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force
2. Lorsque vous créez un plan de récupération pour l'application, ajoutez une étape de script « démarrage avant le groupe 1 » qui exécute le script qui bascule des groupes de disponibilité.

## <a name="protect-a-standalone-sql-server"></a>Protéger un serveur SQL Server autonome
Dans cette configuration, nous vous recommandons d’utiliser la réplication Site Recovery pour protéger l'ordinateur SQL Server. La procédure varie selon que SQL Server est configuré comme un ordinateur virtuel ou un serveur physique, et que vous souhaitez répliquer sur Azure ou un site local secondaire. Obtenez des instructions pour tous les scénarios de déploiement dans [Présentation d’Azure Site Recovery](site-recovery-overview.md).

## <a name="protect-a-sql-server-cluster-standard-or-2008-r2"></a>Protéger un cluster de SQL Server (Standard ou 2008 R2)
Pour un cluster exécutant SQL Server Standard Edition ou SQL Server 2008 R2, nous vous recommandons d’utiliser la réplication Site Recovery pour protéger SQL Server.

### <a name="on-premises-to-on-premises"></a>Local vers local
* Si l’application utilise des transactions distribuées, nous vous recommandons de déployer [Site Recovery avec la réplication SAN](site-recovery-vmm-san.md) pour un environnement Hyper-V et [serveur physique/VMware vers VMware](site-recovery-vmware-to-vmware.md) pour un environnement VMware.
* Pour les applications non-DTC, tirez parti de l'approche ci-dessus pour récupérer le cluster comme un serveur autonome en exploitant une mise en miroir de base de données locale haute sécurité.

### <a name="on-premises-to-azure"></a>Local vers Azure
Site Recovery ne prend pas en charge le cluster invité lors de la réplication vers Azure. De même, SQL Server n’inclut aucune solution de récupération d'urgence à faible coût dans l’édition Standard. Nous vous recommandons de protéger le cluster SQL Server local sur un serveur SQL Server autonome et de le récupérer dans Azure.

1. Configurez une instance SQL Server autonome supplémentaire sur le site local.
2. Configurez cette instance comme une copie miroir des bases de données à protéger. Configurez la mise en miroir en mode haute sécurité.
3. Configurez Site Recovery sur le site local en fonction de l’environnement ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou [serveur physique/VMware](site-recovery-vmware-to-azure-classic.md)).
4. Utilisez la réplication Site Recovery pour répliquer la nouvelle instance SQL Server sur Azure. Cette copie miroir de haute sécurité est synchronisée avec le cluster primaire, mais elle est répliquée sur Azure avec la réplication de la récupération de sites.

La figure suivante illustre cette configuration.

![Cluster standard](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)

### <a name="failback-considerations"></a>Considérations en matière de restauration automatique
Pour les clusters SQL standard, la restauration automatique après un basculement non planifié nécessite une sauvegarde SQL, une restauration à partir de l’instance miroir sur le cluster d’origine, puis le rétablissement de la copie miroir.

## <a name="next-steps"></a>Étapes suivantes
[Découvrez](site-recovery-best-practices.md) comment préparer le déploiement de Site Recovery.



<!--HONumber=Jan17_HO5-->


