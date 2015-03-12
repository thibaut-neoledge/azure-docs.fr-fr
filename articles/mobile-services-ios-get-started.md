<properties 
	pageTitle="Prise en main d'Azure Mobile Services pour les applications iOS" 
	description="Suivez ce didacticiel pour commencer à utiliser Azure Mobile Services pour le développement iOS." 
	services="" 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="hero-article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# <a name="getting-started"> </a>Prise en main de Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Ce didacticiel présente l'ajout d'un service principal cloud à une application iOS à l'aide d'Azure Mobile Services.</p>
<p>Si vous préférez regarder une vidéo, le clip sur la droite suit la même procédure que ce didacticiel.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="label">regarder le didacticiel</a><a style="background-image: url('/media/devcenter/mobile/videos/get-started-ios-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vidéo</span></a> <span class="time">09:38</span></div>
</div>

Dans ce didacticiel, vous allez créer un service mobile et une simple application _To do list_ qui stocke les données d'application dans le nouveau service mobile. Le service mobile à créer utilise du code JavaScript pour la logique métier côté serveur. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur dans les langages .NET pris en charge à l'aide de Visual Studio, consultez la [version .NET principal] de cette rubrique.

Voici une capture d'écran de l'application terminée :

![][0]

XCode 4.5 et iOS 5.0 ou versions ultérieures sont requis pour suivre ce didacticiel.

> [AZURE.NOTE] Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10 services mobiles gratuits que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20 target="_blank").

## <a name="create-new-service"> </a>Création d'un service mobile

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2>Création d'une application iOS</h2>

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application iOS connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2. Dans l'onglet de démarrage rapide, cliquez sur **iOS** sous **Choisissez une plateforme** et développez **Créer une application iOS**.

   	![][6]

   	Cette opération affiche les trois étapes faciles permettant de créer une application iOS connectée à votre service mobile.

  	![][7]

3. Si ce n'est pas déjà fait, téléchargez et installez [Xcode] v4.4 ou une version ultérieure.

4. Cliquez sur **Créer une table TodoItem** pour créer une table permettant de stocker les données d'application.

5. Sous **Télécharger et exécuter l'application**, cliquez sur **Télécharger**.

  	Cette opération télécharge le projet de votre exemple d'application _To do list_ qui est connectée à votre service mobile, ainsi que le Kit de développement logiciel (SDK) Mobile Services iOS. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

## Exécution de votre nouvelle application iOS

[AZURE.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

<ol start="4">
<li><p>De retour dans le portail de gestion, cliquez sur l'onglet <strong>Données</strong>, puis sur la table <strong>TodoItems</strong>.<p></li></ol>

![](./media/mobile-services-ios-get-started/mobile-data-tab.png)

Cela vous permet de parcourir les données insérées par l'application dans la table.</p>

![](./media/mobile-services-ios-get-started/mobile-data-browse.png)


## <a name="next-steps"> </a>Étapes suivantes
Maintenant que vous avez effectué le démarrage rapide, découvrez comment exécuter d'autres tâches importantes dans Mobile Services :

* [Prise en main des données]
  <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

* [Prise en main de la synchronisation des données hors connexion]
  <br/>En savoir plus sur la synchronisation des données hors connexion pour rendre votre application réactive et robuste.

* [Prise en main de l'authentification]
  <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

* [Prise en main des notifications Push]
  <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.


<!-- Anchors. -->
[Prise en main de Mobile Services]:#getting-started
[Création d'un service mobile]:#create-new-service
[Définition de l'instance de service mobile]:#define-mobile-service-instance
[Étapes suivantes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started/mobile-quickstart-completed-ios.png

[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-ios-get-started-data/
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-ios-get-started-users/
[Prise en main de la synchronisation des données hors connexion]: /fr-fr/documentation/articles/mobile-services-ios-get-started-offline-data
[Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/

[Kit de développement logiciel (SDK) Mobile Services pour iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Portail de gestion]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Version .NET principal]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started


<!--HONumber=42-->
