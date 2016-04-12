<properties
	pageTitle="Prise en main d’Azure Mobile Services pour les applications iOS"
	description="Suivez ce didacticiel pour commencer à utiliser Azure Mobile Services pour le développement iOS."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="03/09/2016"
	ms.author="krisragh"/>

# <a name="getting-started"> </a>Prise en main de Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Pour la version Mobile Apps équivalente de cette rubrique, consultez l’article [Créer une application iOS dans Azure Mobile Apps](../app-service-mobile/app-service-mobile-ios-get-started.md).

Ce didacticiel présente l’ajout d’un service principal cloud à une application iOS à l’aide d’Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application _To do list_ qui stocke les données d'application dans le nouveau service mobile. Le service mobile utilise .NET et Visual Studio pour la logique métier côté serveur. Pour créer un service mobile avec une logique métier côté serveur en JavaScript, consultez la [version relative au service principal JavaScript] de cette rubrique.

> [AZURE.NOTE] Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir des [services mobiles gratuits que vous pourrez conserver après l'expiration de votre période d'évaluation](https://azure.microsoft.com/pricing/details/mobile-services/). Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-ios-get-started%2F).

## <a name="create-new-service"> </a>Création d'un service mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Téléchargement du service mobile et de l’application sur votre ordinateur local

Vous avez créé le service mobile. À présent, vous allez télécharger des projets que vous pouvez exécuter localement.

1. Cliquez sur le service mobile que vous venez de créer, puis dans l’onglet de démarrage rapide, cliquez sur **iOS** sous **Choisir une plateforme** et développez **Créer une application iOS**.

2. Sur votre PC Windows, cliquez sur **Télécharger** sous **Télécharger et publier votre service dans le cloud**. Cela permet de télécharger le projet Visual Studio qui implémente votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

3. Sur votre Mac, cliquez sur **Télécharger** sous **Télécharger et exécuter l’application**. Cette opération télécharge le projet de votre exemple d'application _To do list_ qui est connectée à votre service mobile, ainsi que le Kit de développement logiciel (SDK) Mobile Services iOS. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

## Test du service mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publication de votre service mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


## Exécution de votre nouvelle application iOS

[AZURE.INCLUDE [mobile-services-ios-run-app](../../includes/mobile-services-ios-run-app.md)]


## <a name="next-steps"> </a>Étapes suivantes

Cette section montre comment exécuter votre nouvelle application cliente sur le service mobile exécuté sous Azure. Avant de pouvoir tester l'application iOS avec le service mobile exécuté sur un ordinateur local, vous devez configurer le serveur Web et le pare-feu de manière à autoriser l'accès depuis votre ordinateur de développement iOS. Pour plus d'informations, consultez la rubrique [Configuration du serveur Web local pour autoriser les connexions à un service mobile local](mobile-services-dotnet-backend-how-to-configure-iis-express.md).

Apprenez à effectuer d’autres tâches importantes dans Mobile Services :

* [Prise en main de la synchronisation des données hors connexion] <br/>Découvrez comment utiliser la synchronisation des données hors connexion pour rendre votre application réactive et robuste.

* [Ajout de l’authentification à une application existante] <br/>Découvrez comment authentifier les utilisateurs de votre application avec un fournisseur d’identité.

* [Ajout de notifications Push à votre application existante] <br/>Découvrez comment envoyer une notification Push très basique à votre application.

* [Résolution des problèmes d’un serveur principal .NET Mobile Services] <br/>Découvrez comment diagnostiquer et résoudre les problèmes qui peuvent se produire avec un serveur principal .NET Mobile Services.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Prise en main de la synchronisation des données hors connexion]: mobile-services-ios-get-started-offline-data.md
[Ajout de l’authentification à une application existante]: mobile-services-dotnet-backend-ios-get-started-users.md
[Ajout de notifications Push à votre application existante]: mobile-services-dotnet-backend-ios-get-started-push.md
[Résolution des problèmes d’un serveur principal .NET Mobile Services]: mobile-services-dotnet-backend-how-to-troubleshoot.md
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[version relative au service principal JavaScript]: mobile-services-ios-get-started.md

<!---HONumber=AcomDC_0323_2016-->