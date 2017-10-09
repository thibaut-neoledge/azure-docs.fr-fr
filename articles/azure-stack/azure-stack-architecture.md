---
title: "Architecture du Kit de développement Microsoft Azure Stack | Microsoft Docs"
description: "Affichez l’architecture du Kit de développement Microsoft Azure Stack."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e8ff17e069bd9237bf06cd79b4c222c50a999eda
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Architecture du Kit de développement Microsoft Azure Stack

*S’applique à : Kit de développement Azure Stack*

Le Kit de développement Azure Stack est un déploiement à nœud unique d’Azure Stack. Tous les composants sont installés sur des machines virtuelles exécutées sur un ordinateur hôte unique. 

## <a name="logical-architecture-diagram"></a>Schéma de l’architecture logique
Le schéma suivant illustre l’architecture logique du Kit de développement Azure Stack et de ses composants.

![](media/azure-stack-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Rôles de machine virtuelle
Le Kit de développement Azure Stack offre des services à l’aide des machines virtuelles suivantes sur l’ordinateur hôte :

| Nom | Description |
| ----- | ----- |
| **AzS-ACS01** | Services de stockage Azure Stack.|
| **AzS-ADFS01** | Services de fédération Active Directory (ADFS).  |
| **AzS-BGPNAT01** | Routeur Edge et fournit des capacités NAT et VPN pour Azure Stack. |
| **AzS-CA01** | Services d’autorité de certification pour les services de rôle Azure Stack.|
| **AzS-DC01** | Services Active Directory, DNS et DHCP pour Microsoft Azure Stack.|
| **AzS-ERCS01** | Machine virtuelle la console de récupération d’urgence. |
| **AzS-GWY01** | Services de passerelle Edge, comme les connexions de site à site VPN pour les réseaux locataires.|
| **AzS-NC01** | Contrôleur réseau qui gère les services réseau Azure Stack.  |
| **AzS-SLB01** | Services multiplexeur d’équilibrage de charge dans Azure Stack pour les clients et les services d’infrastructure Azure Stack.  |
| **AzS-SQL01** | Magasin de données interne pour les rôles d’infrastructure Azure Stack.  |
| **AzS-WAS01** | Portail d’administration Azure Stack et services Azure Resource Manager.|
| **AzS-WASP01**| Portail utilisateur (locataire) Azure Stack et services Azure Resource Manager.|
| **AzS-XRP01** | Contrôleur de gestion d’infrastructure pour Microsoft Azure Stack, notamment les fournisseurs de ressources Calcul, Réseau et Stockage.|


## <a name="next-steps"></a>Étapes suivantes
[Déployer Azure Stack](azure-stack-deploy.md)

[Test des premiers scénarios](azure-stack-first-scenarios.md)


