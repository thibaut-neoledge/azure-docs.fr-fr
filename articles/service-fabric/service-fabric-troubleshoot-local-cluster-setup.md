---
title: "Dépanner votre configuration de cluster Service Fabric locale | Microsoft Docs"
description: "Cet article aborde un ensemble de suggestions relatives à la résolution des problèmes de votre cluster de développement local"
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 97f4feaa-bba0-47af-8fdd-07f811fe2202
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: mikkelhegn
ms.openlocfilehash: aa393f884b564cee81fcf75cc2eff895efea9471
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Résoudre les problèmes d'installation de votre cluster de développement local
Si vous rencontrez un problème en interagissant avec votre cluster de développement Azure Service Fabric local, examinez les suggestions suivantes de résolution.

## <a name="cluster-setup-failures"></a>Échecs de configuration du cluster
### <a name="cannot-clean-up-service-fabric-logs"></a>Impossible de nettoyer les journaux de Service Fabric
#### <a name="problem"></a>Problème
Lors de l’exécution du script DevClusterSetup, une erreur de ce type peut s’afficher :

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Solution
Fermez la fenêtre PowerShell active et ouvrez une nouvelle fenêtre PowerShell en tant qu’administrateur. Vous devriez pouvoir exécuter le script.

## <a name="cluster-connection-failures"></a>Échecs de connexion au cluster
### <a name="service-fabric-powershell-cmdlets-are-not-recognized-in-azure-powershell"></a>Applets de commande PowerShell de Service Fabric non reconnues dans Azure PowerShell
#### <a name="problem"></a>Problème
Si vous essayez d’exécuter l’une des applets de commande PowerShell de Service Fabric, par exemple `Connect-ServiceFabricCluster` , dans une fenêtre Azure PowerShell, l’applet de commande échoue avec un message indiquant qu’elle n’est pas reconnue. Cela est dû au fait qu’Azure PowerShell utilise la version 32 bits de Windows PowerShell (même sur les versions 64 bits du système d’exploitation), tandis que les applets de commande Service Fabric fonctionnent uniquement dans des environnements 64 bits.

#### <a name="solution"></a>Solution
Exécutez toujours les applets de commande Service Fabric directement à partir de Windows PowerShell.

> [!NOTE]
> La dernière version d'Azure PowerShell ne crée aucun raccourci spécial, et cela ne devrait donc plus se reproduire.
> 
> 

### <a name="type-initialization-exception"></a>Exception durant l’initialisation de type
#### <a name="problem"></a>Problème
Quand vous êtes connecté au cluster dans PowerShell, l’erreur TypeInitializationException apparaît pour System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Solution
Votre variable PATH n’a pas été correctement définie durant l’installation. Déconnectez-vous de Windows, puis reconnectez-vous. Le chemin d’accès est alors actualisé.

### <a name="cluster-connection-fails-with-object-is-closed"></a>La connexion au cluster est mise en échec avec une indication de fermeture de l’objet
#### <a name="problem"></a>Problème
Un appel à Connect-ServiceFabricCluster est mis en échec avec une erreur de ce type :

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Solution
Fermez la fenêtre PowerShell active et ouvrez une nouvelle fenêtre PowerShell en tant qu’administrateur. Vous devez être maintenant en mesure de vous connecter.

### <a name="fabric-connection-denied-exception"></a>Exception Connexion Fabric refusée
#### <a name="problem"></a>Problème
Pendant le débogage à partir de Visual Studio, vous obtenez une erreur FabricConnectionDeniedException.

#### <a name="solution"></a>Solution
Cette erreur se produit généralement lorsque vous démarrez manuellement un processus hôte de service, sans recourir au runtime de Service Fabric.

Assurez-vous de ne pas disposer de projets de service définis en tant que projets de démarrage dans votre solution. Seuls les projets d’application Service Fabric doivent être définis en tant que projets de démarrage.

> [!TIP]
> Si, après l’installation, votre cluster local commence à se comporter de manière anormale, vous pouvez le réinitialiser à l’aide de l’application de barre d’état système de gestionnaire de cluster local. Cela supprime le cluster existant et en installe un nouveau. Notez que toutes les applications déployées et les données associées sont supprimées.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* [Comprendre votre cluster et résoudre les problèmes à l’aide des rapports d’intégrité système](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Visualiser votre cluster à l’aide de l’outil Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

