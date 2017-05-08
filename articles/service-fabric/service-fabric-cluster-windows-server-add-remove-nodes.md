---
title: "Ajouter ou supprimer des nœuds d’un cluster Service Fabric autonome | Microsoft Docs"
description: "Apprenez à ajouter ou supprimer des nœuds d’un cluster Azure Service Fabric sur une machine physique ou virtuelle sous Windows Server, qu’elle soit locale ou dans un cloud."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: bc6b8fc0-d2af-42f8-a164-58538be38d02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/02/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: fa59bae2b824e6b75e120ab2b61027746ee1ea78
ms.lasthandoff: 04/27/2017


---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Ajouter ou supprimer des nœuds d’un cluster Service Fabric autonome sous Windows Server
Une fois que vous avez [créé votre cluster Service Fabric autonome sur des ordinateurs Windows Server](service-fabric-cluster-creation-for-windows-server.md), les besoins de votre entreprise peuvent évoluer de sorte que vous devrez peut-être ajouter ou supprimer plusieurs nœuds de votre cluster. Cet article fournit des étapes détaillées pour atteindre cet objectif.

## <a name="add-nodes-to-your-cluster"></a>Ajouter des nœuds à votre cluster
1. Préparez la machine virtuelle/l’ordinateur que vous souhaitez ajouter à votre cluster en suivant les étapes présentées dans la section [Préparer les machines à la configuration requise pour le déploiement de cluster](service-fabric-cluster-creation-for-windows-server.md) .
2. Prévoyez le domaine d’erreur et le domaine de mise à niveau auquel vous allez ajouter cette machine virtuelle ou cet ordinateur.
3. Avec Bureau à distance (RDP), accédez à la machine virtuelle ou à l’ordinateur que vous souhaitez ajouter au cluster.
4. Copiez ou [téléchargez le package autonome Service Fabric pour Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) sur cette machine virtuelle ou cet ordinateur et décompressez le package.
5. Exécutez PowerShell en tant qu’administrateur, puis naviguez jusqu’à l’emplacement du package décompressé.
6. Exécutez le script PowerShell *AddNode.ps1* avec les paramètres qui décrivent le nouveau nœud à ajouter. L’exemple ci-dessous ajoute un nouveau nœud nommé VM5, de type NodeType0, avec l’adresse IP 182.17.34.52, à UD1 et fd:/dc1/r0. *ExistingClusterConnectionEndPoint* est un point de terminaison de connexion pour un nœud déjà présent dans le cluster existant. Pour ce point de terminaison, vous pouvez choisir l’adresse IP de *n’importe quel* nœud du cluster.

```
.\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA

```
Vous pouvez vérifier si le nouveau nœud est ajouté en exécutant l’applet de commande [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps).


## <a name="remove-nodes-from-your-cluster"></a>Supprimer des nœuds de votre cluster
Selon le niveau de fiabilité choisi pour le cluster, vous ne pouvez pas supprimer les n (3/5/7/9) premiers nœuds du type de nœud principal. Notez que l’exécution de la commande RemoveNode sur un cluster de développement n’est pas prise en charge.

1. Avec Bureau à distance (RDP), accédez à la machine virtuelle ou à l’ordinateur que vous souhaitez supprimer du cluster.
2. Copiez ou [téléchargez le package autonome Service Fabric pour Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) et décompressez le package sur cette machine virtuelle ou cet ordinateur.
3. Exécutez PowerShell en tant qu’administrateur, puis naviguez jusqu’à l’emplacement du package décompressé.
4. Exécutez le script *RemoveNode.ps1* dans PowerShell. L’exemple ci-dessous supprime le nœud actif du cluster. Le *ExistingClientConnectionEndpoint* est un point de terminaison de connexion client pour tout nœud restant dans le cluster. Choisissez l’adresse IP et le port de point de terminaison de *tout* **autre nœud** du cluster. Cet **autre nœud** mettra à jour à son tour la configuration du cluster pour le nœud supprimé. 

```

.\RemoveNode.ps1 -ExistingClientConnectionEndpoint 182.17.34.50:19000

```

> [!NOTE]
> Certains nœuds ne peuvent pas être supprimés en raison des dépendances de services système. Ces nœuds sont des nœuds principaux et peuvent être identifiés en interrogeant le manifeste de cluster à l’aide de `Get-ServiceFabricClusterManifest` et en recherchant des entrées de nœud marquées avec `IsSeedNode=”true”`. 
> 
> 

Même après la suppression d’un nœud, il peut apparaître comme défaillant dans des requêtes et dans SFX. Il s’agit d’un problème connu. Il sera résolu dans la prochaine version. 


## <a name="remove-node-types-from-your-cluster"></a>Supprimer des types de nœuds de votre cluster
La suppression d’un type de nœud nécessite une attention supplémentaire. Avant de supprimer un type de nœud, vérifiez s’il existe un nœud qui référence le type de nœud concerné.


## <a name="replace-primary-nodes-of-your-cluster"></a>Remplacer les nœuds principaux de votre cluster
Le remplacement des nœuds principaux doit être effectué un nœud à la fois, au lieu de supprimer, puis d’ajouter des nœuds par lots.


## <a name="next-steps"></a>Étapes suivantes
* [Paramètres de configuration pour un cluster Windows autonome](service-fabric-cluster-manifest.md)
* [Sécuriser un cluster autonome sur Windows à l’aide de certificats X509](service-fabric-windows-cluster-x509-security.md)
* [Créer un cluster Service Fabric autonome avec des machines virtuelles Azure Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)


