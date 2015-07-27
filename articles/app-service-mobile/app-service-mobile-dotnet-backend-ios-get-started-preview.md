<properties
	pageTitle="Prise en main d'Azure App Service pour les applications iOS mobiles"
	description="Suivez ce didacticiel pour commencer à utiliser Azure App Service pour le développement d'applications iOS."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="ysxu"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="get-started-article"
	ms.date="06/18/2015"
	ms.author="yuaxu"/>

# <a name="getting-started"> </a>Création d'une application iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]

Ce didacticiel montre comment créer une application iOS avec Azure App Service Mobile Apps. Vous allez créer un nouveau backend d'application mobile et une application _To do list_ simple qui stocke les données de l'application. Ce didacticiel utilise .NET et Visual Studio pour la logique côté serveur.

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10 applications mobiles gratuites que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE]Si vous voulez prendre en main Azure App Service avant de créer un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), où vous pouvez créer immédiatement une première application mobile temporaire dans App Service. Aucune carte de crédit n'est requise ; vous ne prenez aucun engagement.

## <a name="create-new-service"> </a>Création d'un backend d'application mobile

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Création d'une application iOS

À présent que vous avez créé votre backend d'application mobile, vous pouvez suivre un démarrage rapide facile dans la version préliminaire du portail Azure pour créer une application ou modifier une application existante à connecter à votre backend d'application mobile.

1. Dans le portail Azure, cliquez sur **Application mobile**, puis sur le backend d'application mobile que vous venez de créer.

2. En haut du panneau, cliquez sur Ajouter un client et développez iOS.

	![][6]

	Cette opération affiche les étapes permettant de créer une application iOS connectée à votre backend d'application mobile.

3. Si ce n'est déjà fait, téléchargez et installez <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> sur votre ordinateur local ou sur votre machine virtuelle.

4. Téléchargez et installez [Xcode] v4.4 ou une version plus récente et Xamarin Studio. Vous pouvez également utiliser Xamarin pour Visual Studio.

5. Sous **Télécharger et publier votre service dans le cloud**, cliquez sur **Télécharger**.

 Cette opération lance le téléchargement d'une solution contenant les projets du backend d'application mobile et de l'exemple d'application _To do list_ connecté à votre backend d'application mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

6. Téléchargez votre profil de publication, enregistrez le fichier téléchargé sur votre ordinateur local et notez où vous l'enregistrez.

## Test de l'application mobile

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Publication de votre application mobile

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Exécution de l'application iOS

[AZURE.INCLUDE [app-service-mobile-ios-run-app-preview](../../includes/app-service-mobile-ios-run-app-preview.md)]



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/app-service-mobile-dotnet-backend-ios-get-started-preview/ios-quickstart.png

[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png

[Management Portal]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
 

<!---HONumber=July15_HO3-->