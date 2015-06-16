<properties 
	pageTitle="Créer une application API ASP.NET dans Azure App Service" 
	description="Découvrez comment créer une application API ASP.NET dans Azure App Service avec Visual Studio 2013" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="bradyg;tarcher"/>

# Créer une application API ASP.NET dans Azure App Service

## Vue d'ensemble

Ce didacticiel montre comment créer un projet d’API web ASP.NET et le configurer pour un déploiement dans le cloud en tant qu’[application API](app-service-api-apps-why-best-platform.md) dans [Azure App Service](../app-service/app-service-value-prop-what-is.md). Si vous avez un projet d’API web que vous souhaitez convertir en application API, reportez-vous à l’article [Configurer un projet d’API web comme application API](./app-service-dotnet-create-api-app-visual-studio). Les didacticiels suivants de la série montrent comment [déployer](app-service-dotnet-deploy-api-app.md) et [déboguer](../app-service-dotnet-remotely-debug-api-app.md) le projet d’application API que vous créez dans ce didacticiel.

Pour plus d’informations sur les applications API, consultez la rubrique [Que sont les applications API](app-service-api-apps-why-best-platform.md).

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

Ce didacticiel requiert la version 2.5.1 ou ultérieure du Kit de développement logiciel (SDK) Azure pour .NET.

## Créer un projet d’application API 

Cette section présente l’utilisation du modèle de projet d’application API Azure pour créer une application API. Pour savoir comment configurer un projet d’API web existant comme application API, passez à la [section suivante](#configure-a-web-api-project-as-an-api-app).

1. Ouvrez Visual Studio 2013.

2. Sélectionnez **Fichier > Nouveau projet**.

3. Sélectionnez le modèle **Application web ASP.NET**.

4. Nommez ce projet *ContactsList*

	![](./media/app-service-dotnet-create-api-app/01-filenew-v3.png)

5. Cliquez sur **OK**.

6. Dans la boîte de dialogue **Nouveau projet ASP.NET**, sélectionnez le modèle de projet **Application API Azure**.

	![](./media/app-service-dotnet-create-api-app/02-api-app-template-v3.png)

7. Cliquez sur **OK** pour créer le projet.

Visual Studio crée un projet d’API web configuré pour le déploiement sous forme d’application API.

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## Étapes suivantes

Votre application API est maintenant prête à être déployée et vous pouvez pour cela suivre le didacticiel [Déployer une application API](app-service-dotnet-deploy-api-app.md).

<!--HONumber=54--> 