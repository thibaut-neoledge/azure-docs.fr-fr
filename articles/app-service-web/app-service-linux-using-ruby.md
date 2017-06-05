---
title: "Utilisation de Ruby dans l’application web Azure App Service sur Linux | Microsoft Docs"
description: "Utilisation de Ruby dans l’application web Azure App Service sur Linux."
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5aeef6f31dacb1b27c605d39a35a81bd0211e06a
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---

# <a name="using-ruby-in-web-app-on-linux"></a>Utilisation de Ruby dans Web App sur Linux #

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
* [Qu’est-ce que Web App sur Linux ?](app-service-linux-intro.md)
* [Création d’applications web dans Web App sur Linux](app-service-linux-how-to-create-web-app.md)
* [Déploiement Git local vers Azure App Service](app-service-deploy-local-git.md)
* [FAQ de l’application web Azure App Service sur Linux](app-service-linux-faq.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-ruby/New-Linux.png
[2]: ./media/app-service-linux-using-ruby/Ruby-UX.png
