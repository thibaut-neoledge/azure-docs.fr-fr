---
title: "Déploiement continu Docker Hub avec l’application web Azure sur Linux | Microsoft Docs"
description: "Configuration du déploiement continu Docker Hub avec l’application web Azure sur Linux."
keywords: azure app service, linux, oss
services: app-service
documentationcenter: 
author: ahmedelnably
manager: erikre
editor: 
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 68e11f07520bbe8da6812a34fe95708fc9e932ac
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="docker-hub-continuous-deployment-with-azure-web-app-on-linux"></a>Déploiement continu Docker Hub avec l’application web Azure sur Linux

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

Dans ce didacticiel, vous allez configurer le déploiement continu d’une image de conteneur personnalisée à partir de [Docker Hub](https://hub.docker.com).

## <a name="step-1---log-in-to-azure"></a>Étape 1 : Se connecter à Azure

Connectez-vous au portail Azure à l’adresse http://portal.azure.com.

## <a name="step-2---enable-docker-hub-continuous-deployment"></a>Étape 2 : activer le déploiement continu Docker Hub

Dans le **[portail Azure](https://portal.azure.com/)**, cliquez sur l’option **App Service** sur le côté gauche de la page.

Cliquez sur le nom de l’application pour laquelle vous souhaitez configurer le déploiement continu Docker Hub.

Dans **Paramètres de l’application**, ajoutez un paramètre d’application nommé `DOCKER_ENABLE_CI` avec la valeur `true`.

![insérer une image de Paramètres de l’application](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="step-3---add-a-web-hook-to-docker-hub"></a>Étape 3 : ajouter un webhook à Docker Hub

Dans votre page Docker Hub, cliquez sur **Webhooks**, puis sur **Créer un Webhook**.

![insérer une image de l’ajout du webhook 1](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

Pour l’URL du Webhook, vous devez disposer du point de terminaison suivant : `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

![insérer une image de l’ajout du webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

Vous pouvez obtenir votre `publishingusername` et `publishingpwd` en téléchargeant le profil de publication de l’application web à l’aide du portail Azure.

![insérer une image de l’ajout du webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

Lorsque l’image est mise à jour, l’application web est mise à jour automatiquement avec la nouvelle image.

## <a name="next-steps"></a>Étapes suivantes
* [Qu’est-ce que l’application web Azure sur Linux ?](./app-service-linux-intro.md)
* [Création d’applications dans l’application web Azure sur Linux](./app-service-linux-how-to-create-web-app.md)
* [Utilisation de la configuration PM2 pour Node.js dans l’application web Azure sur Linux](app-service-linux-using-nodejs-pm2.md)
* [Utilisation de .NET Core dans l’application web Azure sur Linux](app-service-linux-using-dotnetcore.md)
* [Utilisation de Ruby dans l’application web Azure sur Linux](app-service-linux-ruby-get-started.md)
* [Comment utiliser une image Docker personnalisée pour l’application web Azure sur Linux](./app-service-linux-using-custom-docker-image.md)
* [FAQ de l’application web Azure App Service sur Linux](./app-service-linux-faq.md) 




