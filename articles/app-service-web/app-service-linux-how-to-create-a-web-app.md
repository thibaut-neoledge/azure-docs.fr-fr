---
title: "Création d’une application web avec App Service sur Linux | Microsoft Docs"
description: "Workflow de création d’application web d’App Service sur Linux."
keywords: azure app service, application web, linux, oss
services: app-service
documentationcenter: 
author: naziml
manager: wpickett
editor: 
ms.assetid: 3a71d10a-a0fe-4d28-af95-03b2860057d5
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: naziml
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8d87315ec97631c7ad3f0163aeaa0abde11b7416


---
# <a name="create-a-web-app-with-app-service-on-linux"></a>Créer une application web avec App Service sur Linux
## <a name="use-the-azure-portal-to-create-your-web-app"></a>Utiliser le portail Azure pour créer votre application web
Vous pouvez commencer à créer votre application web sur Linux à partir du [portail Azure](https://portal.azure.com) comme indiqué dans l’image suivante :

![Commencez à créer une application web sur le portail Azure][1]

Ensuite, le **panneau Créer** s’ouvre comme indiqué dans l’image suivante :

![Panneau Créer][2]

1. Donnez un nom à votre application web.
2. Sélectionnez un groupe de ressources existant ou créez-en un. (Consultez les régions disponibles dans la [section Limitations](app-service-linux-intro.md).)
3. Sélectionnez un plan Azure App Service existant ou créez-en un. (Consultez les notes relatives au plan App Service dans la [section Limitations](app-service-linux-intro.md).)
4. Sélectionnez la pile d’applications que vous souhaitez utiliser. Vous pouvez choisir entre plusieurs versions de Node.js et PHP.

Une fois que vous avez créé l’application, vous pouvez modifier la pile d’applications dans les paramètres de l’application comme illustré dans l’image suivante :

![Paramètres de l’application][3]

## <a name="deploy-your-web-app"></a>Déployez votre application web
Les **options de déploiement** disponibles dans le portail de gestion vous donnent la possibilité d’utiliser un Git local ou un référentiel GitHub pour déployer votre application. Le reste de la procédure est identique à celle suivie pour une application web non Linux. Vous pouvez consulter ces instructions dans notre article sur le [déploiement Git local](app-service-deploy-local-git.md) ou sur le [déploiement continu](app-service-continuous-deployment.md) pour GitHub.

Vous pouvez également utiliser FTP pour télécharger votre application sur votre site. Vous pouvez obtenir le point de terminaison FTP de votre application web à partir de la section des journaux de diagnostics, comme illustré dans l’image suivante :

![Journaux de diagnostics][4]

## <a name="next-steps"></a>Étapes suivantes
* [Qu’est-ce qu’App Service sur Linux ?](app-service-linux-intro.md)
* [Utilisation de la configuration PM2 pour Node.js dans les applications Web sur Linux](app-service-linux-using-nodejs-pm2.md)

<!--Image references-->
[1]: ./media/app-service-linux-how-to-create-a-web-app/top-level-create.png
[2]: ./media/app-service-linux-how-to-create-a-web-app/create-blade.png
[3]: ./media/app-service-linux-how-to-create-a-web-app/application-settings-change-stack.png
[4]: ./media/app-service-linux-how-to-create-a-web-app/diagnostic-logs-ftp.png



<!--HONumber=Nov16_HO3-->


