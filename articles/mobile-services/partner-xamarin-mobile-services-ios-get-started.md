<properties
	pageTitle="Prise en main de Mobile Services pour les applications Xamarin iOS | Microsoft Azure"
	description="Suivez ce didacticiel pour commencer à utiliser Azure Mobile Services pour le développement Xamarin iOS."
	services="mobile-services"
	documentationCenter="xamarin"
	authors="conceptdev"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="07/21/2016"
	ms.author="craig.dunn@xamarin.com"/>

# <a name="getting-started"> </a>Prise en main de Mobile Services
[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)] &nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Pour la version Mobile Apps équivalente de cette rubrique, consultez l’article [Créer une application Xamarin.iOS](../app-service-mobile/app-service-mobile-xamarin-ios-get-started.md).

Ce didacticiel vous montre comment ajouter un service principal cloud à une application Xamarin.iOS en utilisant Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application *To do list* qui stocke les données d'application dans le nouveau service mobile.

Si vous préférez regarder une vidéo, sachez que celle ci-dessous suit la même procédure que ce didacticiel.

Vidéo : « Prise en main de Xamarin et d'Azure Mobile Services » avec Craig Dunn, évangéliste du développement pour Xamarin (durée : 10:05 min)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

Voici une capture d'écran de l'application terminée :

![][0]

Ce didacticiel nécessite XCode et Xamarin Studio pour OS X ou le plug-in Xamarin Visual Studio pour Visual Studio sur Windows. Pour obtenir des instructions complètes sur l’installation, consultez la page [Configuration et installation pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).

> [AZURE.IMPORTANT] Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10 services mobiles gratuits que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-new-service"> </a>Création d'un service mobile

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Création d'une application Xamarin.iOS

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le Portail Azure Classic pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application Xamarin.iOS connectée à votre service mobile.

1.  Dans le [Portail Azure Classic], cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2. Sous l'onglet de démarrage rapide, cliquez sur **Xamarin.iOS** sous **Choisissez une plateforme** et développez **Créer une application Xamarin.iOS**.

	![][6]

	Les trois étapes permettant de créer une application Xamarin.iOS connectée à votre service mobile s'affichent.

  	![][7]

3. Si ce n’est déjà fait, téléchargez et installez Xcode (nous vous recommandons la dernière version, Xcode 6.0 ou une version plus récente) et [Xamarin Studio].

4. Cliquez sur **Create TodoItems table** pour créer une table permettant de stocker les données d'application.

5. Sous **Download and run app**, cliquez sur **Télécharger**.

	Cette opération télécharge le projet de votre exemple d'application _To do list_ qui est connectée à votre service mobile et référence le composant Azure Mobile Services pour Xamarin.iOS. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

## Exécution de votre nouvelle application Xamarin.iOS

La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

1. Accédez à l'emplacement où vous avez enregistré les fichiers projet compressés, développez les fichiers sur votre ordinateur et ouvrez le fichier solution **XamarinTodoQuickStart.iOS.sln** dans Xamarin Studio ou Visual Studio.

	![][8]

	![][9]

2. Appuyez sur le bouton **Exécuter** pour générer le projet, et démarrez l'application dans l'émulateur iPhone (configuration par défaut pour ce projet).

3. Dans l'application, tapez un texte explicite, comme _Suivre le didacticiel_ , puis cliquez sur l'icône plus (**+**).

	![][10]

	Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la liste.

	> [AZURE.NOTE] Vous pouvez vérifier le code qui se trouve dans le fichier TodoService.cs C# et permet d'accéder au service mobile pour exécuter une requête et insérer des données.

4. De retour dans le [Portail Azure Classic], cliquez sur l’onglet **Données**, puis sur la table **TodoItems**.

	![][11]

	Cela vous permet de parcourir les données insérées par l'application dans la table.

	![][12]


## Étapes suivantes
Vous avez terminé les étapes de démarrage rapide. Découvrez ensuite comment effectuer d’autres tâches importantes dans Mobile Services :

* [Prise en main de la synchronisation des données hors connexion] Découvrez comment le démarrage rapide utilise la synchronisation des données hors connexion afin de rendre l’application réactive et robuste.

* [Prise en main de l’authentification] Découvrez comment authentifier les utilisateurs de votre application avec un fournisseur d’identité.

* [Prise en main des notifications Push] Découvrez comment envoyer une notification Push très basique à votre application.




[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Prise en main de la synchronisation des données hors connexion]: mobile-services-xamarin-ios-get-started-offline-data.md
[Prise en main de l’authentification]: partner-xamarin-mobile-services-ios-get-started-users.md
[Prise en main des notifications Push]: partner-xamarin-mobile-services-ios-get-started-push.md

[Xamarin Studio]: http://xamarin.com/download
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Portail Azure Classic]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0727_2016-->