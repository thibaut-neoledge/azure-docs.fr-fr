---
title: "Exemple de script Azure PowerShell - Mettre à niveau une application Service Fabric | Microsoft Docs"
description: "Exemple de script Azure PowerShell - Mettre à niveau une application Service Fabric."
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: ryanwi
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 43c1c0d38d12a36c39a3962a3399d9c05937e8b1
ms.contentlocale: fr-fr
ms.lasthandoff: 08/24/2017

---

# <a name="upgrade-a-service-fabric-application"></a>Mettre à niveau une application Service Fabric

Cet exemple de script met à niveau une instance d’application Service Fabric en cours d’exécution vers la version 1.3.0. Le script copie le nouveau package d’application dans le magasin d’images de cluster, enregistre le type d’application, démarre une mise à niveau contrôlée et vérifie en permanence l’état de mise à niveau jusqu’à la fin de la mise à niveau ou jusqu’à la restauration. Personnalisez les paramètres selon vos besoins. 

Si nécessaire, installez le module Service Fabric PowerShell avec le [Kit de développement logiciel (SDK) Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Mettre à niveau une application")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | Récupère toutes les applications du cluster Service Fabric ou une application en particulier.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | Récupère l’état d’une mise à niveau d’application Service Fabric. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | Récupère les types d’applications Service Fabric enregistrés sur le cluster Service Fabric. |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Annule l’enregistrement d’un type d’application Service Fabric.  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Copie un package d’application Service Fabric dans le magasin d’images.  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Enregistre un type d’application Service Fabric. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Met à niveau une application Service Fabric vers la version spécifiée du type d’application. |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Service Fabric PowerShell, consultez la [documentation Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Vous trouverez des exemples supplémentaires de scripts PowerShell pour Azure Service Fabric sur la page [Exemples Azure PowerShell](../service-fabric-powershell-samples.md).

