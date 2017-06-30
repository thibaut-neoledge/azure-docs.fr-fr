---
title: "Différences Azure Service Fabric entre Linux et Windows | Microsoft Docs"
description: "Différences entre la version préliminaire d’Azure Service Fabric sur Linux et Azure Service Fabric sur Windows."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: e2d21b28c482427c60f708171336e6901b50e544
ms.contentlocale: fr-fr
ms.lasthandoff: 05/26/2017


---
# <a name="differences-between-service-fabric-on-linux-preview-and-windows-generally-available"></a>Différences entre Service Fabric sur Linux (version préliminaire) et Windows (mise à la disposition générale)

Étant donné que Service Fabric sur Linux est une version préliminaire, certaines fonctionnalités sont prises en charge sur Windows, mais pas encore sur Linux. Les ensembles de fonctionnalités seront identiques lors de la mise à disposition générale de Service Fabric sur Linux. Avec les versions à venir, cet écart de fonctionnalités sera réduit. Les différences suivantes existent entre les versions les plus récentes disponibles (c’est-à-dire entre la version 5.6 pour Windows et la version 5.5 pour Linux) : 

* Les collections fiables (et les services avec état fiable) 
* ReverseProxy 
* Le programme d’installation autonome 
* La validation de schéma XML pour les fichiers de manifeste 
* La redirection de la console 
* Le service d’analyse des erreurs
* Docker Compose et les pilotes de journalisation et de volume pour les conteneurs 
* La gestion des ressources pour les conteneurs et les services 
* Service DNS
* La prise en charge d’Azure Active Directory
* Des équivalents de commandes d’interface de ligne de commande de certaines commandes Powershell 
* Seul un sous-ensemble de commandes Powershell peut être exécuté sur un cluster Linux (comme expliqué en détail dans la section suivante).

>[!NOTE]
>La redirection de console n’est pas prise en charge dans les clusters de production, même sur Windows.

Les outils de développement sont également différents entre Windows et Linux. VisualStudio, Powershell, VSTS et ETW sont utilisés sur Windows, tandis que Yeoman, Eclipse, Jenkins et LTTng sont utilisés sur Linux.

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>Applets de commande PowerShell ne fonctionnant pas sur un cluster Linux Service Fabric

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Cmd
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy



## <a name="next-steps"></a>Étapes suivantes
* [Préparer votre environnement de développement sur Linux](service-fabric-get-started-linux.md)
* [Prepare your development environment on OSX (Préparer votre environnement de développement sur OSX)](service-fabric-get-started-mac.md)
* [Create and deploy your first Service Fabric Java application on Linux using Yeoman (Créer et déployer votre première application Java Service Fabric sur Linux à l’aide de Yeoman)](service-fabric-create-your-first-linux-application-with-java.md)
* [Create and deploy your first Service Fabric Java application on Linux using Service Fabric Plugin for Eclipse (Créer et déployer votre première application Java Service Fabric sur Linux à l’aide du plug-in Service Fabric pour Eclipse)](service-fabric-get-started-eclipse.md)
* [Create your first Java application on Linux (Créer votre première application Java sur Linux)](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Use the Azure CLI to manage your Service Fabric applications (Utiliser l’interface Azure CLI pour gérer vos applications Service Fabric)](service-fabric-azure-cli.md)

