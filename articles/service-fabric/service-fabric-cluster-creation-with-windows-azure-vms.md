---
title: "Créer un cluster autonome avec des machines virtuelles Azure Windows | Microsoft Docs"
description: "Découvrez comment créer et gérer un cluster Azure Service Fabric sur des machines virtuelles sous Windows Server."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 7eeb40d2-fb22-4a77-80ca-f1b46b22edbc
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/21/2017
ms.author: ryanwi;chackdan
redirect_url: /azure/service-fabric/service-fabric-cluster-creation-via-arm
ms.translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: ba1f6e5662700c309fcf198a4e114fd9b2b47c5f
ms.contentlocale: fr-fr
ms.lasthandoff: 03/29/2017


---
# <a name="create-a-three-node-standalone-service-fabric-cluster-with-azure-virtual-machines-running-windows-server"></a>Créer un cluster Service Fabric autonome à trois nœuds avec des machines virtuelles Azure sous Windows Server
Cet article décrit comment créer un cluster sur des machines virtuelles Azure Windows, à l’aide du programme d’installation Service Fabric autonome pour Windows Server. Il s’agit d’un cas spécial de [Créer et gérer un cluster sur Windows Server](service-fabric-cluster-creation-for-windows-server.md) où les machines virtuelles sont des [machines virtuelles Azure sur Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), mais vous ne créez pas de [cluster Service Fabric cloud Azure](service-fabric-cluster-creation-via-portal.md). La différence est que le cluster Service Fabric autonome créé par la procédure suivante est entièrement géré par vos soins, alors que les clusters Service Fabric cloud Azure sont gérés et mis à niveau par le fournisseur de ressources Service Fabric.

## <a name="steps-to-create-the-standalone-cluster"></a>Étapes pour créer le cluster autonome
1. Connectez-vous au portail Azure et créez une machine virtuelle Windows Server 2012 R2 Datacenter ou Windows Server 2016 Datacenter dans un groupe de ressources. Lisez l’article [Créer une machine virtuelle Windows dans le portail Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour plus d’informations.
2. Ajoutez encore quelques machines virtuelles au même groupe de ressources. Vérifiez que chaque machine virtuelle a le même nom d’utilisateur d’administration et le même mot de passe lors de la création. Une fois créées, vous devriez voir les trois machines virtuelles sur le même réseau virtuel.
3. Connectez-vous à chacune des machines virtuelles et désactivez le Pare-feu Windows à l’aide du [tableau de bord Gestionnaire de serveur, Serveur Local](https://technet.microsoft.com/library/jj134147.aspx). Cela permet de s’assurer que le trafic réseau peut communiquer entre les machines. Une fois connecté à chaque machine, obtenez l’adresse IP en ouvrant une invite de commandes et en tapant `ipconfig`. Vous pouvez également voir l’adresse IP de chaque machine sur le portail, en sélectionnant la ressource de réseau virtuel pour le groupe de ressources et en vérifiant les interfaces réseau créées pour chacun de ces machines.
4. Connectez-vous à l’une des machines virtuelles et vérifiez que vous pouvez communiquer avec les deux autres à l’aide d’une commande ping.
5. Connectez-vous à l’une des machines virtuelles et [téléchargez le package Service Fabric pour Windows Server autonome](http://go.microsoft.com/fwlink/?LinkId=730690) dans un nouveau dossier sur la machine, puis extrayez le package.
6. Ouvrez le fichier *ClusterConfig.Unsecure.MultiMachine.json* dans le Bloc-notes et modifiez chaque nœud avec les trois adresses IP des machines virtuelles. Modifiez le nom du cluster en haut et enregistrez le fichier.  Vous trouverez ci-dessous un exemple partiel du manifeste de cluster.
   
    ```
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "01-2017",
        "nodes": [
        {
            "nodeName": "standalonenode0",
            "iPAddress": "10.1.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "standalonenode1",
            "iPAddress": "10.1.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "standalonenode2",
            "iPAddress": "10.1.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
        ],
    ```
7. Si vous souhaitez en faire un cluster sécurisé, choisissez la mesure de sécurité que vous souhaitez utiliser et suivez les étapes décrites dans le lien associé : [X509 Certificate](service-fabric-windows-cluster-x509-security.md) (certificat X509) ou [Windows Security](service-fabric-windows-cluster-windows-security.md) (sécurité Windows). Si vous configurez le cluster à l’aide de Windows Security (sécurité de Windows), vous devrez configurer un contrôleur de domaine pour gérer Active Directory. Notez que l’utilisation d’un ordinateur de contrôleur de domaine en tant que nœud Service Fabric n'est pas prise en charge.
8. Ouvrez une [fenêtre PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Accédez au dossier dans lequel vous avez extrait le package du programme d’installation autonome téléchargé et enregistré le fichier de configuration de cluster. Exécutez la commande PowerShell suivante pour déployer le cluster :
   
    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
    ```

Le script configure à distance le cluster Service Fabric et rendra compte de la progression à mesure que le déploiement s’effectue.

9. Après environ une minute, vous pouvez vérifier que le cluster est opérationnel en vous connectant à Service Fabric Explorer en utilisant une des adresses IP de la machine, par exemple avec `http://10.1.0.5:19080/Explorer/index.html`. 

## <a name="next-steps"></a>Étapes suivantes
* [Créer des clusters Service Fabric autonomes sur un serveur Windows Server ou Linux](service-fabric-deploy-anywhere.md)
* [Ajouter ou supprimer des nœuds d’un cluster Service Fabric autonome](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Paramètres de configuration pour un cluster Windows autonome](service-fabric-cluster-manifest.md)
* [Sécuriser un cluster autonome sur Windows à l’aide de la sécurité Windows](service-fabric-windows-cluster-windows-security.md)
* [Sécuriser un cluster autonome sur Windows à l’aide de certificats X509](service-fabric-windows-cluster-x509-security.md)


