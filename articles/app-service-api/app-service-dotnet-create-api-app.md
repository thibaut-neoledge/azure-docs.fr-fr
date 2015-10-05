<properties
	pageTitle="Créer une application API ASP.NET dans Azure App Service | Microsoft Azure"
	description="Découvrez comment créer une application API ASP.NET dans Azure App Service avec Visual Studio 2013."
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
	ms.date="08/14/2015"
	ms.author="tdykstra"/>

# Créer une application API ASP.NET dans Azure App Service

> [AZURE.SELECTOR]
- [.NET - Visual Studio 2015](app-service-dotnet-create-api-app.md)
- [.NET - Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)
- [Node.js](app-service-api-nodejs-api-app.md)
- [Java](app-service-api-java-api-app.md)

## Vue d’ensemble

Ce didacticiel montre comment créer un projet d’API web ASP.NET configuré pour un déploiement dans le cloud en tant qu’[application API dans Azure App Service](app-service-api-apps-why-best-platform.md). Pour plus d'informations sur la configuration d'un projet d'API web existant pour le déploiement en tant qu'application API, consultez la rubrique [Configurer un projet d'API web comme une application API](app-service-dotnet-create-api-app-visual-studio.md).

Les didacticiels suivants de la série montrent comment [déployer](app-service-dotnet-deploy-api-app.md) et [déboguer](../app-service-dotnet-remotely-debug-api-app.md) le projet d’application API que vous créez dans ce didacticiel.

[AZURE.INCLUDE [install-sdk-2015-2013](../../includes/install-sdk-2015-2013.md)]

Ce didacticiel requiert la version 2.6 ou ultérieure du Kit de développement logiciel (SDK) Azure pour .NET.

## Créer un projet d’application API

Lorsque les instructions vous invitent à saisir un nom pour le projet, saisissez **ContactsList**.

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## Étapes suivantes

Votre application API est maintenant prête à être déployée et vous pouvez pour cela suivre le didacticiel [Déployer une application API](app-service-dotnet-deploy-api-app.md).

<!---HONumber=Sept15_HO4-->