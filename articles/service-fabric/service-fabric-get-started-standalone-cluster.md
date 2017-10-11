---
title: Configurer un cluster Azure Service Fabric autonome | Microsoft Docs
description: "Créez un cluster de développement autonome avec trois nœuds s’exécutant sur le même ordinateur. Une fois l’installation terminée, vous serez prêt à créer un cluster de plusieurs ordinateurs."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/06/2017
ms.author: dekapur
ms.openlocfilehash: 5c8f4c784eed7b64810a3dd1c36c043d22a66936
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2017
---
# <a name="create-your-first-service-fabric-standalone-cluster"></a>Créer votre premier cluster Service Fabric autonome
Vous pouvez créer un cluster Service Fabric autonome sur n’importe quel ordinateur ou machine virtuelle exécutant Windows Server 2012 R2 ou Windows Server 2016, en local ou dans le cloud. Ce démarrage rapide vous aide à créer un cluster de développement autonome en seulement quelques minutes.  Une fois que vous avez terminé, vous disposez d’un cluster à trois nœuds s’exécutant sur un seul ordinateur sur lequel vous pouvez déployer des applications.

## <a name="before-you-begin"></a>Avant de commencer
Service Fabric fournit un package d’installation pour créer des clusters Service Fabric autonomes.  [Téléchargez le package d’installation](http://go.microsoft.com/fwlink/?LinkId=730690).  Décompressez le package d’installation dans un dossier sur l’ordinateur ou la machine virtuelle sur lequel/laquelle vous configurez le cluster de développement.  Le contenu du package d’installation est décrit en détail [ici](service-fabric-cluster-standalone-package-contents.md).

L’administrateur de cluster déployant et configurant le cluster doit disposer de privilèges d’administrateur sur l’ordinateur. Vous ne pouvez pas installer Service Fabric sur un contrôleur de domaine.

## <a name="validate-the-environment"></a>Valider l’environnement
Le script *TestConfiguration.ps1* contenu dans le package autonome est utilisé pour analyser les meilleures pratiques, afin de vérifier si un cluster peut être déployé dans un environnement donné. La section [Préparation du déploiement](service-fabric-cluster-standalone-deployment-preparation.md) répertorie les conditions préalables et les exigences de l’environnement. Exécutez le script pour vérifier si vous pouvez créer le cluster de développement :

```powershell
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
```
## <a name="create-the-cluster"></a>Création du cluster
Plusieurs fichiers d’exemples de configuration de cluster sont installés avec le package d’installation. *ClusterConfig.Unsecure.DevCluster.json* correspond à la configuration de cluster la plus simple : un cluster à trois nœuds non sécurisé, s’exécutant sur un seul ordinateur.  Les autres fichiers de configuration décrivent des clusters uniques ou de plusieurs ordinateurs, sécurisés à l’aide de certificats X.509 ou de la sécurité Windows.  Vous n’avez pas besoin de modifier les paramètres de configuration par défaut pour ce didacticiel, mais parcourez le fichier de configuration et familiarisez-vous avec les paramètres.  La section **Nœuds** décrit les trois nœuds du cluster : nom, adresse IP, [type de nœud, domaine d’erreur et domaine de mise à niveau](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  La section **Propriétés** définit la [sécurité, le niveau de fiabilité, la collecte des diagnostics et les types de nœuds](service-fabric-cluster-manifest.md#cluster-properties) pour le cluster.

Ce cluster n’est pas sécurisé.  N’importe qui peut se connecter anonymement et effectuer des opérations de gestion. Les clusters de production doivent donc toujours être sécurisés à l’aide de certificats X.509 ou via la sécurité Windows.  La sécurité peut uniquement être configurée au moment de la création du cluster et il n’est pas possible d’activer la sécurité une fois le cluster créé.  Lisez [Sécuriser un cluster](service-fabric-cluster-security.md) pour en savoir plus sur la sécurité du cluster Service Fabric.  

Pour créer le cluster de développement à trois nœuds, exécutez le script *CreateServiceFabricCluster.ps1* à partir d’une session administrateur PowerShell :

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Le package de runtime Service Fabric est automatiquement téléchargé et installé lors de la création du cluster.

## <a name="connect-to-the-cluster"></a>Connexion au cluster
Votre cluster de développement à trois nœuds est maintenant en cours d’exécution. Le module Service Fabric PowerShell est installé avec le runtime.  Vous pouvez vérifier que le cluster s’exécute à partir du même ordinateur ou à partir d’un ordinateur distant avec le runtime Service Fabric.  L’applet de commande [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) établit une connexion au cluster.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
```
Pour obtenir des exemples de connexion à un cluster, consultez [Se connecter à un cluster sécurisé](service-fabric-connect-to-secure-cluster.md) . Une fois connecté au cluster, utilisez l’applet de commande [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) pour afficher une liste des nœuds du cluster et les informations d’état pour chaque nœud. **HealthState** doit être à l’état *OK* pour chaque nœud.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

## <a name="visualize-the-cluster-using-service-fabric-explorer"></a>Visualiser votre cluster à l’aide de l’outil Service Fabric Explorer
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) est un bon outil pour visualiser votre cluster et gérer les applications.  Service Fabric Explorer est un service qui s’exécute dans le cluster, auquel vous accédez à l’aide d’un navigateur en utilisant l’adresse [http://localhost:19080/Explorer](http://localhost:19080/Explorer). 

Le tableau de bord de cluster fournit une vue d’ensemble de votre cluster, y compris un résumé de l’intégrité de l’application et du nœud. L’affichage des nœuds montre la disposition physique du cluster. Vous pouvez identifier les applications ayant déployé du code sur un nœud donné.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="remove-the-cluster"></a>Supprimer le cluster
Pour supprimer un cluster, exécutez le script PowerShell *RemoveServiceFabricCluster.ps1* à partir du dossier de package et transmettez le chemin du fichier de configuration JSON. Vous pouvez éventuellement spécifier un emplacement pour le journal de la suppression.

```powershell
# Removes Service Fabric cluster nodes from each computer in the configuration file.
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -Force
```

Pour supprimer le runtime Service Fabric de l’ordinateur, exécutez le script PowerShell suivant à partir du dossier de package.

```powershell
# Removes Service Fabric from the current computer.
.\CleanFabric.ps1
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez configuré un cluster de développement autonome, consultez les articles suivants :
* [Configurez un cluster de plusieurs ordinateurs autonome](service-fabric-cluster-creation-for-windows-server.md) et activez la sécurité.
* [Déployer des applications à l’aide de PowerShell](service-fabric-deploy-remove-applications.md)

[service-fabric-explorer]: ./media/service-fabric-get-started-standalone-cluster/sfx.png
