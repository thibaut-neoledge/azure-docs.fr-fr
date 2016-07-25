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
   ms.date="07/05/2016"
   ms.author="chackdan"/>


# Créer un cluster exécuté sur Windows Server

Azure Service Fabric permet la création de clusters Service Fabric sur toute machine virtuelle ou tout ordinateur exécutant Windows Server. Cela signifie que vous pouvez déployer et exécuter des applications Service Fabric dans n’importe quel environnement, que ce soit en local ou avec un fournisseur cloud, où vous avez un ensemble d’ordinateurs Windows Server interconnectés. Service Fabric fournit un package d’installation pour créer des clusters Service Fabric, appelé package Windows Server autonome.

Cet article vous guide tout au long des étapes de création locale d’un cluster à l’aide d’un package autonome Service Fabric, même s’il peut être facilement adapté à tout autre environnement, notamment d’autres fournisseurs de cloud.

>[AZURE.NOTE] Ce package Windows Server autonome est actuellement en préversion et n’est pas pris en charge pour les charges de production. [Cliquez ici](http://go.microsoft.com/fwlink/?LinkID=733084) si vous souhaitez télécharger une copie du CLUF (Contrat de Licence Utilisateur Final).

<a id="downloadpackage"></a>
## Télécharger le package autonome Service Fabric


[Téléchargez le package autonome Service Fabric pour Windows Server 2012 R2 et ultérieur](http://go.microsoft.com/fwlink/?LinkId=730690), nommé *Microsoft.Azure.ServiceFabric.WindowsServer.&lt;version&gt;.zip*.

Vous trouverez les fichiers suivants dans le package de téléchargement :

|**Nom de fichier**|**Brève description**|
|-----------------------|--------------------------|
|MicrosoftAzureServiceFabric.cab|Fichier CAB qui contient les fichiers binaires déployés sur chaque ordinateur du cluster.|
|ClusterConfig.Unsecure.DevCluster.json|Exemple de fichier de configuration de cluster qui contient les paramètres pour un cluster de développement avec machine virtuelle/ordinateur unique à trois nœuds non sécurisé, notamment les informations de chaque nœud du cluster. |
|ClusterConfig.Unsecure.MultiMachine.json|Exemple de fichier de configuration de cluster qui contient les paramètres pour un cluster avec plusieurs machines virtuelles/ordinateurs non sécurisé, notamment les informations de chaque ordinateur du cluster.|
|ClusterConfig.Windows.DevCluster.json|Exemple de fichier de configuration de cluster qui contient tous les paramètres pour un cluster de développement avec machine virtuelle/ordinateur unique à trois nœuds sécurisé, notamment les informations de chaque nœud du cluster. Le cluster est sécurisé à l’aide d[’identités Windows](https://msdn.microsoft.com/library/ff649396.aspx).|
|ClusterConfig.Windows.MultiMachine.json|Exemple de fichier de configuration de cluster qui contient tous les paramètres pour un cluster avec plusieurs machines virtuelles/ordinateurs sécurisé, utilisant la sécurité Windows, notamment les informations de chaque ordinateur du cluster sécurisé. Le cluster est sécurisé à l’aide d[’identités Windows](https://msdn.microsoft.com/library/ff649396.aspx).|
|ClusterConfig.x509.DevCluster.json|Exemple de fichier de configuration de cluster qui contient tous les paramètres pour un cluster de développement avec machine virtuelle/ordinateur unique à trois nœuds sécurisé, notamment les informations de chaque nœud du cluster. Le cluster est sécurisé à l’aide de certificats x509.|
|ClusterConfig.x509.MultiMachine.json|Exemple de fichier de configuration de cluster qui contient tous les paramètres pour le cluster à plusieurs machines virtuelles/ordinateurs sécurisé, notamment les informations de chaque nœud du cluster sécurisé. Le cluster est sécurisé à l’aide de certificats x509.|
|EULA.txt|Les termes du contrat de licence pour l’utilisation du package Windows Server autonome Microsoft Azure Service Fabric. [Cliquez ici](http://go.microsoft.com/fwlink/?LinkID=733084) si vous souhaitez télécharger une copie du CLUF (Contrat de Licence Utilisateur Final).|
|Readme.txt|Lien vers les notes de publication et des instructions d’installation de base. Il s’agit d’une partie des instructions que vous trouverez dans cette page.|
|CreateServiceFabricCluster.ps1|Script PowerShell qui crée le cluster à l’aide des paramètres figurant dans le fichier ClusterConfig.json.|
|RemoveServiceFabricCluster.ps1|Script PowerShell qui supprime un cluster à l’aide des paramètres figurant dans le fichier ClusterConfig.json.|
|AddNode.ps1|Script PowerShell pour l’ajout d’un nœud à un cluster déployé existant.|
|RemoveNode.ps1|Script PowerShell pour la suppression d’un nœud d’un cluster déployé existant.|


## Planifier et préparer le déploiement de cluster
Les étapes suivantes doivent être effectuées avant la création de votre cluster.

### Étape 1 : planifier votre infrastructure de cluster
Vous allez créer un cluster Service Fabric sur les ordinateurs que vous avez pour décider des types de défaillance auxquels le cluster doit survivre. Par exemple, devez-vous séparer les lignes d’alimentation des connexions internet qui alimentent ces ordinateurs ? Vous devez en outre prendre en compte la sécurité physique de ces ordinateurs. Où l’emplacement physique se trouve-t-il et qui a besoin d’y accéder ? Une fois ces décisions prises, vous mappez logiquement les ordinateurs sur les différents domaines d’erreur (voir ci-dessous pour plus d’informations). L’infrastructure de planification pour les clusters de production est plus complexe que pour les clusters de test.

### Étape 2 : préparer les ordinateurs pour répondre aux conditions préalables
Conditions préalables pour chaque ordinateur que vous souhaitez ajouter au cluster :

- 2 Go de mémoire minimum recommandés
- Connectivité réseau Vérifiez que les ordinateurs sont sur un réseau/des réseaux sécurisé(s)
- Windows Server 2012 R2 ou Windows Server 2012 (KB2858668 doit être installé).
- .NET Framework 4.5.1 ou une version ultérieure, installation complète
- Windows PowerShell 3.0
- L’administrateur de cluster déployant et configurant le cluster doit disposer de privilèges d’administrateur sur chaque ordinateur.
- Le service RemoteRegistry doit être exécuté sur tous les ordinateurs.

### Étape 3 : déterminer la taille initiale du cluster
Le runtime Service Fabric est déployé sur chaque nœud et les nœuds sont membres du cluster. Un déploiement de production type comprend un nœud par instance du système d’exploitation (physique ou virtuel). La taille de cluster est déterminée par vos besoins métier. Toutefois, vous devez disposer d’une taille minimale de cluster de trois nœuds (ordinateurs/machines virtuelles). Notez que, à des fins de développement, vous pouvez avoir plusieurs nœuds sur un ordinateur donné. Dans un environnement de production, Service Fabric ne prend en charge qu’un seul nœud par ordinateur physique ou virtuel.

### Étape 4 : déterminer le nombre de domaines d’erreur et de domaines de mise à niveau
Un **domaine d’erreur (FD)** est une unité physique de défaillance directement liée à l’infrastructure physique dans les centres de données. Un domaine d’erreur est constitué de composants matériels (ordinateurs, commutateurs, réseau, etc.) qui partagent un point de défaillance unique. Bien qu’il n’existe aucun mappage 1:1 entre les domaines d’erreur et les racks, chaque rack peut être considéré au sens large comme un domaine d’erreur. Lorsque vous envisagez d’utiliser les nœuds de votre cluster, il est fortement recommandé de distribuer les nœuds sur au moins trois domaines d’erreur.

Quand vous spécifiez des domaines d’erreur dans *ClusterConfig.json*, vous choisissez le nom de chaque domaine d’erreur. Service Fabric prend en charge les domaines d’erreur hiérarchiques pour vous permettre d’y refléter votre topologie d’infrastructure. Par exemple, les domaines d’erreur suivants sont valides :

- "faultDomain": "fd:/Room1/Rack1/Machine1"
- "faultDomain": "fd:/FD1"
- "faultDomain": "fd:/Room1/Rack1/PDU1/M1"


Un **domaine de mise à niveau (UD)** est une unité logique de nœuds. Pendant une mise à niveau orchestrée par Service Fabric (mise à niveau de l’application ou du cluster), tous les nœuds dans un domaine de mise à niveau sont mis hors service pour effectuer la mise à niveau alors que les nœuds dans d’autres domaines de mise à niveau restent disponibles pour répondre aux requêtes. Les mises à niveau du microprogramme effectuées sur vos ordinateurs ne respecteront pas les domaines de mise à niveau. Vous devez les exécuter sur un ordinateur à la fois.

L’approche la plus simple de ces concepts consiste à considérer les domaines d’erreur comme unité de défaillance non planifiée et les domaines mise à niveau comme unité de maintenance planifiée.

Quand vous spécifiez des domaines de mise à niveau dans *ClusterConfig.json*, vous choisissez le nom de chaque domaine de mise à niveau. Par exemple, les éléments suivants sont valides :

- "upgradeDomain": "UD0"
- "upgradeDomain": "UD1A"
- "upgradeDomain": "DomainRed"
- "upgradeDomain": "Blue"

Pour plus d’informations sur les domaines de mise à niveau et les domaines d’erreur, consultez l’article [Description d’un cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

### Étape 5 : télécharger le package autonome Service Fabric pour Windows Server
[Téléchargez le package autonome Service Fabric pour Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) et décompressez le package sur un ordinateur de déploiement qui ne fait pas partie du cluster ou sur l’un des ordinateurs qui fera partie de votre cluster.

<a id="createcluster"></a>
## Création de votre cluster

Une fois les étapes décrites dans la section Planification et préparation ci-dessus effectuées, vous êtes prêt à créer votre cluster.

### Étape 1 : modifier la configuration du cluster
Le cluster est décrit dans un fichier *ClusterConfig.json*. Pour plus d’informations sur les sections de ce fichier, consultez l’article [Paramètres de configuration pour un cluster Windows autonome](service-fabric-cluster-manifest.md). Ouvrez l’un des fichiers *ClusterConfig.json* à partir du package que vous avez téléchargé et modifiez les paramètres suivants :

|**Paramètres de configuration**|**Description**|
|-----------------------|--------------------------|
|**NodeTypes**|Les types de nœuds permettent de séparer vos nœuds de cluster en différents groupes. Un cluster doit comprendre au moins un NodeType. Tous les nœuds d’un groupe ont les caractéristiques communes suivantes. <br> **Name** : il s’agit du nom de type de nœud. <br> **Endpoints Ports** : il s’agit de différents points de terminaison (ports) nommés qui sont associés à ce type de nœud. Vous pouvez utiliser n’importe quel numéro de port, tant qu’il n’entre en conflit avec aucun autre élément dans ce manifeste et n’est pas déjà en cours d’utilisation par d’autres applications exécutées sur les ordinateurs/machines virtuelles <br> **PlacementProperties** : elles décrivent les propriétés pour ce type de nœud que vous pourrez ensuite utiliser comme contraintes de placement pour les services système ou vos services. Ces propriétés sont des paires clé/valeur définies par l'utilisateur qui fournissent des métadonnées supplémentaires pour un nœud donné. La présence d’un disque dur ou d’une carte graphique sur le nœud, le nombre de rotations du disque dur, les noyaux et d’autres propriétés physiques sont des exemples de propriétés du nœud. <br> **Capacities** : les capacités du nœud définissent le nom et la quantité d’une ressource spécifique disponible sur un nœud particulier pour consommation. Par exemple, un nœud peut définir qu’il possède la capacité pour une mesure appelée « MemoryInMb » et qu’il dispose de 2 048 Mo de mémoire disponible par défaut. Ces capacités sont utilisées au moment de l’exécution pour garantir que les services qui nécessitent une quantité spécifique de ressources sont placés sur des nœuds où ces ressources sont disponibles.<br>**IsPrimary** : si plusieurs NodeType sont définis, vérifiez qu’un seul est défini comme primaire avec la valeur *true* ; c’est là que les services système s’exécutent. Tous les autres types de nœuds doivent avoir la valeur *false*.|
|**Nœuds**|Détails de chacun des nœuds qui font partie du cluster (type de nœud, nom de nœud, adresse IP, domaine d’erreur et domaine de mise à niveau du nœud). Les ordinateurs sur lesquels vous souhaitez créer le cluster doivent être répertoriés ici avec leur adresse IP. <br> Si vous utilisez les mêmes adresses IP pour tous les nœuds, un cluster à boîtier unique est créé. Vous pouvez l’utiliser à des fins de test. Les clusters à boîtier unique ne doivent pas servir au déploiement de charges de travail de production.|

### Étape 2 : exécuter le script de création de cluster
Une fois que vous avez modifié la configuration de cluster dans le document JSON et ajouté toutes les informations de nœud, exécutez le script PowerShell de création de cluster *CreateServiceFabricCluster.ps1* à partir du dossier de package et transmettez-lui le chemin du fichier de configuration JSON et l’emplacement du fichier CAB de package.

Ce script peut être exécuté sur n’importe quel ordinateur disposant d’un accès administrateur à tous les ordinateurs qui sont répertoriés en tant que nœuds dans le fichier de configuration du cluster. L’ordinateur sur lequel ce script est exécuté peut faire partie du cluster ou non.

```
#Create an unsecure local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true
```
```
#Create an unsecure multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true
```

>[AZURE.NOTE] Les journaux de déploiement sont disponibles localement sur une machine virtuelle/un ordinateur sur lequel vous avez exécuté le script PowerShell CreateServiceFabricCluster. Vous les trouverez dans un sous-dossier nommé « DeploymentTraces » dans le dossier où vous avez exécuté la commande Powershell. En outre, pour voir si Service Fabric a été déployé correctement sur un ordinateur, vous pouvez rechercher les fichiers installés dans le répertoire C:\\ProgramData et vérifier si les processus FabricHost.exe et Fabric.exe sont mentionnés comme étant en cours d’exécution dans le Gestionnaire des tâches.

### Étape 3 : se connecter au cluster
Désormais, vous pouvez vous connecter au cluster avec Service Fabric Explorer directement à partir de l’un des ordinateurs avec http://localhost:19080/Explorer/index.html ou à distance avec http://<*adresse\_IP\_ordinateur*>:19080/Explorer/index.html

## Ajouter des nœuds à votre cluster

1. Préparez la machine virtuelle ou l’ordinateur que vous voulez ajouter à votre cluster (reportez-vous à l’étape 2 de la section Planifier et préparer le déploiement de cluster ci-dessus).
2. Prévoyez le domaine d’erreur et le domaine de mise à niveau auquel vous allez ajouter cette machine virtuelle ou cet ordinateur.
3. Copiez ou [téléchargez le package autonome Service Fabric pour Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) et décompressez le package sur la machine virtuelle ou l’ordinateur que vous souhaitez ajouter au cluster.
4. Ouvrez une invite PowerShell comme administrateur, puis naviguez jusqu’à l’emplacement du package décompressé.
5. Exécutez le script Powershell *AddNode.ps1* avec les paramètres qui décrivent le nouveau nœud à ajouter. L’exemple ci-dessous ajoute un nouveau nœud nommé VM5, de type NodeType0, avec l’adresse IP 182.17.34.52, à UD1 et FD1. *ExistingClusterConnectionEndPoint* est un point de terminaison de connexion pour un nœud déjà présent dans le cluster existant. Vous pouvez choisir *n’importe quelle* adresse IP de nœud dans le cluster.

```
.\AddNode.ps1 -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClusterConnectionEndPoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain FD1
```

## Supprimer des nœuds de votre cluster

1. Avec Bureau à distance (RDP), accédez à la machine virtuelle ou à l’ordinateur que vous souhaitez supprimer du cluster.
2. Copiez ou [téléchargez le package autonome Service Fabric pour Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) et décompressez le package sur la machine virtuelle ou l’ordinateur que vous souhaitez ajouter au cluster.
3. Ouvrez une invite PowerShell comme administrateur, puis naviguez jusqu’à l’emplacement du package décompressé.
4. Exécutez le script Powershell *RemoveNode.ps1*. L’exemple ci-dessous supprime le nœud actif du cluster. *ExistingClusterConnectionEndPoint* est un point de terminaison de connexion pour un nœud déjà présent dans le cluster existant. Vous pouvez choisir *n’importe quelle* adresse IP de nœud dans le cluster.

```
.\RemoveNode.ps1 -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -ExistingClusterConnectionEndPoint 182.17.34.50:19000
```

## Supprimer votre cluster
Pour supprimer un cluster, exécutez le script Powershell *RemoveServiceFabricCluster.ps1* à partir du dossier de package et transmettez le chemin au fichier de configuration JSON et l’emplacement du fichier CAB de package.

Ce script peut être exécuté sur n’importe quel ordinateur disposant d’un accès administrateur à tous les ordinateurs qui sont répertoriés en tant que nœuds dans le fichier de configuration du cluster. L’ordinateur sur lequel ce script est exécuté peut faire partie du cluster ou non.

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
```

## Procédure : Création d’un cluster à trois nœuds avec des machines virtuelles IaaS Azure
Les étapes suivantes décrivent comment créer un cluster sur des machines virtuelles IaaS Azure à l’aide du programme d’installation de Windows Server autonome. Notez que le runtime Service Fabric sur ce cluster IaaS est entièrement géré par vous-même, contrairement aux clusters créés par le biais du portail Azure (qui sont mis à niveau par le fournisseur de ressources Service Fabric).

1. Connectez-vous au portail Azure et créez une machine virtuelle Windows Server 2012 R2 Datacenter dans un groupe de ressources.
2. Ajoutez deux autres machines virtuelles Windows Server 2012 R2 Datacenter au même groupe de ressources. Vérifiez que chaque machine virtuelle a le même nom d’utilisateur d’administration et le même mot de passe lors de la création. Une fois créées, vous devriez voir les trois machines virtuelles sur le même réseau virtuel.
3. Connectez-vous à chacune des machines virtuelles et désactivez le Pare-feu Windows à l’aide du tableau de bord Gestionnaire de serveur, Serveur Local. Cela permet de s’assurer que le trafic réseau peut communiquer entre les machines. Sur chaque machine, obtenez l’adresse IP en ouvrant une invite de commandes et en tapant *ipconfig*. Vous pouvez également voir l’adresse IP de chaque machine en sélectionnant la ressource de réseau virtuel du groupe de ressources dans le portail Azure.
4. Connectez-vous à l’une des machines et vérifiez que vous pouvez communiquer avec les deux autres à l’aide d’une commande ping.
5. Connectez-vous à l’une des machines virtuelles et téléchargez le package Windows Server autonome dans un nouveau dossier sur la machine, puis décompressez le package.
6. Ouvrez le fichier *ClusterConfig.Unsecure.MultiMachine.json* dans le Bloc-notes et modifiez chaque nœud avec les trois adresses IP des machines virtuelles, modifiez le nom du cluster en haut, puis enregistrez le fichier. Un exemple partiel du manifeste de cluster est illustré ci-dessous, montrant les adresses IP de chaque machine.

    ```
    {
        "name": "TestCluster",
        "clusterManifestVersion": "1.0.0",
        "apiVersion": "2015-01-01-alpha",
        "nodes": [
        {
            "nodeName": "vm0",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "vm1",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "vm2",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
    ],
    ```

7. Ouvrez une fenêtre Powershell ISE et accédez au dossier où vous avez téléchargé et décompressé le package du programme d’installation autonome et enregistré le fichier manifeste ci-dessus. Exécutez la commande PowerShell suivante.

    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
    ```

8. Le script Powershell doit s’exécuter, se connecter à chaque machine et créer un cluster. Après environ une minute, vous pouvez vérifier que le cluster est opérationnel en vous connectant à Service Fabric Explorer sur l’une des adresses IP de machine, par exemple http://10.7.0.5:19080/Explorer/index.html. S’agissant d’un cluster autonome sur des machines virtuelles IaaS, si vous souhaitez le sécuriser, vous devez déployer des certificats sur les machines virtuelles ou configurer l’une d’elles comme contrôleur Windows Server Active Directory (AD) pour l’authentification Windows, comme vous le feriez localement. Pour configurer des clusters sécurisés, consultez Étapes suivantes.

## Étapes suivantes
- [Créer des clusters Service Fabric autonomes sur un serveur Windows Server ou Linux](service-fabric-deploy-anywhere.md)
- [Paramètres de configuration pour un cluster Windows autonome](service-fabric-cluster-manifest.md)

- [Sécuriser un cluster autonome sur Windows à l’aide de la sécurité Windows](service-fabric-windows-cluster-windows-security.md)
- [Sécuriser un cluster autonome sur Windows à l’aide de certificats X509](service-fabric-windows-cluster-x509-security.md)

<!---HONumber=AcomDC_0713_2016-->