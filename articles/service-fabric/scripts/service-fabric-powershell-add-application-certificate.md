---
title: "Exemple de script Azure PowerShell - Ajouter un certificat d’application à un cluster | Microsoft Docs"
description: "Exemple de script Azure PowerShell - Ajouter un certificat d’application à un cluster Service Fabric"
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
ms.openlocfilehash: 3212944a8bd882ced86e841944ac60bd1845e3d1
ms.contentlocale: fr-fr
ms.lasthandoff: 07/15/2017

---

# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Ajouter un certificat d’application à un cluster Service Fabric

Cet exemple de script crée un certificat auto-signé dans le coffre de clés Azure spécifié et l’installe sur tous les nœuds du cluster Service Fabric. Le certificat est également téléchargé vers un dossier local. Le nom du certificat téléchargé est le même que celui du certificat placé dans le coffre de clés. Personnalisez les paramètres, le cas échéant.

Si nécessaire, installez Azure PowerShell à l’aide des instructions figurant dans le [Guide Azure PowerShell](/powershell/azure/overview), puis exécutez `Login-AzureRmAccount` pour créer une connexion avec Azure. 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-application-certificate/add-new-application-certificate.ps1 "Ajouter un certificat d’application à un cluster")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [Add-AzureRmServiceFabricApplicationCertificate](/powershell/module/azurerm.servicefabric/Add-AzureRmServiceFabricApplicationCertificate) | Ajoutez un nouveau certificat d’application au groupe de machines virtuelles identiques qui composent le cluster.  |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour Azure Service Fabric sur la page [Azure PowerShell Samples](../service-fabric-powershell-samples.md) (Exemples Azure PowerShell).

