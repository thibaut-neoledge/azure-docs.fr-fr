---
title: "Créer et gérer un cluster Azure Service Fabric autonome | Microsoft Docs"
description: "Créez et gérez un cluster Azure Service Fabric sur n’importe quel ordinateur (physique ou virtuel) exécutant Windows Server, qu’il soit local ou dans un cloud."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 31349169-de19-4be6-8742-ca20ac41eb9e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2016
ms.author: dkshir;chackdan
translationtype: Human Translation
ms.sourcegitcommit: 6db229794c90e985de834bd3bfb6e0b030de2df5
ms.openlocfilehash: 0cb59a2e94a9c985cb56d9dd20c05e2e22a45151


---
# <a name="create-and-manage-a-cluster-running-on-windows-server"></a>Créer et gérer un cluster exécuté sur Windows Server
Vous pouvez utiliser Azure Service Fabric pour créer des clusters Service Fabric sur toute machine virtuelle ou tout ordinateur exécutant Windows Server. Cela signifie que vous pouvez déployer et exécuter des applications Service Fabric dans n’importe quel environnement contenant un ensemble d’ordinateurs Windows Server interconnectés, que ce soit en local ou avec un fournisseur cloud. Service Fabric fournit un package d’installation pour créer des clusters Service Fabric, appelé package Windows Server autonome.

Cet article vous guide tout au long des étapes de création locale d’un cluster à l’aide d’un package autonome Service Fabric, même s’il peut être facilement adapté à tout autre environnement, notamment d’autres fournisseurs de cloud.

> [!NOTE]
> Ce package de Windows Server autonome est commercialisé et peut être utilisé pour les déploiements de production. Ce package peut contenir de nouvelles fonctionnalités Service Fabric en version « préliminaire ». Faites défiler jusqu'à la section « Fonctionnalités préliminaires incluses dans ce package. » pour obtenir la liste des fonctionnalités préliminaires. Vous pouvez [télécharger une copie du CLUF](http://go.microsoft.com/fwlink/?LinkID=733084) maintenant.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-standalone-package"></a>Obtenir de l’aide pour le package autonome Service Fabric
* Interrogez la Communauté sur le package autonome Service Fabric pour Windows Server dans le [forum Azure Service Fabric](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Ouvrez un ticket pour obtenir le [support professionnel Service Fabric](http://support.microsoft.com/oas/default.aspx?prid=16146).  En savoir plus sur le [support professionnel Microsoft](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Vous pouvez également bénéficier du support pour ce package dans le cadre du [Support Premier Microsoft](https://support.microsoft.com/en-us/premier)  

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-standalone-package"></a>Télécharger le package autonome Service Fabric
[Téléchargez le package autonome Service Fabric pour Windows Server 2012 R2 et versions ultérieures](http://go.microsoft.com/fwlink/?LinkId=730690), intitulé Microsoft.Azure.ServiceFabric.WindowsServer.&lt;version&gt;.zip.

Vous trouverez les fichiers suivants dans le package de téléchargement :

| **Nom de fichier** | **Brève description** |
| --- | --- |
| MicrosoftAzureServiceFabric.cab |Fichier CAB qui contient les fichiers binaires déployés sur chaque ordinateur du cluster. |
| ClusterConfig.Unsecure.DevCluster.json |Un exemple de fichier de configuration de cluster qui contient les paramètres pour un cluster de développement avec ordinateur unique (ou machine virtuelle) à trois nœuds non sécurisé, notamment les informations de chaque nœud du cluster. |
| ClusterConfig.Unsecure.MultiMachine.json |Un exemple de fichier de configuration de cluster qui contient les paramètres pour un cluster avec plusieurs ordinateurs (ou machines virtuelles) non sécurisé, notamment les informations de chaque ordinateur du cluster. |
| ClusterConfig.Windows.DevCluster.json |Un exemple de fichier de configuration de cluster qui contient tous les paramètres pour un cluster de développement avec ordinateur unique (ou machine virtuelle) à trois nœuds sécurisé, notamment les informations de chaque nœud se trouvant dans le cluster. Le cluster est sécurisé à l’aide [d’identités Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.Windows.MultiMachine.json |Un exemple de fichier de configuration de cluster qui contient tous les paramètres pour un cluster avec plusieurs ordinateurs (ou machines virtuelles) sécurisé, utilisant la sécurité Windows, notamment les informations de chaque ordinateur du cluster sécurisé. Le cluster est sécurisé à l’aide [d’identités Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.x509.DevCluster.json |Un exemple de fichier de configuration de cluster qui contient les paramètres pour un cluster de développement avec ordinateur unique (ou machine virtuelle) à trois nœuds sécurisé, notamment les informations de chaque nœud du cluster. Le cluster est sécurisé à l’aide de certificats x509. |
| ClusterConfig.x509.MultiMachine.json |Un exemple de fichier de configuration de cluster qui contient tous les paramètres pour le cluster à plusieurs ordinateurs (ou machines virtuelles) sécurisé, notamment les informations de chaque nœud du cluster sécurisé. Le cluster est sécurisé à l’aide de certificats x509. |
| EULA_ENU.txt |Les termes du contrat de licence pour l’utilisation du package Windows Server autonome Microsoft Azure Service Fabric. Vous pouvez [télécharger une copie du CLUF](http://go.microsoft.com/fwlink/?LinkID=733084) maintenant. |
| Readme.txt |Un lien vers les notes de publication et des instructions d’installation de base. Il s’agit d’une partie des instructions figurant dans ce document. |
| CreateServiceFabricCluster.ps1 |Un script PowerShell qui crée le cluster à l’aide des paramètres figurant dans ClusterConfig.json. |
| RemoveServiceFabricCluster.ps1 |Un script PowerShell qui supprime un cluster à l’aide des paramètres figurant ClusterConfig.json. |
| ThirdPartyNotice.rtf |Informations sur le logiciel tiers du package. |
| AddNode.ps1 |Un script PowerShell pour l’ajout d’un nœud à un cluster déployé existant. |
| RemoveNode.ps1 |Un script PowerShell pour la suppression d’un nœud d’un cluster déployé existant. |
| CleanFabric.ps1 |Un script PowerShell pour supprimer une installation autonome Service Fabric de l’ordinateur actuel. Les installations MSI précédentes doivent être supprimées à l’aide de leur propre programme de désinstallation associé. |
| TestConfiguration.ps1 |Un script PowerShell pour l’analyse de l’infrastructure comme spécifié dans Cluster.json. |

## <a name="plan-and-prepare-your-cluster-deployment"></a>Planifier et préparer le déploiement de cluster
Exécutez les étapes suivantes avant de créer votre cluster.

### <a name="step-1-plan-your-cluster-infrastructure"></a>Étape 1 : planifier votre infrastructure de cluster
Vous allez créer un cluster Service Fabric sur les ordinateurs que vous avez pour décider des types de défaillance auxquels le cluster doit survivre. Par exemple, devez-vous séparer les lignes d’alimentation des connexions internet reliées à ces ordinateurs ? Prenez en outre en compte la sécurité physique de ces ordinateurs. Où sont situés les ordinateurs et qui a besoin d’y accéder ? Une fois ces décisions prises, vous pouvez mapper logiquement les ordinateurs sur les différents domaines d’erreur (voir l’étape 4). La planification de l’infrastructure pour les clusters de production est plus complexe que pour les clusters de test.

<a id="preparemachines"></a>

### <a name="step-2-prepare-the-machines-to-meet-the-prerequisites"></a>Étape 2 : préparer les ordinateurs pour répondre aux conditions préalables
Conditions préalables pour chaque ordinateur que vous souhaitez ajouter au cluster :

* Un minimum de 16 Go de RAM est recommandé.
* Un minimum de 40 Go d’espace disque disponible est recommandé.
* Un processeur 4 cœurs ou plus est recommandé.
* Connectivité à un ou plusieurs réseaux sécurisés pour tous les ordinateurs.
* Windows Server 2012 R2 ou Windows Server 2012 ( [KB2858668](https://support.microsoft.com/kb/2858668) doit être installé).
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
[Téléchargez le package autonome Service Fabric pour Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) et décompressez le package sur un ordinateur de déploiement qui ne fait pas partie du cluster ou sur l’un des ordinateurs qui fera partie de votre cluster. Vous pouvez renommer le dossier décompressé `Microsoft.Azure.ServiceFabric.WindowsServer`.

<a id="createcluster"></a>

## <a name="create-your-cluster"></a>Création de votre cluster
Une fois la planification et la préparation ci-dessus effectuées, vous êtes prêt à créer votre cluster.

### <a name="step-1-modify-cluster-configuration"></a>Étape 1 : modifier la configuration du cluster
Le cluster est décrit dans un ClusterConfig.json. Pour plus d’informations sur les sections de ce fichier, consultez [Paramètres de configuration pour un cluster Windows autonome](service-fabric-cluster-manifest.md).
Ouvrez l’un des fichiers ClusterConfig.json à partir du package que vous avez téléchargé et modifiez les paramètres suivants :

<!--Loc Comment: Please, check that line 129 the clause has been modified to "that you use as placement constraints" instead of using "you are used as placement constraints"-->

| **Paramètres de configuration** | **Description** |
| --- | --- |
| **NodeTypes** |Les types de nœuds permettent de séparer vos nœuds de cluster en différents groupes. Un cluster doit comprendre au moins un NodeType. Tous les nœuds d’un groupe ont les caractéristiques communes suivantes :  <br> **Name** : il s’agit du nom de type de nœud. <br>**Endpoints Ports** : il s’agit de différents points de terminaison (ports) nommés qui sont associés à ce type de nœud. Vous pouvez utiliser n’importe quel numéro de port, tant qu’il n’entre pas en conflit avec un autre élément de ce manifeste et qu’il n’est pas déjà utilisé par une autre application en cours d’exécution sur l’ordinateur/la machine virtuelle. <br> **Placement Properties** : il s’agit des propriétés pour ce type de nœud que vous avez utilisées comme contraintes de positionnement pour les services système ou pour vos services. Ces propriétés sont des paires clé/valeur définies par l’utilisateur qui fournissent des métadonnées supplémentaires pour un nœud donné. La présence d’un disque dur ou d’une carte graphique sur le nœud, le nombre de rotations du disque dur, les noyaux et d’autres propriétés physiques sont des exemples de propriétés du nœud. <br> **Capacities** : les capacités du nœud définissent le nom et la quantité d’une ressource spécifique disponible sur un nœud particulier pour consommation. Par exemple, un nœud peut définir qu’il possède la capacité pour une mesure appelée « MemoryInMb » et qu’il dispose de 2 048 Mo de mémoire disponible par défaut. Ces capacités sont utilisées au moment de l’exécution pour garantir que les services qui nécessitent une quantité spécifique de ressources sont placés sur des nœuds où ces ressources sont disponibles dans la quantité requise.<br>**IsPrimary** : si plusieurs NodeType sont définis, vérifiez qu’un seul est défini comme primaire avec la valeur *true*; c’est là que les services système s’exécutent. Tous les autres types de nœuds doivent avoir la valeur *false* |
| **Nœuds** |Il s’agit des détails de chacun des nœuds qui font partie du cluster (type de nœud, nom de nœud, adresse IP, domaine d’erreur et domaine de mise à niveau du nœud). Les ordinateurs sur lesquels vous souhaitez créer le cluster doivent être répertoriés ici avec leur adresse IP. <br> Si vous utilisez la même adresse IP pour tous les nœuds, un cluster à boîtier unique est créé. Vous pouvez l’utiliser à des fins de test. N’utilisez pas les clusters à boîtier unique pour le déploiement de charges de travail de production. |

### <a name="step-2-run-the-testconfiguration-script"></a>Étape 2 : Exécuter le script TestConfiguration
Le script TestConfiguration teste votre infrastructure telle que définie dans cluster.json pour vérifier que les autorisations nécessaires sont affectées, que les ordinateurs sont connectés entre eux et que d’autres attributs sont définis afin que le déploiement puisse s’effectuer correctement. Il s’agit essentiellement d’un outil d’analyse des meilleures pratiques. Nous continuerons à ajouter des validations à cet outil au fil du temps pour le rendre plus efficace.

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

### <a name="step-3-run-the-create-cluster-script"></a>Étape 3 : Exécuter le script de création de cluster
Après avoir modifié la configuration de cluster dans le document JSON et ajouté toutes les informations de nœud, exécutez le script PowerShell de création de cluster *CreateServiceFabricCluster.ps1* à partir du dossier de package et transmettez-lui le chemin du fichier de configuration JSON. Une fois ces opérations terminées, acceptez le CLUF.

Ce script peut être exécuté sur n’importe quel ordinateur disposant d’un accès administrateur à tous les ordinateurs qui sont répertoriés en tant que nœuds dans le fichier de configuration du cluster. L’ordinateur sur lequel ce script est exécuté ne doit pas nécessairement faire partie du cluster.

```
#Create an unsecured local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```
```
#Create an unsecured multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

> [!NOTE]
> Les journaux de déploiement sont disponibles localement sur la machine virtuelle/l’ordinateur sur laquelle/lequel vous avez exécuté le script PowerShell CreateServiceFabricCluster. Ils se trouvent dans un sous-dossier nommé DeploymentTraces sous le dossier où vous avez exécuté la commande PowerShell. Pour voir si Service Fabric a été déployé correctement sur un ordinateur, vous pouvez rechercher les fichiers installés dans le répertoire C:\ProgramData et vérifier si les processus FabricHost.exe et Fabric.exe sont mentionnés comme étant en cours d’exécution dans le Gestionnaire des tâches.
> 
> 

### <a name="step-4-connect-to-the-cluster"></a>Étape 4 : Se connecter au cluster
Pour vous connecter à un cluster sécurisé, consultez [Service fabric connect to secure cluster](service-fabric-connect-to-secure-cluster.md) (Connexion à un cluster sécurisé avec Service Fabric).

Exécutez la commande PowerShell suivante pour vous connecter à un cluster non sécurisé :

```powershell

Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>

Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000

```
### <a name="step-5-bring-up-service-fabric-explorer"></a>Étape 5 : Afficher Service Fabric Explorer
Désormais, vous pouvez vous connecter au cluster avec Service Fabric Explorer directement à partir de l’un des ordinateurs avec http://localhost:19080/Explorer/index.html ou à distance avec http://<*adresse_IP_ordinateur*>:19080/Explorer/index.html.

## <a name="add-and-remove-nodes"></a>Ajouter et supprimer des nœuds
Vous pouvez ajouter des nœuds à votre cluster Service Fabric autonome ou en supprimer en fonction de l’évolution de vos besoins. Consultez l’article [Ajouter ou supprimer des nœuds à/d’un cluster Service Fabric autonome](service-fabric-cluster-windows-server-add-remove-nodes.md) pour obtenir des instructions détaillées.

## <a name="remove-a-cluster"></a>Supprimer un cluster
Pour supprimer un cluster, exécutez le script PowerShell *RemoveServiceFabricCluster.ps1* à partir du dossier de package et transmettez le chemin du fichier de configuration JSON. Vous pouvez éventuellement spécifier un emplacement pour le journal de la suppression.

Ce script peut être exécuté sur n’importe quel ordinateur disposant d’un accès administrateur à tous les ordinateurs qui sont répertoriés en tant que nœuds dans le fichier de configuration du cluster. L’ordinateur sur lequel ce script est exécuté ne doit pas nécessairement faire partie du cluster.

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   
```


<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Données de télémétrie recueillies et comment se désabonner
Par défaut, le produit collecte les données de télémétrie sur l’utilisation de Service Fabric en vue de l’améliorer. L’outil d’analyse des meilleures pratiques qui s’exécute dans le cadre de l’installation contrôle la connectivité à [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1). Si le service n’est pas joignable, la configuration échoue, sauf si vous vous désabonnez de la télémétrie.

1. Le pipeline de télémétrie essaye de charger les données suivantes sur [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) une fois par jour. Ce meilleur téléchargement n’a aucun impact sur la fonctionnalité de cluster. Les données de télémétrie sont envoyées uniquement à partir du nœud qui exécute le service Failover Manager principal. Aucun autre nœud n’envoie des données de télémétrie.
2. La télémétrie se compose des éléments suivants :

* Nombre de services
* Nombre de ServiceTypes
* Nombre de Applications
* Nombre de ApplicationUpgrades
* Nombre de FailoverUnits
* Nombre de InBuildFailoverUnits
* Nombre de UnhealthyFailoverUnits
* Nombre de Replicas
* Nombre de InBuildReplicas
* Nombre de StandByReplicas
* Nombre de OfflineReplicas
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* Nombre de nœuds
* IsContextComplete : True/False
* ClusterId : GUID généré de manière aléatoire pour chaque cluster
* ServiceFabricVersion
* Adresse IP de la machine virtuelle ou de l’ordinateur à partir de laquelle/duquel les données de télémétrie sont téléchargées

Pour désactiver les données de télémétrie, ajoutez ce qui suit à *properties* dans votre configuration de cluster : *enableTelemetry: false*.

<a id="previewfeatures"></a>

## <a name="preview-features-included-in-this-package"></a>Fonctionnalités préliminaires incluses dans ce package
Aucune.


> [!NOTE]
> À partir de la nouvelle [version mise à la disposition générale du cluster autonome pour Windows Server (version 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), vous pouvez mettre à niveau votre cluster vers des versions ultérieures, manuellement ou automatiquement. Reportez-vous au document [Mettre à niveau une version autonome du cluster Service Fabric](service-fabric-cluster-upgrade-windows-server.md) pour plus d’informations.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* [Paramètres de configuration pour un cluster Windows autonome](service-fabric-cluster-manifest.md)
* [Ajouter ou supprimer des nœuds à/d’un cluster Service Fabric autonome](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Mettre à niveau une version autonome du cluster Service Fabric](service-fabric-cluster-upgrade-windows-server.md)
* [Créer un cluster Service Fabric autonome avec des machines virtuelles Azure Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Sécuriser un cluster autonome sur Windows à l’aide de la sécurité Windows](service-fabric-windows-cluster-windows-security.md)
* [Sécuriser un cluster autonome sur Windows à l’aide de certificats X509](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png



<!--HONumber=Dec16_HO2-->


