---
title: "Qu’est-ce qu’Azure Stack ? | Microsoft Docs"
description: "Azure Stack vous permet de tester les services Azure dans votre centre de données."
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/25/2017
ms.author: helaw
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 950ba44c0b7eb80c9b0a3c69a9fca03cd244576d
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="what-is-azure-stack"></a>Qu’est-ce qu’Azure Stack ?

Microsoft Azure Stack est une plateforme cloud hybride permettant de créer des services Azure à partir du centre de données de votre organisation.  Azure Stack autorise de nouveaux scénarios pour vos applications modernes dans des situations clés, comme les environnements de périmètre et déconnectés ou la satisfaction d’exigences de sécurité et de conformité spécifiques.  Vous disposez de deux options pour déployer Azure Stack.

## <a name="azure-stack-integrated-systems"></a>Systèmes intégrés Azure Stack
Les systèmes intégrés Azure Stack sont disponibles par le biais d’un partenariat entre Microsoft et des [fournisseurs de matériel partenaires](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), qui aboutit à la création d’une solution combinant innovation cloud et simplicité de gestion.  Azure Stack étant proposé en tant que système intégré de matériels et de logiciels, vous bénéficiez de la flexibilité et du contrôle adéquats, tout en adoptant l’innovation propre au cloud.  Les systèmes intégrés Azure Stack présentent une taille comprise entre 4 et 12 nœuds et sont pris en charge conjointement par le fournisseur de matériel partenaire et Microsoft.  Utilisez des systèmes intégrés Azure Stack pour autoriser de nouveaux scénarios pour vos charges de travail de production.    

## <a name="azure-stack-development-kit"></a>Kit de développement Azure Stack
Le Kit de développement Microsoft Azure Stack est un déploiement à un seul nœud d’Azure Stack qui vous permet de découvrir et d’évaluer Azure Stack.  Vous pouvez également utiliser le Kit de développement Azure Stack comme environnement de développement, où vous pouvez développer à l’aide d’API et d’outils cohérents avec Azure.  Le Kit de développement Azure Stack n’est pas destiné à être utilisé en tant qu’environnement de production.

Le Kit de développement Azure Stack présente les limitations suivantes :
* Il est associé à un seul fournisseur d’identité Azure Active Directory ou des services de fédération Active Directory (AD FS). Vous pouvez créer plusieurs utilisateurs dans ce répertoire et attribuer des abonnements à chaque utilisateur.
* Avec tous les composants déployés sur une seule machine, les ressources physiques disponibles sont limitées pour les ressources client. Cette configuration n’est pas destinée à l’évaluation des performances ou de la mise à l’échelle.
* Les scénarios de mise en réseau sont limités en raison de l’exigence d’hôte/de carte d’interface réseau unique.  

## <a name="next-steps"></a>Étapes suivantes
[Principaux concepts et fonctionnalités](azure-stack-key-features.md)

[Azure Stack : une extension d’Azure (pdf)](https://azure.microsoft.com/en-us/resources/azure-stack-an-extension-of-azure/)


