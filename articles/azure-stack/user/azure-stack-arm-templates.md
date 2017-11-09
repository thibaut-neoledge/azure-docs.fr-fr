---
title: "Utiliser les modèles Azure Resource Manager dans Azure Stack | Microsoft Docs"
description: "Découvrez comment utiliser les modèles Azure Resource Manager dans Azure Stack pour approvisionner des ressources."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 7648855011e8f77c35713d2d2ae50f2e474a08a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Utiliser les modèles Azure Resource Manager dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Les modèles Azure Resource Manager déploient et approvisionnent toutes les ressources de l’application en une seule opération coordonnée. Vous pouvez également redéployer des modèles pour apporter des modifications aux ressources dans le groupe de ressources.

Ces modèles peuvent être déployés à l’aide du portail Microsoft Azure Stack, de PowerShell, de l’interface de ligne de commande et de Visual Studio.

Les modèles de démarrage rapide suivants sont disponibles sur [GitHub](http://aka.ms/azurestackgithub) :

## <a name="deploy-sharepoint-non-high-availability"></a>Déploiement de SharePoint (sans haute disponibilité)
Utilisez l’extension PowerShell DSC pour créer une batterie de serveurs SharePoint 2013 comprenant les ressources suivantes :

* Un réseau virtuel
* Trois comptes de stockage
* Deux équilibreurs de charge externes
* Une machine virtuelle configurée en tant que contrôleur de domaine dans une nouvelle forêt avec un domaine unique
* Une machine virtuelle configurée sous la forme d’un serveur autonome SQL Server 2014
* Une machine virtuelle configurée comme une batterie de serveurs SharePoint 2013 composée d’un seul ordinateur

## <a name="deploy-ad-non-high-availability"></a>Déployer AD (sans haute disponibilité)
Utilisez l’extension PowerShell DSC pour créer un serveur de contrôleurs de domaine AD comprenant les ressources suivantes :

* Un réseau virtuel
* Un compte de stockage
* Un équilibreur de charge externe
* Une machine virtuelle configurée en tant que contrôleur de domaine dans une nouvelle forêt avec un domaine unique

## <a name="deploy-adsql-non-high-availability"></a>Déploiement d’AD/SQL (sans haute disponibilité)
Utilisez l’extension PowerShell DSC pour créer un serveur autonome SQL Server 2014 comprenant les ressources suivantes :

* Un réseau virtuel
* deux comptes de stockage ;
* Un équilibreur de charge externe
* Une machine virtuelle configurée en tant que contrôleur de domaine dans une nouvelle forêt avec un domaine unique
* Une machine virtuelle configurée sous la forme d’un serveur autonome SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server
Utilisez l’extension PowerShell DSC pour configurer un gestionnaire local de configuration (LCM) de machines virtuelles existant et enregistrez-le dans un serveur Pull DSC de compte Azure Automation.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Création d’une machine virtuelle à partir d’une image utilisateur
Créez une machine virtuelle à partir d’une image utilisateur personnalisée. Ce modèle déploie également un réseau virtuel (avec DNS), une adresse IP publique et une interface réseau.

## <a name="simple-vm"></a>Machine virtuelle simple
Déployez une machine virtuelle Windows comprenant un réseau virtuel (avec DNS), une adresse IP publique et une interface réseau.

## <a name="cancel-a-running-template-deployment"></a>Annuler le déploiement d’un modèle en cours d’exécution
Pour annuler le déploiement d’un modèle en cours d’exécution, utilisez la cmdlet PowerShell `Stop-AzureRmResourceGroupDeployment`.

## <a name="next-steps"></a>Étapes suivantes
[Déployer des modèles avec le portail](azure-stack-deploy-template-portal.md)

[Présentation d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)

