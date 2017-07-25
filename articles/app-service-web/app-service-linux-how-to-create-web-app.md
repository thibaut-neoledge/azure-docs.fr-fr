---
title: "Créer une application web Azure en cours d’exécution sur Linux | Microsoft Docs"
description: "Workflow de création d’application web Azure Web App sur Linux."
keywords: azure app service, application web, linux, oss
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: 3a71d10a-a0fe-4d28-af95-03b2860057d5
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 49091d4a85bed23927850f9c0bbc5ea8b6e8c9e1
ms.contentlocale: fr-fr
ms.lasthandoff: 06/07/2017


---
# <a name="create-an-azure-web-app-running-on-linux"></a>Créer une application web Azure en cours d’exécution sur Linux

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


## <a name="use-the-azure-portal-to-create-your-web-app"></a>Utiliser le portail Azure pour créer votre application web
Vous pouvez commencer à créer votre application web sur Linux à partir du [portail Azure](https://portal.azure.com) comme indiqué dans l’image suivante :

![Commencez à créer une application web sur le portail Azure][1]

Ensuite, le **panneau Créer** s’ouvre comme indiqué dans l’image suivante :

![Panneau Créer][2]

1. Donnez un nom à votre application web.
2. Sélectionnez un groupe de ressources existant ou créez-en un. (Consultez les régions disponibles dans la [section Limitations](app-service-linux-intro.md).)
3. Sélectionnez un plan Azure App Service existant ou créez-en un. (Consultez les notes relatives au plan App Service dans la [section Limitations](app-service-linux-intro.md).)
4. Sélectionnez la pile d’applications que vous souhaitez utiliser. Vous pouvez choisir entre plusieurs versions de Node.js, PHP, .Net Core et Ruby.

Une fois que vous avez créé l’application, vous pouvez modifier la pile d’applications dans les paramètres de l’application comme illustré dans l’image suivante :

![Paramètres de l’application][3]

## <a name="deploy-your-web-app"></a>Déployez votre application web
Les **options de déploiement** disponibles dans le portail de gestion vous permettent d’utiliser un référentiel Git local ou GitHub pour déployer votre application. Les autres instructions sont similaires à celles d’une application non-Linux. Vous pouvez suivre les instructions de [déploiement Git local](app-service-deploy-local-git.md) ou [déploiement continu](app-service-continuous-deployment.md) pour déployer votre application.

Vous pouvez également utiliser FTP pour télécharger votre application sur votre site. Vous pouvez obtenir le point de terminaison FTP de votre application web à partir de la section des journaux de diagnostics, comme illustré dans l’image suivante :

![Journaux de diagnostics][4]

## <a name="next-steps"></a>Étapes suivantes
* [Qu’est-ce qu’Azure Web App sur Linux ?](app-service-linux-intro.md)
* [Utiliser la configuration PM2 pour Node.js dans Azure Web App sur Linux](app-service-linux-using-nodejs-pm2.md)
* [Utiliser Ruby dans l’application web Azure App Service sur Linux](app-service-linux-ruby-get-started.md)
* [FAQ de l’application web Azure App Service sur Linux](app-service-linux-faq.md)

<!--Image references-->
[1]: ./media/app-service-linux-how-to-create-a-web-app/top-level-create.png
[2]: ./media/app-service-linux-how-to-create-a-web-app/create-blade.png
[3]: ./media/app-service-linux-how-to-create-a-web-app/application-settings-change-stack.png
[4]: ./media/app-service-linux-how-to-create-a-web-app/diagnostic-logs-ftp.png

