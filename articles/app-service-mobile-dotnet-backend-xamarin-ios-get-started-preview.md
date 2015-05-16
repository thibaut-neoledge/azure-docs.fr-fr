<properties
	pageTitle="Prise en main de Mobile Apps dans Xamarin iOS"
	description="Commencez à utiliser Xamarin iOS pour générer une application Azure Mobile App à l&#39;aide d&#39;Azure App Service."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/24/2015"
	ms.author="chrande"/>


# <a name="getting-started"> </a>Création d'une application Xamarin iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../includes/app-service-mobile-selector-get-started-preview.md)]

Ce didacticiel montre comment ajouter un service backend cloud à une application Xamarin iOS à l'aide d'Azure Mobile App. Dans ce didacticiel, vous allez créer un service .NET et une application _To do list_ simple qui stocke les données d'application sur le backend .NET.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10 applications mobiles gratuites que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE]Si vous voulez prendre en main Azure App Service avant de créer un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), où vous pouvez créer immédiatement une première application mobile temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Création d'un backend d'application mobile

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Création d'une application Xamarin iOS

Une fois que vous avez créé votre backend d'application mobile, vous pouvez suivre un démarrage rapide facile dans le portail Azure pour créer une application ou modifier une application existante à connecter à votre backend d'application mobile.

Dans cette section, vous allez télécharger une nouvelle application Xamarin iOS et un projet de service pour votre backend d'application mobile.

1. Dans le portail Azure, cliquez sur **Application mobile**, puis sur le backend d'application mobile que vous venez de créer.

2. En haut du panneau, cliquez sur **Ajouter un client** et développez **Xamarin iOS**.

	![][6]

	Cette opération affiche les trois étapes faciles permettant de créer une application Xamarin iOS connectée à votre backend d'application mobile.

3. Si ce n'est déjà fait, téléchargez et installez <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> sur votre ordinateur local ou sur votre machine virtuelle.

4. Téléchargez et installez [Xcode] v4.4 ou une version plus récente et [Xamarin Studio]. Vous pouvez également utiliser Xamarin pour Visual Studio.

5. Sous **Télécharger et publier votre service dans le cloud**, cliquez sur **Télécharger**.

 Cette opération lance le téléchargement d'une solution contenant les projets du backend d'application mobile et de l'exemple d'application _To do list_ connecté à votre backend d'application mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

6. Téléchargez votre profil de publication, enregistrez le fichier téléchargé sur votre ordinateur local et notez où vous l'enregistrez.

## Test du backend d'application mobile

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Publication de votre backend d'application mobile

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Exécution de l'application Xamarin iOS

La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

1. Accédez au projet client dans la solution de backend d'application mobile, dans Visual Studio ou Xamarin Studio.

	![][8]

	![][9]

2. Appuyez sur le bouton **Exécuter** pour générer le projet client et démarrer l'application dans l'émulateur iPhone.

3. Dans l'application, tapez un texte explicite, comme _Suivre le didacticiel_, puis cliquez sur l'icône plus \(\*\*+\*\*\).

	![][10]

	Ceci envoie une demande POST vers le nouveau backend d'application mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans cette table sont renvoyés par le backend d'application mobile et les données sont affichées dans la liste.

>[AZURE.NOTE]Vous pouvez vérifier le code \(se trouvant dans le fichier C\# QSTodoService.cs\) qui accède à votre backend d'application mobile pour exécuter une requête et insérer des données.


<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Get started with offline data sync]: app-service-mobile-xamarin-ios-get-started-offline-data-preview.md
[Get started with authentication]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-users.md
[Get started with push notifications]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/
[JavaScript backend version]: partner-xamarin-mobile-services-ios-get-started.md
[Get started with data in app services using Visual Studio 2012]: app-service-mobile-windows-store-dotnet-get-started-data-vs2012-preview.md
[Troubleshoot a mobile app .NET backend]: app-service-mobile-dotnet-backend-how-to-troubleshoot-preview.md


[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409

<!--HONumber=52-->
