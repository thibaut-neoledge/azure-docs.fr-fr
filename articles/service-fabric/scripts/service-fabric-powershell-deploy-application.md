---
title: "Exemple de script Azure PowerShell - Déployer une application sur un cluster | Microsoft Docs"
description: "Exemple de script Azure PowerShell - Déployez une application sur un cluster Service Fabric."
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
ms.date: 06/20/2017
ms.author: ryanwi
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: b9aa30e18f6997050f29792e53946d5ec747e9ff
ms.contentlocale: fr-fr
ms.lasthandoff: 07/15/2017

---

# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Déployer une application sur un cluster Service Fabric

Cet exemple de script copie un package d’application dans le magasin d’images d’un cluster, inscrit le type d’application dans le cluster et crée une instance d’application à partir du type d’application.  Si des services par défaut ont été définis dans le manifeste d’application du type d’application cible, ils sont également créés à ce stade. Personnalisez les paramètres selon vos besoins. 

Si nécessaire, installez le module Service Fabric PowerShell avec le [Kit de développement logiciel (SDK) Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Déployer une application sur un cluster")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Après avoir exécuté l’exemple de script, vous pouvez utiliser le script présenté dans l’article [Supprimer une application](service-fabric-powershell-remove-application.md) pour supprimer l’instance d’application, désinscrire le type d’application et supprimer le package d’application du magasin d’images.

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Copie un package d’application dans le magasin d’images du cluster.  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Inscrit un type d’application et la version associée sur le cluster. |
|[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Crée une application à partir d’un type d’application inscrit. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Service Fabric PowerShell, consultez la [documentation Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Vous trouverez des exemples supplémentaires de scripts PowerShell pour Azure Service Fabric sur la page [Exemples Azure PowerShell](../service-fabric-powershell-samples.md).

