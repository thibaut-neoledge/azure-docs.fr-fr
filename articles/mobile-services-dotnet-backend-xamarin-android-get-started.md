<properties 
	pageTitle="Prise en main de Mobile Services pour les applications Xamarin Android - Azure Mobile Services" 
	description="Suivez ce didacticiel pour commencer à utiliser Azure Mobile Services pour le développement Xamarin Android." 
	services="" 
	documentationCenter="xamarin" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/11/2014" 
	ms.author="donnam"/>

# <a name="getting-started"> </a>Prise en main de Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Ce didacticiel vous montre comment ajouter un service principal cloud à une application Xamarin Android en utilisant Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application _To do list_ qui stocke les données d'application dans le nouveau service mobile. Le service mobile que vous allez créer utilise les langages .NET pris en charge à l'aide de Visual Studio pour la logique métier côté serveur et pour la gestion du service mobile. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur en JavaScript, consultez la [version relative au service principal JavaScript] de cette rubrique.

>[AZURE.NOTE]Cette rubrique montre comment créer un projet de service mobile à l'aide du portail de gestion Azure. Visual Studio 2013 Update 2 vous permet également d'ajouter un nouveau projet de service mobile dans une solution Visual Studio existante. Pour plus d'informations, consultez la page [Démarrage rapide : Ajout d'un service mobile (service principal .NET)](http://msdn.microsoft.com/library/windows/apps/dn629482.aspx)

Voici une capture d'écran de l'application terminée :

![][0]

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Mobile Services pour les applications Xamarin Android. 

>[AZURE.NOTE]Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10 services mobiles gratuits que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-android-get-started" target="_blank">Version d'évaluation gratuite Azure</a>.<br />Ce didacticiel requiert <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Une version d'évaluation gratuite est disponible.

## Création d'un service mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Création d'une application Xamarin Android

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile. 

Dans cette section, vous allez télécharger une nouvelle application Xamarin Android et un projet de service pour votre service mobile.

1. Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.
   
2. Dans l'onglet de démarrage rapide, cliquez sur **Xamarin** sous **Choisissez une plateforme** et développez **Créer une application Xamarin**.

   	![][6]

   	Cette opération affiche les trois étapes faciles permettant de créer une application Xamarin Android connectée à votre service mobile.

  	![][7]

3. Si ce n'est déjà fait, téléchargez et installez <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> sur votre ordinateur local ou sur votre machine virtuelle.  

4. Si vous ne l'avez pas encore fait, téléchargez et installez [Xamarin Studio] ou Xamarin pour Visual Studio.

5. Sous **Télécharger et publier votre service dans le cloud**, sélectionnez **Android** et cliquez sur **Télécharger**. 

  	Ceci entraîne le téléchargement d'une solution contenant les projets du service mobile et de l'exemple d'application _To do list_ connectée à votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

6. Téléchargez votre profil de publication, enregistrez le fichier téléchargé sur votre ordinateur local et notez où vous l'enregistrez.

## Test du service mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publication de votre service mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Exécution de l'application Xamarin Android

La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

1. Accédez au projet client dans la solution de service mobile, dans Visual Studio ou Xamarin Studio.

	![][8]

	![][9]

2. Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l'application. Vous êtes invité à sélectionner un émulateur ou un périphérique USB connecté. 

	> [AZURE.NOTE] Afin d'exécuter le projet dans l'émulateur Android, vous devez définir au moins un AVD (appareil virtuel Android). Utilisez le gestionnaire AVD pour créer et gérer ces appareils.

3. Dans l'application, tapez un texte explicite, tel que _Complete the tutorial_, puis cliquez sur l'icône du signe plus (**+**).

	![][10]

	Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la demande sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la liste.

	> [AZURE.NOTE] 
   	> Vous pouvez vérifier le code qui se trouve dans le fichier C# ToDoActivity.cs et permet d'accéder au service mobile pour exécuter une requête et insérer des données.
    
## Étapes suivantes
Maintenant que vous avez effectué le démarrage rapide, découvrez comment exécuter d'autres tâches importantes dans Mobile Services : 

* [Prise en main de la synchronisation des données hors connexion]
  <br/>Découvrez comment le démarrage rapide utilise la synchronisation des données hors connexion afin de rendre l'application réactive et robuste.

* [Prise en main de l'authentification]
  <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

* [Prise en main des notifications Push] 
  <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

* [Résolution des problèmes du service principal .NET de Mobile Services]
  <br/> Apprenez à diagnostiquer et résoudre les problèmes pouvant survenir avec un service principal Mobile Services .NET. 

<!-- Anchors. -->
[Prise en main de Mobile Services]:#getting-started
[Création d'un service mobile]:#create-new-service
[Étapes suivantes]:#next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Prise en main de la synchronisation des données hors connexion]: /fr-fr/documentation/articles/mobile-services-xamarin-android-get-started-offline-data
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users
[Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Kit de développement logiciel (SDK) Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript et HTML] : mobile-services-win8-javascript/
[Portail de gestion]: https://manage.windowsazure.com/
[Version principale JavaScript]: /fr-fr/documentation/articles/partner-xamarin-mobile-services-android-get-started
[Prise en main des données dans Mobile Services avec Visual Studio 2012]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012
[Résolution des problèmes du service principal .NET de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/


[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin pour Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409


<!--HONumber=42-->
