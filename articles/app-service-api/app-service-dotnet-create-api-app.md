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
	ms.topic="hero-article" 
	ms.date="05/19/2015"
	ms.author="bradyg;tarcher"/>

# Créer une application API ASP.NET dans Azure App Service

> [AZURE.SELECTOR]
- [Visual Studio 2013](app-service-dotnet-create-api-app.md)
- [Visual Studio 2015 RC](app-service-dotnet-create-api-app-vs2015.md)
- [Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)

## Vue d'ensemble

Ce didacticiel montre comment créer un projet d’API web ASP.NET en utilisant un modèle Visual Studio 2013 qui le configure pour un déploiement dans le cloud en tant qu’[application API](app-service-api-apps-why-best-platform.md) dans [Azure App Service](../app-service/app-service-value-prop-what-is.md). Pour plus d'informations sur la configuration d'un projet d'API web existant pour le déploiement en tant qu'application API, consultez la rubrique [Configurer un projet d'API web comme une application API](app-service-dotnet-create-api-app-visual-studio.md).

Les didacticiels suivants de la série montrent comment [déployer](app-service-dotnet-deploy-api-app.md) et [déboguer](../app-service-dotnet-remotely-debug-api-app.md) le projet d’application API que vous créez dans ce didacticiel.

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

Ce didacticiel requiert la version 2.5.1 ou ultérieure du Kit de développement logiciel (SDK) Azure pour .NET.

## Créer un projet d’application API

Lorsque les instructions vous invitent à entrer un nom pour le projet, entrez *ContactsList*.

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## Étapes suivantes

Votre application API est maintenant prête à être déployée et vous pouvez pour cela suivre le didacticiel [Déployer une application API](app-service-dotnet-deploy-api-app.md).
 

<!---HONumber=August15_HO6-->