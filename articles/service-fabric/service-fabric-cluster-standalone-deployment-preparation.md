---
title: "Préparation du déploiement d’un cluster Azure Service Fabric autonome | Microsoft Docs"
description: "Documentation relative à la préparation de l’environnement et la création de la configuration du cluster, à prendre en considération avant de déployer un cluster conçu pour gérer une charge de travail de production."
services: service-fabric
documentationcenter: .net
author: maburlik
manager: timlt
editor: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/17/2017
ms.author: maburlik;chackdan
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: bd9ea82adc97698957dfebc3ee7c14c498808dd3
ms.lasthandoff: 03/29/2017


---

<a id="preparemachines"></a>

## <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Planifier et préparer votre déploiement de cluster Service Fabric autonome
Exécutez les étapes suivantes avant de créer votre cluster.

### <a name="step-1-plan-your-cluster-infrastructure"></a>Étape 1 : planifier votre infrastructure de cluster
Vous allez créer un cluster Service Fabric sur les ordinateurs que vous avez pour décider des types de défaillance auxquels le cluster doit survivre. Par exemple, devez-vous séparer les lignes d’alimentation des connexions internet reliées à ces ordinateurs ? Prenez en outre en compte la sécurité physique de ces ordinateurs. Où sont situés les ordinateurs et qui a besoin d’y accéder ? Une fois ces décisions prises, vous pouvez mapper logiquement les ordinateurs sur les différents domaines d’erreur (voir l’étape 4). La planification de l’infrastructure pour les clusters de production est plus complexe que pour les clusters de test.

### <a name="step-2-prepare-the-machines-to-meet-the-prerequisites"></a>Étape 2 : préparer les ordinateurs pour répondre aux conditions préalables
Conditions préalables pour chaque ordinateur que vous souhaitez ajouter au cluster :

* Un minimum de 16 Go de RAM est recommandé.
* Un minimum de 40 Go d’espace disque disponible est recommandé.
* Un processeur 4 cœurs ou plus est recommandé.
* Connectivité à un ou plusieurs réseaux sécurisés pour tous les ordinateurs.
* Windows Server 2012 R2 ou Windows Server 2016. 
* [.NET Framework 4.5.1 ou une version ultérieure](https://www.microsoft.com/download/details.aspx?id=40773), installation complète.
* [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell).
* Le [service RemoteRegistry](https://technet.microsoft.com/library/cc754820) doit être exécuté sur tous les ordinateurs.

L’administrateur de cluster déployant et configurant le cluster doit disposer de [privilèges d’administrateur](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) sur chaque ordinateur. Vous ne pouvez pas installer Service Fabric sur un contrôleur de domaine.

### <a name="step-3-determine-the-initial-cluster-size"></a>Étape 3 : déterminer la taille initiale du cluster
Le runtime Service Fabric est déployé sur chaque nœud d’un cluster Service Fabric autonome, et les nœuds sont membres du cluster. Un déploiement de production type comprend un nœud par instance du système d’exploitation (physique ou virtuel). La taille de cluster est déterminée par les besoins de votre entreprise. Toutefois, vous devez disposer d’une taille minimale de cluster de trois nœuds (ordinateurs ou machines virtuelles).
À des fins de développement, vous pouvez avoir plusieurs nœuds sur un ordinateur donné. Dans un environnement de production, Service Fabric ne prend en charge qu’un seul nœud par ordinateur physique ou virtuel.

### <a name="step-4-determine-the-number-of-fault-domains-and-upgrade-domains"></a>Étape 4 : déterminer le nombre de domaines d’erreur et de domaines de mise à niveau
Un *domaine d’erreur (FD)* est une unité physique de défaillance directement liée à l’infrastructure physique dans les centres de données. Un domaine d’erreur est constitué de composants matériels (ordinateurs, commutateurs, réseaux, etc.) qui partagent un point de défaillance unique. Bien qu’il n’existe aucun mappage 1:1 entre les domaines d’erreur et les racks, chaque rack peut être considéré au sens large comme un domaine d’erreur. Lorsque vous envisagez d’utiliser les nœuds de votre cluster, nous vous recommandons fortement de distribuer les nœuds sur au moins trois domaines d’erreur.

Quand vous spécifiez des domaines d’erreur dans ClusterConfig.json, vous pouvez choisir le nom de chaque domaine d’erreur. Service Fabric prend en charge les domaines d’erreur hiérarchiques pour vous permettre d’y refléter votre topologie d’infrastructure.  Par exemple, les domaines d’erreur suivants sont valides :

* "faultDomain": "fd:/Room1/Rack1/Machine1"
* "faultDomain": "fd:/FD1"
* "faultDomain": "fd:/Room1/Rack1/PDU1/M1"

Un *domaine de mise à niveau (UD)* est une unité logique de nœuds. Pendant les mises à niveau orchestrées par Service Fabric (mise à niveau de l’application ou du cluster), tous les nœuds dans un domaine de mise à niveau sont mis hors service pour effectuer la mise à niveau alors que les nœuds dans d’autres domaines de mise à niveau restent disponibles pour répondre aux requêtes. Les mises à niveau du microprogramme que vous effectuez sur vos ordinateurs ne respectent pas les domaines de mise à niveau. Vous devez donc les exécuter sur un ordinateur à la fois.

L’approche la plus simple de ces concepts consiste à considérer les domaines d’erreur comme unité de défaillance non planifiée et les domaines mise à niveau comme unité de maintenance planifiée.

Quand vous spécifiez des domaines de mise à niveau dans ClusterConfig.json, vous pouvez choisir le nom de chaque domaine de mise à niveau. Par exemple, les noms suivants sont valides :

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Blue"

Pour plus d’informations sur les domaines de mise à niveau et les domaines d’erreur, consultez [Description d’un cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md) .

### <a name="step-5-download-the-service-fabric-standalone-package-for-windows-server"></a>Étape 5 : télécharger le package autonome Service Fabric pour Windows Server
[Lien de téléchargement - Package autonome Service Fabric - Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) et décompressez le package sur un ordinateur de déploiement qui ne fait pas partie du cluster ou sur l’un des ordinateurs qui fera partie de votre cluster.

### <a name="step-6-modify-cluster-configuration"></a>Étape 6 : Modifier la configuration du cluster
Pour créer un cluster autonome, vous devez créer un fichier ClusterConfig.json de configuration de cluster autonome qui décrit les spécifications du cluster. Vous pouvez baser le fichier de configuration sur les modèles accessibles avec le lien ci-dessous. <br>
[Configurations de cluster autonome](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Pour plus d’informations sur les sections de ce fichier, consultez [Paramètres de configuration pour un cluster Windows autonome](service-fabric-cluster-manifest.md).

Ouvrez l’un des fichiers ClusterConfig.json à partir du package que vous avez téléchargé et modifiez les paramètres suivants :
| **Paramètres de configuration** | **Description** |
| --- | --- |
| **NodeTypes** |Les types de nœuds permettent de séparer vos nœuds de cluster en différents groupes. Un cluster doit comprendre au moins un NodeType. Tous les nœuds d’un groupe ont les caractéristiques communes suivantes :  <br> **Name** : il s’agit du nom de type de nœud. <br>**Endpoints Ports** : il s’agit de différents points de terminaison (ports) nommés qui sont associés à ce type de nœud. Vous pouvez utiliser n’importe quel numéro de port, tant qu’il n’entre pas en conflit avec un autre élément de ce manifeste et qu’il n’est pas déjà utilisé par une autre application en cours d’exécution sur l’ordinateur/la machine virtuelle. <br> **Placement Properties** : il s’agit des propriétés pour ce type de nœud que vous avez utilisées comme contraintes de positionnement pour les services système ou pour vos services. Ces propriétés sont des paires clé/valeur définies par l’utilisateur qui fournissent des métadonnées supplémentaires pour un nœud donné. La présence d’un disque dur ou d’une carte graphique sur le nœud, le nombre de rotations du disque dur, les noyaux et d’autres propriétés physiques sont des exemples de propriétés du nœud. <br> **Capacities** : les capacités du nœud définissent le nom et la quantité d’une ressource spécifique disponible sur un nœud particulier pour consommation. Par exemple, un nœud peut définir qu’il possède la capacité pour une mesure appelée « MemoryInMb » et qu’il dispose de 2 048 Mo de mémoire disponible par défaut. Ces capacités sont utilisées au moment de l’exécution pour garantir que les services qui nécessitent une quantité spécifique de ressources sont placés sur des nœuds où ces ressources sont disponibles dans la quantité requise.<br>**IsPrimary** : si plusieurs NodeType sont définis, vérifiez qu’un seul est défini comme primaire avec la valeur *true*; c’est là que les services système s’exécutent. Tous les autres types de nœuds doivent avoir la valeur *false* |
| **Nœuds** |Il s’agit des détails de chacun des nœuds qui font partie du cluster (type de nœud, nom de nœud, adresse IP, domaine d’erreur et domaine de mise à niveau du nœud). Les ordinateurs sur lesquels vous souhaitez créer le cluster doivent être répertoriés ici avec leur adresse IP. <br> Si vous utilisez la même adresse IP pour tous les nœuds, un cluster à boîtier unique est créé. Vous pouvez l’utiliser à des fins de test. N’utilisez pas les clusters à boîtier unique pour le déploiement de charges de travail de production. |

Une fois tous les paramètres de la configuration du cluster configurés dans l’environnement, vous pouvez tester la configuration dans l’environnement du cluster (étape 7).

<a id="environmentsetup"></a>

### <a name="step-7-environment-setup"></a>Étape 7. Configuration de l’environnement

Lorsqu’un administrateur de cluster configure un cluster autonome Service Fabric, l’environnement doit être configuré avec les critères suivants : <br>
1. L’utilisateur qui crée le cluster doit disposer de privilèges de sécurité de niveau administrateur sur tous les ordinateurs qui sont répertoriés en tant que nœuds dans le fichier de configuration du cluster.
2. L’ordinateur à partir duquel le cluster est créé, ainsi que chaque ordinateur nœud du cluster doit respecter les conditions suivantes :
* le Kit de développement logiciel (SDK) Service Fabric doit être désinstallé ;
* le runtime Service Fabric doit être désinstallé ; 
* le service pare-feu Windows (mpssvc) doit être activé ;
* le service Registre distant (remoteregistry) doit être activé ;
* le partage de fichiers (SMB) doit être activé ;
* les ports requis doivent être ouverts, selon les ports de configuration du cluster ;
* les ports requis doivent êtres ouverts pour le service Registre distant et SMB Windows : 135, 137, 138, 139 et 445 ;
* la connectivité réseau entre les ordinateurs doit être activée.
3. Aucun des ordinateurs nœuds de cluster ne doit être un contrôleur de domaine.
4. Si le cluster à déployer est un cluster sécurisé, vérifiez que les conditions préalables de sécurité requises sont en place et correctement configurées en fonction de la configuration.
5. Si les ordinateurs du cluster ne sont pas accessibles via Internet, définissez les éléments suivants dans la configuration du cluster :
* Désactiver la télémétrie : sous *Propriétés*, définissez   *« enableTelemetry » : false*
* Désactiver le téléchargement de version Fabric automatique et les notifications indiquant que la prise en charge de la version actuelle touche à sa fin : sous *Propriétés*, définissez   *« fabricClusterAutoupgradeEnabled » : true*
6. Définissez les exclusions antivirus Service Fabric appropriées :

| **Répertoires exclus de l’antivirus** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (dans la configuration du cluster) |
| FabricLogRoot (dans la configuration du cluster) |

| **Processus exclus de l’antivirus** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |

### <a name="step-8-validate-environment-using-testconfiguration-script"></a>Étape 8 : Valider l’environnement à l’aide du script TestConfiguration
Vous trouverez le script TestConfiguration.ps1 dans le package autonome. Il est utilisé comme un outil d’analyse des meilleures pratiques pour valider certains des critères ci-dessus et doit être utilisé comme contrôle de la validité pour vérifier si un cluster peut être déployé dans un environnement donné. En cas de défaillance, consultez la liste sous [Configuration de l’environnement](service-fabric-cluster-standalone-deployment-preparation.md) pour la résolution des problèmes. 

Ce script peut être exécuté sur n’importe quel ordinateur disposant d’un accès administrateur à tous les ordinateurs qui sont répertoriés en tant que nœuds dans le fichier de configuration du cluster. L’ordinateur sur lequel ce script est exécuté ne doit pas nécessairement faire partie du cluster.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 : True
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
Passed                     : True
```

Actuellement, ce module de test de configuration ne valide pas la configuration de sécurité. Cette opération doit donc être effectuée de manière indépendante.  

> [!NOTE]
> Nous améliorons en permanence ce module afin de le rendre plus fiable. Par conséquent, s’il existe un cas défectueux ou manquant qui, selon vous, n’est pas actuellement détecté par TestConfiguration, faites-le nous savoir via nos [canaux de prise en charge](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-support).   
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* [Créer un cluster autonome s’exécutant sur Windows Server](service-fabric-cluster-creation-for-windows-server.md)

