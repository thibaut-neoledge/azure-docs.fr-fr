<properties
   pageTitle="Création d’un cluster Azure Service Fabric local ou sur cloud | Microsoft Azure"
   description="Apprenez à créer un cluster Azure Service Fabric sur n’importe quel ordinateur (physique ou virtuel) exécutant Windows Server, qu’il soit local ou dans un cloud."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/14/2016"
   ms.author="chackdan"/>


# Création d’un cluster Azure Service Fabric en local ou dans le cloud

Azure Service Fabric permet la création de clusters Service Fabric sur toute machine virtuelle ou tout ordinateur exécutant Windows Server. Cela signifie que vous pouvez déployer et exécuter des applications Service Fabric dans n’importe quel environnement, que ce soit en local ou avec un fournisseur cloud, où vous avez un ensemble d’ordinateurs Windows Server interconnectés. Service Fabric fournit un package d’installation pour créer de tels clusters Service Fabric.

Cet article vous guide tout au long des étapes de création d’un cluster à l’aide d’un package autonome Service Fabric en local, même s’il peut être facilement adapté à tout autre environnement, notamment d’autres clouds.

>[AZURE.NOTE] Cette offre autonome est actuellement en version préliminaire. [Cliquez ici](http://go.microsoft.com/fwlink/?LinkID=733084) si vous souhaitez télécharger une copie du CLUF (Contrat de Licence Utilisateur Final).

<a id="downloadpackage"></a>
## Télécharger le package autonome Service Fabric


[Téléchargez le package autonome Service Fabric pour Windows Server 2012 R2](http://go.microsoft.com/fwlink/?LinkId=730690), intitulé *Microsoft.Azure.ServiceFabric.WindowsServer.&lt;version&gt;.zip*.

Vous trouverez les fichiers suivants dans le package de téléchargement :

|**Nom de fichier**|**Brève description**|
|-----------------------|--------------------------|
|MicrosoftAzureServiceFabric.cab|Le fichier CAB qui contient les fichiers binaires qui seront déployés sur chaque ordinateur du cluster.|
|ClusterConfig.JSON|Fichier de configuration de cluster qui contient tous les paramètres pour le cluster, notamment les informations de chaque ordinateur faisant partie du cluster.|
|EULA.txt|Les termes du contrat de licence pour l’utilisation du package autonome Microsoft Azure Service Fabric. [Cliquez ici](http://go.microsoft.com/fwlink/?LinkID=733084) si vous souhaitez télécharger une copie du CLUF (Contrat de Licence Utilisateur Final).|
|Readme.txt|Lien vers les notes de publication et des instructions d’installation de base. Il s’agit d’un sous-ensemble des instructions que vous trouverez sur cette page.|
|CreateServiceFabricCluster.ps1|Script PowerShell qui crée le cluster à l’aide des paramètres figurant dans le fichier ClusterConfig.JSON.|
|RemoveServiceFabricCluster.ps1|Script PowerShell pour la suppression d’un cluster à l’aide des paramètres figurant dans le fichier ClusterConfig.JSON.|

## Planification et préparation du déploiement de cluster
Les étapes suivantes doivent être effectuées avant la création de votre cluster.

### Étape 1 : planifier votre infrastructure de cluster
Vous allez créer un cluster Service Fabric sur les ordinateurs que vous possédez, afin de décider des types de défaillance auxquels le cluster doit survivre. Par exemple, devez-vous séparer les lignes d’alimentation des connexions internet qui alimentent ces ordinateurs ? Vous devez en outre prendre en compte la sécurité physique de ces ordinateurs. Où l’emplacement physique se trouve-t-il et qui a besoin d’y accéder ? Une fois ces décisions prises, vous mappez logiquement les ordinateurs sur les différents domaines d’erreur (voir ci-dessous pour plus d’informations). L’infrastructure de planification pour les clusters de production est beaucoup plus complexe que pour les clusters de test.

### Étape 2 : préparer les ordinateurs pour répondre aux conditions préalables
Conditions préalables pour chaque ordinateur que vous souhaitez ajouter au cluster :

- 2 Go de mémoire minimum recommandés
- Connectivité réseau : assurez-vous que les ordinateurs sont sur un réseau/des réseaux sécurisé(s)
- Windows Server 2012 R2 ou Windows Server 2012 (KB2858668 doit être installé).
- .NET Framework 4.5.1 ou une version ultérieure, installation complète
- Windows PowerShell 3.0
- L’administrateur de cluster déployant et configurant le cluster doit disposer de privilèges d’administrateur sur chaque ordinateur.
- Le service RemoteRegistry doit être exécuté sur tous les ordinateurs.

### Étape 3 : déterminer la taille initiale du cluster
Chaque nœud est composé d’une pile Service Fabric complète et est un membre individuel du cluster Service Fabric. Un déploiement type de Service Fabric comprend un nœud par instance du système d’exploitation (physique ou virtuel). La taille de cluster est déterminée par vos besoins métier. Toutefois, vous devez disposer d’une taille minimale de cluster de trois nœuds (ordinateurs/machines virtuelles). Notez que, à des fins de développement, vous pouvez avoir plusieurs nœuds sur un ordinateur donné. Dans un environnement de production, Service Fabric ne prend en charge qu’un seul nœud par ordinateur physique ou virtuel.

### Étape 4 : déterminer le nombre de domaines d’erreur et de domaines de mise à niveau
Un **domaine d’erreur (FD)** est une unité physique de défaillance et est directement lié à l’infrastructure physique dans les centres de données. Un domaine d’erreur est constitué de composants matériels (ordinateurs, commutateurs, etc.) qui partagent un point de défaillance unique. Bien qu’il n’existe aucun mappage 1:1 entre les domaines d’erreur et les racks, chaque rack peut être considéré au sens large comme un domaine d’erreur. Lorsque vous envisagez d’utiliser les nœuds de votre cluster, il est fortement recommandé de distribuer les nœuds sur au moins trois domaines d’erreur.

Lorsque vous spécifiez des domaines d’erreur dans *ClusterConfig.JSON*, vous choisissez le nom du domaine d’erreur. Service Fabric prend en charge les domaines d’erreur hiérarchiques pour vous permettre d’y refléter votre topologie d’infrastructure. Par exemple, les éléments suivants sont autorisés :

- "faultDomain": "fd:/Room1/Rack1/Machine1"
- "faultDomain": "fd:/FD1"
- "faultDomain": "fd:/Room1/Rack1/PDU1/M1"


Un **domaine de mise à niveau** est une unité logique de nœuds. Pendant une mise à niveau orchestrée par Service Fabric (mise à niveau de l’application ou du cluster), tous les nœuds dans un domaine de mise à niveau sont mis hors service pour effectuer la mise à niveau alors que les nœuds dans d’autres domaines de mise à niveau restent disponibles pour répondre aux requêtes. Les mises à niveau du microprogramme effectuées sur vos ordinateurs ne respecteront pas les domaines de mise à niveau. Vous devez les exécuter sur un ordinateur à la fois.

L’approche la plus simple de ces concepts consiste à considérer les domaines d’erreur comme unité de défaillance non planifiée et les domaines mise à niveau comme unité de maintenance planifiée.

Lorsque vous spécifiez des domaines de mise à niveau dans *ClusterConfig.JSON*, vous choisissez le nom du domaine de mise à niveau. Par exemple, tous les éléments suivants sont autorisés :

- "upgradeDomain": "UD0"
- "upgradeDomain": "UD1A"
- "upgradeDomain": "DomainRed"
- "upgradeDomain": "Blue"

### Étape 5 : télécharger le package autonome Service Fabric pour Windows Server
[Téléchargez le package autonome Service Fabric pour Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) et décompressez le package sur un ordinateur de déploiement qui ne fait pas partie du cluster ou sur l’un des ordinateurs qui fera partie de votre cluster.

<a id="createcluster"></a>
## Création de votre cluster

Une fois les étapes décrites dans la section Planification et préparation ci-dessus effectuées, vous êtes prêt à créer votre cluster.

### Étape 1 : modifier la configuration du cluster
Ouvrez *ClusterConfig.JSON* à partir du package que vous avez téléchargé. Vous pouvez utiliser n’importe quel éditeur de votre choix pour modifier les paramètres suivants :

|**Paramètres de configuration**|**Description**|
|-----------------------|--------------------------|
|NodeTypes|Les types de nœuds permettent de séparer vos nœuds de cluster en différents groupes. Un cluster doit comprendre au moins un NodeType. Tous les nœuds d’un groupe ont les caractéristiques communes suivantes. <br> *Name* : il s’agit du nom du type de nœud. <br>*EndPoints* : il s’agit de différents points de terminaison (ports) nommés qui sont associés à ce type de nœud. Vous pouvez utiliser n’importe quel numéro de port, tant qu’il n’entre en conflit avec aucun autre élément dans ce manifeste et n’est pas déjà en cours d’utilisation par d’autres programmes sur les ordinateurs/machines virtuelles <br> *PlacementProperties* : elles décrivent les propriétés pour ce type de nœud que vous utiliserez ensuite comme contraintes de placement pour les services système ou vos services. Ces propriétés sont des paires clé/valeur définies par l'utilisateur qui fournissent des métadonnées supplémentaires pour un nœud donné. La présence d’un disque dur ou d’une carte graphique sur le nœud, le nombre de rotations du disque dur, les noyaux et d’autres propriétés physiques sont des exemples de propriétés du nœud. <br> *Capacities* - les capacités du nœud définissent le nom et la quantité d’une ressource spécifique disponible sur un nœud particulier pour consommation. Par exemple, un nœud peut définir qu’il possède la capacité pour une mesure appelée « MemoryInMb » et qu’il dispose de 2 048 Mo de mémoire disponible par défaut. Ces capacités permettent de garantir que les services qui nécessitent une quantité spécifique de ressources sont placés sur des nœuds dans lesquels ces ressources restent disponibles au cours de l’exécution.|
|Nœuds|Les détails de chacun des nœuds qui feront partie du cluster (type de nœud, nom de nœud, adresse IP, domaine d’erreur et domaine de mise à niveau du nœud). Les ordinateurs sur lesquels vous souhaitez créer le cluster doivent être répertoriés ici avec leur adresse IP. <br> Si vous utilisez les mêmes adresses IP pour tous les nœuds, un cluster à boîtier unique est créé. Vous pouvez l’utiliser à des fins de test. Les clusters à boîtier unique ne doivent pas servir au déploiement de charges de travail de production.|

### Étape 2 : exécuter le script de création de cluster
Une fois que vous avez modifié la configuration de cluster dans le document JSON et ajouté toutes les informations de nœud, exécutez le script PowerShell de création de cluster à partir du dossier de package et transmettez-lui le chemin d’accès au fichier de configuration et l’emplacement de la racine du package.

Ce script peut être exécuté sur n’importe quel ordinateur disposant d’un accès administrateur à tous les ordinateurs qui sont répertoriés en tant que nœuds dans le fichier de configuration du cluster. L’ordinateur sur lequel ce script est exécuté peut faire partie du cluster ou non.

```
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath C:\Microsoft.Azure.ServiceFabric.WindowsServer.5.0.135.9590\ClusterConfig.JSON -MicrosoftServiceFabricCabFilePath C:\Microsoft.Azure.ServiceFabric.WindowsServer.5.0.135.9590\MicrosoftAzureServiceFabric.cab
```

## Étapes suivantes

Après avoir créé un cluster, veillez également à le sécuriser :
- [Sécurité des clusters](service-fabric-cluster-security.md)

Lisez les informations suivantes pour démarrer le développement et de déploiement d’applications :
- [Kit de développement logiciel (SDK) de Service Fabric et prise en main](service-fabric-get-started.md)
- [Gestion de vos applications Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md).

En savoir plus sur les clusters Azure et les clusters autonomes :
- [Vue d’ensemble de la création de clusters autonomes et comparaison avec les clusters gérés par Azure](service-fabric-deploy-anywhere.md)

<!---HONumber=AcomDC_0615_2016-->