---
title: "Créer une application web HTML statique dans Azure en 5 minutes | Microsoft Docs"
description: "Découvrez la facilité avec laquelle vous pouvez exécuter des applications web dans App Service en déployant un exemple d’application."
services: app-service\web
documentationcenter: 
author: rick-anderson
manager: wpickett
editor: 
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/08/2017
ms.author: riande
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 895906e1ab4bc50093ed3b18f043c3dd515ca054
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---
# <a name="create-a-static-html-web-app-in-azure-in-five-minutes"></a>Créer une application web HTML statique dans Azure en 5 minutes

Ce guide de démarrage rapide explique pas à pas comment déployer un site HTML + CSS de base dans Azure. Vous allez exécuter l’application à l’aide d’un [plan Azure App Service](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview), puis y créer une application web en utilisant [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli). Vous utilisez Git pour déployer l’application dans Azure. Cela prend environ cinq minutes pour compléter le didacticiel, une fois que les composants requis sont installés.

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

## <a name="prerequisites"></a>Composants requis

Avant de créer cet exemple, téléchargez et installez les composants suivants :

- [Git](https://git-scm.com/)
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Téléchargez l’exemple

Dans une fenêtre de terminal, clonez le référentiel de l’exemple d’application sur votre ordinateur local :

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="view-the-html"></a>Affichez le code HTML

Naviguez vers le répertoire qui contient l’exemple HTML. Ouvrez le fichier *index.html* dans votre navigateur.

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser.png)

[!INCLUDE [login-to-azure](../../includes/login-to-azure.md)] 
[!INCLUDE [configure-deployment-user](../../includes/configure-deployment-user.md)] 

[!INCLUDE [app-service-web-quickstart1](../../includes/app-service-web-quickstart1.md)] 

Créer une [application web](app-service-web-overview.md) dans le plan App Service `quickStartPlan`. L’application web offre un espace d’hébergement pour votre code, et fournit une URL pour afficher l’application déployée.

[!INCLUDE [app-service-web-quickstart2](../../includes/app-service-web-quickstart2.md)] 

La page s’exécute comme une application web Azure App Service :

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

## <a name="update-and-redeploy-the-app"></a>Mise à jour et redéploiement de l’application

Ouvrez le fichier *index.html* . Modifiez le balisage. Par exemple, remplacez `Hello world!` par `Hello Azure!`

Validez vos modifications dans Git, puis envoyez les modifications de code à Azure.

```bash
git commit -am "updated HTML"
git push azure master
```

Une fois le déploiement terminé, actualisez votre navigateur afin de voir les modifications.

[!INCLUDE [manage-azure-web-app](../../includes/manage-azure-web-app.md)]


[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

- Parcourez des exemples de [scripts CLI de Web Apps](app-service-cli-samples.md).
- Apprenez à [mapper un nom de domaine personnalisé](app-service-web-tutorial-custom-domain.md), comme contoso.com, à une [application App Service](app-service-web-tutorial-custom-domain.md).
