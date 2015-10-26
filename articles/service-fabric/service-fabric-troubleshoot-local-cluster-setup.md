<properties
   pageTitle="Dépanner votre configuration de cluster Service Fabric locale | Microsoft Azure"
   description="Cet article aborde un ensemble de suggestions relatives à la résolution des problèmes de votre cluster de développement local"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/13/2015"
   ms.author="seanmck"/>

# Résoudre les problèmes d'installation de votre cluster de développement local

Si vous rencontrez un problème en interagissant avec votre cluster de développement local, examinez les suggestions suivantes de résolution.

## Échecs de configuration du cluster

### Impossible de nettoyer les journaux de Service Fabric

#### Problème

Lors de l’exécution du script DevClusterSetup, une erreur de ce type peut s’afficher :

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### Solution

Fermez la fenêtre PowerShell actuelle et lancez une nouvelle fenêtre PowerShell en tant qu’administrateur. Vous devriez pouvoir exécuter le script.

## Échecs de connexion au cluster

### TypeInitializationException

#### Problème

Lors de la connexion au cluster dans PowerShell ou Service Fabric Explorer, un élément TypeInitializationException apparaît pour System.Fabric.Common.AppTrace.

#### Solution

Votre variable PATH n’a pas été correctement définie durant l’installation. Déconnectez-vous de Windows, puis reconnectez-vous. Cela permettra d’actualiser votre variable.

### La connexion au cluster est mise en échec avec une indication de fermeture de l’objet

#### Problème

Un appel à Connect-ServiceFabricCluster est mis en échec avec une erreur de ce type :

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### Solution

Fermez la fenêtre PowerShell actuelle et lancez une nouvelle fenêtre PowerShell en tant qu’administrateur. Vous devez être maintenant en mesure de vous connecter.

### FabricConnectionDeniedException

#### Problème

Lorsque vous procédez au débogage de Visual Studio, vous obtenez un élément FabricConnectionDeniedException.

#### Solution

Cette erreur se produit généralement lorsque vous essayez de démarrer manuellement un processus hôte de service, sans recourir au runtime de Service Fabric.

Assurez-vous de ne pas disposer de projets de service définis en tant que projets de démarrage dans votre solution. Seuls les projets d’application Service Fabric doivent être définis en tant que projets de démarrage.


## Étapes suivantes

- [Comprendre votre cluster et résoudre les problèmes à l’aide des rapports d’intégrité système](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Visualisation de votre cluster à l’aide de l’outil Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

<!---HONumber=Oct15_HO3-->