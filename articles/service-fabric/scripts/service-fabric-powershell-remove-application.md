---
title: "Exemple de script Azure PowerShell - Supprimer l’application d’un cluster | Microsoft Docs"
description: "Exemple de script Azure PowerShell - Supprimer une application d’un cluster Service Fabric"
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
ms.topic: sample
ms.date: 06/20/2017
ms.author: ryanwi
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 951c9a014f587ce53450dd705fc478da625b1e6b
ms.contentlocale: fr-fr
ms.lasthandoff: 07/15/2017

---

# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Supprimer une application d’un cluster Service Fabric

Cet exemple de script supprime une instance d’application Service Fabric en cours d’exécution, annule l’inscription d’un type d’application et de la version du cluster, et supprime le package d’application à partir du magasin d’images du cluster.  La suppression de l’instance d’application entraîne également celle de toutes les instances de service en cours d’exécution associées à cette application. Personnalisez les paramètres, le cas échéant. 

Si nécessaire, installez le module Service Fabric PowerShell avec le [Kit de développement logiciel (SDK) Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Supprimer une application d’un cluster")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Supprime une instance d’application Service Fabric en cours d’exécution du cluster.  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Annule l’inscription d’un type d’application Service Fabric et de sa version dans le cluster. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Supprime un package d’application Service Fabric du magasin d’images.|

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Service Fabric PowerShell, consultez la [documentation Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Vous trouverez des exemples supplémentaires de scripts PowerShell pour Azure Service Fabric sur la page [Azure PowerShell Samples](../service-fabric-powershell-samples.md) (Exemples Azure PowerShell).

