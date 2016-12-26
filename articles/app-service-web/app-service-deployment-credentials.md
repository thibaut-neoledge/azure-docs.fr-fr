---
title: "Informations d’identification du déploiement d’Azure App Service | Microsoft Docs"
description: "Apprenez à utiliser les informations d’identification du déploiement d’Azure App Service."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: wpickett
editor: mollybos
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/21/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 73753bb96d2c9680ed5d5bdf5eb3780e371c08d3
ms.openlocfilehash: 53f971624c2c7a7f64eed257aeb0c402461cc7ba


---
# <a name="azure-app-service-deployment-credentials"></a>Informations d’identification du déploiement d’Azure App Service
La plateforme [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) prend en charge deux types d’informations d’identification pour le déploiement de contenu.
* Informations d’identification de déploiement : informations d’identification de portée utilisateur
* Profil de publication : informations d’identification de portée application 

## <a name="a-nameuserscopeauser-scoped-credentials"></a><a name="userscope"></a>Informations d’identification de portée utilisateur
Les informations d’identification de portée utilisateur sont créées par l’utilisateur d’Azure et mappent directement sur un compte Microsoft plutôt que sur toute application App Service spécifique. Les informations d’identification de déploiement de portée utilisateur peuvent être définies ou réinitialisées à partir du [portail Azure](https://portal.azure.com) où chaque application App Service possède un point d’entrée d’édition sous **DÉPLOIEMENT DE L’APPLICATION > Informations d’identification du déploiement**. Quel que soit le point d’entrée, les modifications apportées à ces informations d’identification de portée utilisateur s’appliquent à l’intégralité du compte Microsoft. Ces informations d’identification sont fréquemment utilisées pour le déploiement FTP et Git.

![Informations d’identification du déploiement d’Azure App Service](./media/app-service-deployment-credentials/deployment_credentials.png)
 
Lors de la délégation d’accès aux ressources Azure au moyen du contrôle d’accès en fonction du rôle (RBAC) ou des autorisations de coadministrateur, chaque utilisateur Azure recevant l’accès peut utiliser ses propres informations d’identification de portée utilisateur jusqu’à la révocation de l’accès. Ces informations d’identification de déploiement ne doivent pas être partagées avec d’autres utilisateurs Azure.

## <a name="a-nameappscopeaapp-scoped-credentials"></a><a name="appscope"></a>Informations d’identification de portée application
Les informations d’identification de portée application sont automatiquement créées par la plateforme App Service. Elles sont stockées dans le profil de publication XML de chaque application App Service. Le profil de publication est disponible dans le [portail Azure](https://portal.azure.com) par le biais de l’action **Obtenir le profil de publication** action dans le panneau **Vue d’ensemble** de l’application. Ces informations d’identification sont fréquemment utilisées pour le déploiement basé sur WebDeploy. Elles peuvent également être utilisées pour le déploiement FTP ou Git. Visual Studio, qui est un point d’entrée pour le déploiement basé sur WebDeploy, peut analyser le profil de publication pour l’authentification.

![Profil de publication d’Azure App Service](./media/app-service-deployment-credentials/publish_profile.png)

Lors de la délégation d’accès aux ressources Azure au moyen du contrôle d’accès en fonction du rôle (RBAC) ou des autorisations de coadministrateur, chaque utilisateur Azure recevant l’accès peut télécharger le même profil de publication spécifique à l’application. Le profil de publication peut être réinitialisé à tout moment à partir du panneau **Vue d’ensemble** de l’application du portail Azure. La réinitialisation des informations d’identification de portée application peut être judicieuse après la révocation de l’accès délégué.



<!--HONumber=Nov16_HO4-->


