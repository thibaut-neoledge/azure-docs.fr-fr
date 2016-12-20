---
title: Configuration de votre cluster autonome | Microsoft Docs
description: "Cet article explique comment configurer votre cluster Service Fabric autonome ou privé."
services: service-fabric
documentationcenter: .net
author: dsk-2015
manager: timlt
editor: 
ms.assetid: 0c5ec720-8f70-40bd-9f86-cd07b84a219d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2016
ms.author: dkshir
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b19d1ed96cf8e294cb105fa62f8623b28e3cc7fc


---
# <a name="configuration-settings-for-standalone-windows-cluster"></a>Paramètres de configuration pour un cluster Windows autonome
Cet article explique comment configurer un cluster Service Fabric autonome à l’aide du fichier ***ClusterConfig.JSON***. Vous pouvez utiliser ce fichier pour spécifier des informations telles que les nœuds Service Fabric et leurs adresses IP, différents types de nœuds sur le cluster, les configurations de sécurité, ainsi que la topologie du réseau en termes de domaines d’erreur et de mise à niveau, pour votre cluster autonome.

Lorsque vous [téléchargez le package Service Fabric autonome](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), quelques exemples de fichier ClusterConfig.JSON sont téléchargés sur votre ordinateur de travail. Les exemples comprenant *DevCluster* dans leurs noms vous permettent de créer un cluster avec les trois nœuds sur le même ordinateur, comme des nœuds logiques. Parmi ces nœuds, au moins un doit être marqué comme nœud principal. Ce cluster est utile pour un environnement de test ou de développement et n’est pas pris en charge comme cluster de production. Les exemples comprenant *MultiMachine* dans leurs noms vous permettent de créer un cluster de niveau de production, avec chaque nœud sur un ordinateur distinct. Le nombre de nœuds principaux pour ce cluster dépend du [niveau de fiabilité](#reliability).

1. *ClusterConfig.Unsecure.DevCluster.JSON* et *ClusterConfig.Unsecure.MultiMachine.JSON* montrent comment créer un cluster de test ou de production non sécurisé respectivement. 
2. *ClusterConfig.Windows.DevCluster.JSON* et *ClusterConfig.Windows.MultiMachine.JSON* montrent comment créer un cluster de test ou de production sécurisé à l’aide de la [sécurité Windows](service-fabric-windows-cluster-windows-security.md).
3. *ClusterConfig.X509.DevCluster.JSON* et *ClusterConfig.X509.MultiMachine.JSON* montrent comment créer un cluster de test ou de production sécurisé à l’aide de la [sécurité basée sur un certificat X509](service-fabric-windows-cluster-x509-security.md). 

Nous allons maintenant examiner les différentes sections d’un fichier ***ClusterConfig.JSON*** comme indiqué ci-dessous.

## <a name="general-cluster-configurations"></a>Configurations de cluster générales
Cette section couvre les configurations spécifiques à de larges clusters, comme indiqué dans l’extrait de code JSON ci-dessous.

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2015-01-01-alpha",

Vous pouvez attribuer un nom convivial à votre cluster Service Fabric en lui assignant la variable **name** . **clusterConfigurationVersion** est le numéro de version de votre cluster, que vous devez augmenter chaque fois que vous mettez à niveau votre cluster Service Fabric. Il est cependant recommandé de conserver la valeur par défaut attribuée à **apiVersion** .

<a id="clusternodes"></a>

## <a name="nodes-on-the-cluster"></a>Nœuds sur le cluster
Vous pouvez configurer les nœuds de votre cluster Service Fabric à l’aide de la section **nœuds** , comme le montre l’extrait de code suivant.

    "nodes": [{
        "nodeName": "vm0",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType1",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType2",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],

Un cluster Service Fabric doit contenir au moins 3 nœuds. Vous pouvez ajouter d’autres nœuds à cette section, selon votre configuration. Le tableau suivant décrit les paramètres de configuration de chaque nœud.

| **Configuration de nœuds** | **Description** |
| --- | --- |
| nodeName |Vous pouvez attribuer n’importe quel nom convivial au nœud. |
| iPAddress |Identifiez l’adresse IP de votre nœud en ouvrant une fenêtre de commande puis en tapant `ipconfig`. Notez l’adresse IPV4 et assignez-la à la variable **iPAddress** . |
| nodeTypeRef |Chaque nœud peut être associé à un type de nœud différent. Les [types de nœuds](#nodetypes) sont définis dans la section ci-dessous. |
| faultDomain |Les domaines d'erreur permettent aux administrateurs de cluster de définir les nœuds physiques qui sont susceptibles de rencontrer un échec en même temps en raison des dépendances physiques partagées. |
| upgradeDomain |Les domaines de mise à niveau décrivent des ensembles de nœuds qui sont arrêtés pour les mises à niveau Service Fabric, à peu près au même moment. Vous pouvez choisir les nœuds à attribuer aux domaines de mise à niveau car ils ne sont pas limités par des exigences physiques. |

## <a name="cluster-properties"></a>Propriétés du cluster
La section **properties** du fichier ClusterConfig.JSON permet de configurer le cluster comme suit.

<a id="reliability"></a>

### <a name="reliability"></a>Fiabilité
La section**reliabilityLevel** définit le nombre de copies des services système qui peuvent s’exécuter sur les nœuds principaux du cluster. Cette valeur augmente la fiabilité de ces services et, par conséquent, du cluster. Vous pouvez définir cette variable sur *Bronze*, *Silver*, *Gold* ou *Platinum* pour 3, 5, 7 ou 9 copies de ces services, respectivement. Reportez-vous à l’exemple ci-dessous.

    "reliabilityLevel": "Bronze",

Notez que dans la mesure où un nœud principal exécute une copie unique des services système, vous avez besoin d’au moins 3 nœuds principaux pour le niveau de fiabilité *Bronze*, 5 pour *Silver*, 7 pour *Gold* et 9 pour *Platinum*.

### <a name="diagnostics"></a>Diagnostics
La section **diagnosticsStore** vous permet de configurer des paramètres pour activer les diagnostics et corriger les défaillances de nœud ou du cluster, comme illustré dans l’extrait de code suivant. 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

La section **metadata** est une description du diagnostic de votre cluster et peut être définie selon votre installation. Ces variables vous aident à collecter les journaux de suivi ETW, les vidages sur incident ainsi que les compteurs de performance. Consultez les sections [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) (Journal de suivi) et [ETW Tracing](https://msdn.microsoft.com/library/ms751538.aspx) pour plus d’informations sur les journaux de suivi ETW. Tous les journaux, notamment les [vidages sur incident](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) et les [compteurs de performance](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) peuvent être dirigés vers le dossier **connectionString** sur votre ordinateur. Vous pouvez également utiliser *AzureStorage* pour le stockage des diagnostics. Voici un exemple d’extrait de code.

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>Sécurité
La section **security** est nécessaire pour garantir la sécurité d’un cluster Service Fabric autonome. L’extrait de code suivant montre une partie de cette section.

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

La section **metadata** est une description de votre cluster sécurisé et peut être définie selon votre installation. Les propriétés **ClusterCredentialType** et **ServerCredentialType** déterminent le type de sécurité que le cluster et les nœuds implémenteront. Elles peuvent avoir la valeur *X509* pour une sécurité basée sur un certificat, ou *Windows* pour une sécurité basée sur Azure Active Directory. Le reste de la section **security** varie selon le type de sécurité. Consultez les rubriques [Sécuriser un cluster autonome sur Windows à l’aide de certificats X.509](service-fabric-windows-cluster-x509-security.md) ou [Sécuriser un cluster autonome sur Windows à l’aide de la sécurité Windows](service-fabric-windows-cluster-windows-security.md) pour plus d’informations sur la façon de remplir le reste de la section **security**.

<a id="nodetypes"></a>

### <a name="node-types"></a>Types de nœuds
La section **nodeTypes** décrit le type des nœuds de votre cluster. Au moins un type de nœud doit être spécifié pour un cluster, comme indiqué dans l’extrait de code ci-dessous. 

    "nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpointPort": "19001",
        "leaseDriverEndpointPort": "19002"
        "serviceConnectionEndpointPort": "19003",
        "httpGatewayEndpointPort": "19080",
        "applicationPorts": {
            "startPort": "20575",
            "endPort": "20605"
        },
        "ephemeralPorts": {
            "startPort": "20606",
            "endPort": "20861"
        },
        "isPrimary": true
    }]

La valeur **name** représente le nom convivial de ce type de nœud particulier. Pour créer un nœud de ce type, affectez son nom convivial à la variable **nodeTypeRef** pour ce nœud, comme indiqué [ci-dessus](#clusternodes). Pour chaque type de nœud, définissez les points de terminaison de connexion qui seront utilisés. Vous pouvez choisir n’importe quel numéro de port pour ces points de terminaison de connexion, à condition qu’ils n’entrent pas en conflit avec d’autres points de terminaison de ce cluster. Un cluster à plusieurs nœuds contient un ou plusieurs nœuds principaux (c'est-à-dire que **isPrimary** a la valeur *true*) en fonction du [**reliabilityLevel**](#reliability). Consultez la rubrique [Considérations en matière de planification de la capacité du cluster Service Fabric](service-fabric-cluster-capacity.md) pour plus d’informations sur les valeurs **nodeTypes** et **reliabilityLevel** et pour connaître la différence entre les types de nœud principal et non principal. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Points de terminaison utilisés pour configurer les types de nœuds
* *clientConnectionEndpointPort* est le port utilisé par le client pour se connecter au cluster lorsque vous utilisez les API clientes. 
* *clusterConnectionEndpointPort* est le port sur lequel les nœuds communiquent entre eux.
* *leaseDriverEndpointPort* est le port utilisé par le pilote de lease cluster pour savoir si les nœuds sont toujours actifs. 
* *serviceConnectionEndpointPort* est le port utilisé par les applications et les services déployés sur un nœud pour communiquer avec le client Service Fabric sur ce nœud spécifique.
* *httpGatewayEndpointPort* est le port utilisé par le Service Fabric Explorer pour se connecter au cluster.
* *ephemeralPorts* remplacent les [ports dynamiques utilisés par le système d’exploitation](https://support.microsoft.com/kb/929851). Service Fabric utilise une partie de ces ports d’application et le reste est disponible pour le système d’exploitation. Il mappe également cette plage sur la plage existante dans le système d’exploitation. Vous pouvez donc utiliser en toute circonstance les plages spécifiées dans les exemples de fichiers JSON. Vous devez vous assurer que la différence entre les ports de début et de fin est au moins de 255. Vous pouvez rencontrer des conflits si cette différence est trop faible, étant donné que cette plage est partagée avec le système d’exploitation. Consultez la plage de ports dynamiques configurée en exécutant `netsh int ipv4 show dynamicport tcp`.
* *applicationPorts* sont les ports qui sont utilisés par les applications Service Fabric. Ils doivent être un sous-ensemble des *ephemeralPorts* et suffir à couvrir les exigences en matière de points de terminaison de vos applications. Service Fabric les utilise chaque fois que des nouveaux ports sont nécessaires et prend également en charge l’ouverture du pare-feu pour ces ports. 
* *reverseProxyEndpointPort* est un point de terminaison de proxy inverse facultatif. Consultez [Proxy inverse de Service Fabric](service-fabric-reverseproxy.md) pour en savoir plus. 

### <a name="other-settings"></a>Autres paramètres
La section **fabricSettings** vous permet de définir les répertoires racine des données et journaux Service Fabric. Vous pouvez personnaliser ces éléments uniquement lors de la création initiale du cluster. Voici un exemple d’extrait de cette section.

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

Nous vous recommandons d’utiliser un lecteur autre que celui du système d’exploitation pour FabricDataRoot et FabricLogRoot pour plus de fiabilité en cas de défaillance du système d’exploitation. Notez que si vous personnalisez uniquement la racine des données, la racine du journal sera placée un niveau en dessous de la racine des données.

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous disposez d’un fichier ClusterConfig.JSON complètement configuré selon votre cluster autonome, vous pouvez déployer votre cluster en suivant les instructions de l’article [Créer et gérer un cluster exécuté sur Windows Server](service-fabric-cluster-creation-for-windows-server.md) puis passer à l’étape [Visualiser votre cluster à l’aide de l’outil Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).




<!--HONumber=Nov16_HO3-->


