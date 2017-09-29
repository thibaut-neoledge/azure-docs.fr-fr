---
title: "Vue d’ensemble d’App Service : Azure Stack | Microsoft Docs"
description: "Vue d’ensemble d’App Service sur Azure Stack"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/3/2017
ms.author: anwestg
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: 13928744e7d2fc145662c2a0d5c26d512cf02150
ms.contentlocale: fr-fr
ms.lasthandoff: 09/15/2017

---

# <a name="app-service-on-azure-stack-overview"></a>Vue d’ensemble d’App Service sur Azure Stack

Azure App Service sur Azure Stack est l’offre Azure intégrée à Azure Stack. Le programme d’installation App Service sur Azure Stack crée l’ensemble suivant des instances de rôles :

*  Controller
*  Gestion (deux instances sont créées)
*  FrontEnd
*  Éditeur
*  Worker (en mode Partagé)

En outre, le programme d’installation App Service sur Azure Stack crée un serveur de fichiers.
    
## <a name="whats-new-in-the-first-release-candidate-of-app-service-on-azure-stack"></a>Quelles sont les nouveautés dans la première version candidate App Service sur Azure Stack ?
![Portail App Service sur Azure Stack][1]

La première version candidate App Service sur Azure Stack s’appuie sur la troisième préversion et apporte des améliorations et des nouvelles fonctionnalités :

* Environnements Azure Functions dans Azure Stack basés sur AD FS 
* Prise en charge de l’authentification unique pour le portail Functions et des outils de développement avancés (Kudu)
* Prise en charge de Java pour les applications web, mobiles et d’API
* Gestion des niveaux Worker à l’échelle du groupe de machines virtuelles identiques pour améliorer les fonctionnalités de montée en puissance pour les administrateurs de service
* Localisation de l’expérience administrateur
* Stabilité accrue du service
* Mises à jour de l’expérience du portail du locataire et mises à jour du processus d’installation

## <a name="limitations-of-the-technical-preview"></a>Limitations de la préversion technique

Il n’existe aucune prise en charge des préversions App Service sur Azure Stack, même si nous surveillons le Forum MSDN Azure Stack. Ne placez aucune charge de travail de production dans cette préversion. Il n’existe également aucune mise à niveau entre les préversions App Service sur Azure Stack. L’objectif principal de ces préversions est de montrer ce que nous fournissons et d’obtenir des commentaires. 

## <a name="what-is-an-app-service-plan"></a>Qu’est-ce qu’un plan App Service ?

Le fournisseur de ressources App Service utilise le même code que celui utilisé par Azure App Service. Par conséquent, il peut être utile de décrire certains concepts communs. Dans App Service, le conteneur de tarification pour les applications est appelé le plan App Service. Il représente l’ensemble des machines virtuelles dédiées utilisées pour contenir vos applications. Vous pouvez avoir plusieurs plans App Service dans un abonnement donné. 

Dans Azure, il existe des Workers partagés et dédiés. Un Worker partagé prend en charge l’hébergement d’applications mutualisées à densité élevée et il n'existe qu’un seul ensemble de Workers partagés. Les serveurs dédiés sont utilisés par un seul locataire et se présentent dans trois tailles : petit, moyen et grand. Les besoins des clients locaux ne peuvent pas toujours être décrits à l’aide de ces termes. Dans App Service sur Azure Stack, les administrateurs du fournisseur de ressources peuvent définir les niveaux Worker qu'ils souhaitent rendre disponibles. Les administrateurs peuvent définir plusieurs jeux de Workers partagés ou des jeux différents de Workers dédiés selon leurs besoins d’hébergements uniques. À l’aide de ces définitions de niveau Worker, ils peuvent ensuite définir leurs propres références de tarification.

## <a name="portal-features"></a>Fonctionnalités du portail

App Service sur Azure Stack utilise la même interface utilisateur que celle utilisée par Azure App Service, ce qui est également le cas du serveur principal. Certaines fonctionnalités sont désactivées et ne sont pas fonctionnelles dans Azure Stack. Les exigences ou les services spécifiques à Azure dont ces fonctionnalités ont besoin ne sont pas encore disponibles dans Azure Stack. 

## <a name="next-steps"></a>Étapes suivantes

- [Avant de commencer avec App Service sur Azure Stack](azure-stack-app-service-before-you-get-started.md)
- [Installer le fournisseur de ressources App Service](azure-stack-app-service-deploy.md)

Vous pouvez également tester d’autres [services PaaS](azure-stack-tools-paas-services.md), comme le [fournisseur de ressources SQL Server](azure-stack-sql-resource-provider-deploy.md) et le [fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md).

<!--Image references-->
[1]: ./media/azure-stack-app-service-overview/AppService_Portal.png

