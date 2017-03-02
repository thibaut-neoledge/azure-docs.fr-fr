---
title: Utilisation de Ruby dans Azure App Service Web Apps sur Linux | Microsoft Docs
description: Utilisation de Ruby dans Azure App Service Web Apps sur Linux.
keywords: azure app service, application web, faq, linux, oss, ruby
services: app-service
documentationCenter: 
authors: aelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: aelnably;wesmc
translationtype: Human Translation
ms.sourcegitcommit: bb4c7ea7adfe1326ae8259782b5de2762c8c2bf5
ms.openlocfilehash: 8427034e5229000faf134bcd5b7174b0f7046321
ms.lasthandoff: 02/17/2017


---

# <a name="using-ruby-in-web-apps-on-linux"></a>Utilisation de Ruby dans Web Apps sur Linux #

Lors de la dernière mise à jour de notre serveur principal, nous avons introduit la prise en charge de Ruby v.2.3. En définissant la configuration de votre application web Linux, vous pouvez modifier la pile d’applications.

## <a name="using-the-azure-portal"></a>Utilisation du portail Azure ##

Dans le nouveau menu du [portail](https://portal.azure.com), vous pouvez choisir de créer une application Web sur Linux à partir de l’option Web + Mobile comme indiqué dans l’image suivante :

![Commencez à créer une application web sur le portail Azure][1]

Ensuite, le **panneau Créer** s’ouvre comme indiqué dans l’image suivante :

![Panneau Créer][2]

1. Donnez un nom à votre application web.
2. Sélectionnez un groupe de ressources existant ou créez-en un. (Consultez les régions disponibles dans la [section Limitations](app-service-linux-intro.md).)
3. Sélectionnez un plan Azure App Service existant ou créez-en un. (Consultez les notes relatives au plan App Service dans la [section Limitations](app-service-linux-intro.md).)
4. Choisissez l’application Ruby dans les piles Runtime intégrées.

Une fois votre application web Ruby créée, vous pouvez la déployer à l’aide de Git ou FTP.

## <a name="next-steps"></a>Étapes suivantes
* [Qu’est-ce qu’App Service sur Linux ?](app-service-linux-intro.md)
* [Création d’applications Web dans App Service sur Linux](app-service-linux-how-to-create-a-web-app.md)
* [Déploiement Git local vers Azure App Service](app-service-deploy-local-git.md)
* [FAQ Azure App Service Web Apps sous Linux](app-service-linux-faq.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-ruby/New-Linux.png
[2]: ./media/app-service-linux-using-ruby/Ruby-UX.png
