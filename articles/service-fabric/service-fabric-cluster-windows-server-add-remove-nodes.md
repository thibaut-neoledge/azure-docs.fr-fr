---
title: Ajouter ou supprimer des nœuds d’un cluster Service Fabric autonome | Microsoft Docs
description: Apprenez à ajouter ou supprimer des nœuds d’un cluster Azure Service Fabric sur une machine physique ou virtuelle sous Windows Server, qu’elle soit locale ou dans un cloud.
services: service-fabric
documentationcenter: .net
author: dsk-2015
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2016
ms.author: dkshir;chackdan

---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Ajouter ou supprimer des nœuds d’un cluster Service Fabric autonome sous Windows Server
Une fois que vous avez [créé votre cluster Service Fabric autonome sur des ordinateurs Windows Server](service-fabric-cluster-creation-for-windows-server.md), les besoins de votre entreprise peuvent évoluer de sorte que vous devrez peut-être ajouter ou supprimer plusieurs nœuds de votre cluster. Cet article fournit des étapes détaillées pour atteindre cet objectif.

## <a name="add-nodes-to-your-cluster"></a>Ajouter des nœuds à votre cluster
1. Préparez la machine virtuelle/l’ordinateur que vous souhaitez ajouter à votre cluster en suivant les étapes présentées dans la section [Préparer les machines à la configuration requise pour le déploiement de cluster](service-fabric-cluster-creation-for-windows-server.md#preparemachines) .
2. Prévoyez le domaine d’erreur et le domaine de mise à niveau auquel vous allez ajouter cette machine virtuelle ou cet ordinateur.
3. Avec Bureau à distance (RDP), accédez à la machine virtuelle ou à l’ordinateur que vous souhaitez ajouter au cluster.
4. Copiez ou [téléchargez le package autonome Service Fabric pour Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) sur cette machine virtuelle ou cet ordinateur et décompressez le package.
5. Exécutez PowerShell en tant qu’administrateur, puis naviguez jusqu’à l’emplacement du package décompressé.
6. Exécutez le script PowerShell *AddNode.ps1* avec les paramètres qui décrivent le nouveau nœud à ajouter. L’exemple ci-dessous ajoute un nouveau nœud nommé VM5, de type NodeType0, avec l’adresse IP 182.17.34.52, à UD1 et FD1. *ExistingClusterConnectionEndPoint* est un point de terminaison de connexion pour un nœud déjà présent dans le cluster existant. Pour ce point de terminaison, vous pouvez choisir l’adresse IP de *n’importe quel* nœud du cluster.

```
.\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain FD1 -AcceptEULA

```

## <a name="remove-nodes-from-your-cluster"></a>Supprimer des nœuds de votre cluster
1. Selon le niveau de fiabilité choisi pour le cluster, vous ne pouvez pas supprimer les n (3/5/7/9) premiers nœuds du type de nœud principal.
2. L’exécution de la commande RemoveNode sur un cluster de développement n’est pas prise en charge.
3. Avec Bureau à distance (RDP), accédez à la machine virtuelle ou à l’ordinateur que vous souhaitez supprimer du cluster.
4. Copiez ou [téléchargez le package autonome Service Fabric pour Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) et décompressez le package sur cette machine virtuelle ou cet ordinateur.
5. Exécutez PowerShell en tant qu’administrateur, puis naviguez jusqu’à l’emplacement du package décompressé.
6. Exécutez le script PowerShell *RemoveNode.ps1* . L’exemple ci-dessous supprime le nœud actif du cluster. *ExistingClusterConnectionEndPoint* est un point de terminaison de connexion pour un nœud déjà présent dans le cluster existant. Pour ce point de terminaison, vous devez choisir l’adresse IP de *tout* **autre nœud** du cluster.

```
.\RemoveNode.ps1 -ExistingClusterConnectionEndPoint 182.17.34.50:19000
```

Défaut connu qui sera résolu dans la prochaine version : même après avoir supprimé un nœud, le nœud apparaît comme indisponible dans les requêtes et SFX. 

## <a name="next-steps"></a>Étapes suivantes
* [Paramètres de configuration pour un cluster Windows autonome](service-fabric-cluster-manifest.md)
* [Sécuriser un cluster autonome sur Windows à l’aide de la sécurité Windows](service-fabric-windows-cluster-windows-security.md)
* [Sécuriser un cluster autonome sur Windows à l’aide de certificats X509](service-fabric-windows-cluster-x509-security.md)
* [Créer un cluster Service Fabric autonome avec des machines virtuelles Azure Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)

<!--HONumber=Oct16_HO2-->


