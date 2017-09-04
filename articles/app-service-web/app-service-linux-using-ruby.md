---
title: "Utilisation de Ruby dans l’application web Azure App Service sur Linux | Microsoft Docs"
description: "Utilisation de Ruby dans l’application web Azure App Service sur Linux."
keywords: azure app service, application web, faq, linux, oss, ruby
services: app-service
documentationCenter: 
author: ahmedelnably
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
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 56105d1bc153e552e12c0c408c8f6075e4eff9d0
ms.contentlocale: fr-fr
ms.lasthandoff: 08/28/2017

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

Pour plus d’informations sur la création d’une application Ruby, consultez le [guide de prise en main](app-service-linux-ruby-get-started.md)

## <a name="next-steps"></a>Étapes suivantes
* [Qu’est-ce que Web App sur Linux ?](app-service-linux-intro.md)
* [Déploiement Git local vers Azure App Service](app-service-deploy-local-git.md)
* [FAQ de l’application web Azure App Service sur Linux](app-service-linux-faq.md)
* [Créer une application Ruby avec Azure Web App sur Linux](app-service-linux-ruby-get-started.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-ruby/New-Linux.png
[2]: ./media/app-service-linux-using-ruby/Ruby-UX.png
