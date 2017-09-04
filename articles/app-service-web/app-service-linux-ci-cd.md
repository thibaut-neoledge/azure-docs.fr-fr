---
title: "Déploiement continu avec l’application web Azure sur Linux | Microsoft Docs"
description: "Configuration du déploiement continu Docker Hub avec l’application web Azure sur Linux."
keywords: azure app service, linux, oss, acr
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
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: f8f7d51003f8a55b7f51e8cc2cea838e8e5a6196
ms.contentlocale: fr-fr
ms.lasthandoff: 08/28/2017

---
# <a name="continuous-deployment-with-azure-web-app-on-linux"></a>Déploiement continu avec l’application web Azure sur Linux

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

Dans ce didacticiel, vous allez configurer le déploiement continu d’une image de conteneur personnalisée à partir des référentiels [Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/) gérés ou du [hub Docker](https://hub.docker.com).

## <a name="step-1---sign-in-to-azure"></a>Étape 1 : se connecter à Azure

Connectez-vous au portail Azure à l’adresse http://portal.azure.com.

## <a name="step-2---enable-container-continuous-deployment-feature"></a>Étape 2 : Activer la fonctionnalité de déploiement continu de conteneur

Vous pouvez activer la fonctionnalité de déploiement continu avec [l’interface de ligne de commande Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) et en exécutant la commande suivante

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
``` 

Dans le **[portail Azure](https://portal.azure.com/)**, cliquez sur l’option **App Service** sur le côté gauche de la page.

Cliquez sur le nom de l’application pour laquelle vous souhaitez configurer le déploiement continu Docker Hub.

Dans **Paramètres de l’application**, ajoutez un paramètre d’application nommé `DOCKER_ENABLE_CI` avec la valeur `true`.

![insérer une image de Paramètres de l’application](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="step-3---prepare-webhook-url"></a>Étape 3 : préparer l’URL du webhook

Vous pouvez obtenir l’URL du Webhook avec [l’interface de ligne de commande Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) et en exécutant la commande suivante

```azurecli-interactive
az webapp deployment container -n sname1 -g rgname -e true --show-cd-url
``` 

Pour l’URL du Webhook, vous devez disposer du point de terminaison suivant : `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Vous pouvez obtenir votre `publishingusername` et `publishingpwd` en téléchargeant le profil de publication de l’application web à l’aide du portail Azure.

![insérer une image de l’ajout du webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="step-4---add-a-web-hook"></a>Étape 4 : ajouter un webhook

### <a name="azure-container-registry"></a>Azure Container Registry

Dans le panneau de portail de votre registre, cliquez sur **Webhooks** et créez un nouveau webhook en cliquant sur **Ajouter**. Dans le panneau **Create webhook** (Créer un webhook), donnez un nom à votre webhook. Pour l’URI du webhook, vous devez fournir l’URL obtenue à l’**étape 3**.

Veillez à définir l’étendue comme référentiel qui contient votre image de conteneur.

![insérer une image de webhook](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

Lorsque l’image est mise à jour, l’application web est mise à jour automatiquement avec la nouvelle image.

### <a name="docker-hub"></a>Hub Docker

Dans votre page Docker Hub, cliquez sur **Webhooks**, puis sur **Créer un Webhook**.

![insérer une image de l’ajout du webhook 1](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

Pour l’URL du webhook, vous devez fournir l’URL obtenue à l’**étape 3**.

![insérer une image de l’ajout du webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

Lorsque l’image est mise à jour, l’application web est mise à jour automatiquement avec la nouvelle image.

## <a name="next-steps"></a>Étapes suivantes
* [Qu’est-ce qu’Azure Web App sur Linux ?](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/)
* [Utiliser la configuration PM2 pour Node.js dans Azure Web App sur Linux](app-service-linux-using-nodejs-pm2.md)
* [Utilisation de .NET Core dans l’application web Azure sur Linux](app-service-linux-using-dotnetcore.md)
* [Utilisation de Ruby dans l’application web Azure sur Linux](app-service-linux-ruby-get-started.md)
* [Comment utiliser une image Docker personnalisée pour l’application web Azure sur Linux](./app-service-linux-using-custom-docker-image.md)
* [FAQ de Web App d’Azure App Service sur Linux](./app-service-linux-faq.md) 
* [Gérer l’application web sur Linux à l’aide d’Azure CLI 2.0](./app-service-linux-cli.md)




