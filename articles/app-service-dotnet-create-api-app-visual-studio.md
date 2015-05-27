<properties 
	pageTitle="Configurer un projet d’API web comme application API" 
	description="Découvrez comment configurer un projet d’API web comme  application API à l’aide de Visual Studio 2013" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
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

# Configurer un projet d’API web comme application API

## Vue d'ensemble

Ce didacticiel montre comment prendre un projet d’API web existant et le configurer pour le déployer comme [application API](app-service-api-apps-why-best-platform.md) dans [Azure App Service](app-service-value-prop-what-is.md). Les didacticiels suivants de la série montrent comment [déployer](app-service-dotnet-deploy-api-app.md) et [déboguer](app-service-dotnet-remotely-debug-api-app.md) le projet d’application API que vous créez dans ce didacticiel.

Pour plus d’informations sur les applications API, consultez la rubrique [Que sont les applications API](app-service-api-apps-why-best-platform.md).

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

Ce didacticiel requiert la version 2.5.1 ou ultérieure du Kit de développement logiciel (SDK) Azure pour .NET.

## Configurer un projet d’API web 

Cette section présente la configuration d’un projet d’API web existant comme application API. Vous commencez avec le modèle de projet d’API web pour créer un projet d’API web, puis vous le configurez comme une application API.

1. Ouvrez Visual Studio 2013.
2. Sélectionnez **Fichier > Nouveau projet**. 
3. Sélectionnez le modèle **Application web ASP.NET**.  
4. Nommez ce projet *ContactsList*

	![](./media/app-service-dotnet-create-api-app-visual-studio/01-filenew-v3.png)

5. Cliquez sur **OK**.

6. Dans la boîte de dialogue **Nouveau projet ASP.NET**, sélectionnez le modèle de projet **Vide**.

7. Activez la case à cocher **API web**.

8. Désactivez l’option **Héberger sur le cloud**.

	![](./media/app-service-dotnet-create-api-app-visual-studio/webapinewproj.png)

9. Cliquez sur **OK** pour créer le projet.

	![](./media/app-service-dotnet-create-api-app-visual-studio/sewebapi.png)

10. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet (pas la solution), puis sélectionnez **Ajouter > Kit de développement logiciel (SDK) d’application API Azure**.

	![](./media/app-service-dotnet-create-api-app-visual-studio/addapiappsdk.png)

11. Dans la boîte de dialogue **Choisir la source des métadonnées d’application API**, cliquez sur **Génération automatique de métadonnées**.

	![](./media/app-service-dotnet-create-api-app-visual-studio/chooseswagger.png)

	Ce choix active l'interface utilisateur Swagger dynamique, que vous verrez plus tard dans le didacticiel. Si vous choisissez de télécharger un fichier de métadonnées Swagger, il est enregistré avec le nom de fichier *apiDefinition.swagger.json*, comme expliqué dans la section suivante.

12. Cliquez sur **OK**.
 
	À ce stade, Visual Studio installe les packages NuGet de l’application API et ajoute les métadonnées d’application API au projet d’API web.

[AZURE.INCLUDE [app-service-api-review-metadata](../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../includes/app-service-api-direct-deploy-metadata.md)]

## Étapes suivantes

Votre application API est maintenant prête à être déployée et vous pouvez pour cela suivre le didacticiel [Déployer une application API](app-service-dotnet-deploy-api-app.md).

<!--HONumber=54-->