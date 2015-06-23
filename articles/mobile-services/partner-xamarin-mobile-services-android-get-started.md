<properties 
	pageTitle="Prise en main de Mobile Services pour Xamarin.Android" 
	writer="craigd" 
	description="Découvrez comment utiliser Azure Mobile Services avec votre application Xamarin.Android." 
	documentationCenter="xamarin" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="11/11/2014" 
	ms.author="donnam"/>

# <a name="getting-started"></a>Prise en main de Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Ce didacticiel vous montre comment ajouter un service principal cloud à une application Xamarin.Android en utilisant Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une application simple (<em>To do list</em>) qui stocke les données d'application dans le nouveau service mobile.</p>
<p>Voici une capture d'écran de l'application terminée :</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="label">Regarder le didacticiel</a><a style="background-image: url('/media/devcenter/mobile/videos/get-started-xamarin-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vidéo</span></a> <span class="time">10:05</span></div>
</div>

![][0]

Pour suivre ce didacticiel, vous avez besoin de [Xamarin.Android], qui installe Xamarin Studio et un plug-in Visual Studio (sous Windows), ainsi que la dernière plateforme Android. Le Kit de développement logiciel (SDK) Android 4.2 ou une version ultérieure est nécessaire. 

Le projet de démarrage rapide téléchargé contient le composant Azure Mobile Services pour Xamarin.Android. Alors que ce projet cible Android 4.2 ou une version ultérieure, le Kit de développement logiciel (SDK) Mobile Services requiert uniquement Android 2.2 ou une version ultérieure.

> [AZURE.IMPORTANT] Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10 services mobiles gratuits que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A9C9624B5"%20target="_blank).

## <a name="create-new-service"> </a>Création d'un service mobile

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## <h2>Création d'une application Xamarin.Android</h2>

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile. 

Dans cette section, vous allez créer une application Xamarin.Android connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2. Sous l'onglet de démarrage rapide, cliquez sur **Xamarin.Android** sous **Choisissez une plateforme** et développez **Créer une application Android**.

	![][6]

	Les trois étapes permettant de créer une application Xamarin.Android connectée à votre service mobile s'affichent.

	![][7]

3. Cliquez sur **Créer une table TodoItem** pour créer une table permettant de stocker les données d'application.

4. Sous **Télécharger et exécuter l'application**, cliquez sur **Télécharger**. 

	Cette opération télécharge le projet de votre exemple d'application _To do list_ connectée à votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement d'enregistrement.

## Exécution de votre application Android

La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

1. Accédez à l'emplacement où vous avez enregistré les fichiers projet compressés, puis développez les fichiers sur votre ordinateur.

2. Dans Xamarin Studio ou Visual Studio, cliquez sur **Fichier**, puis sur **Ouvrir**, accédez aux exemples de fichiers non compressés et sélectionnez **XamarinTodoQuickStart.Android.sln** pour l'ouvrir.

 	![][8]

	![][9]

3. Cliquez sur le bouton **Exécuter** pour générer le projet et démarrer l'application. Vous êtes invité à sélectionner un émulateur ou un périphérique USB connecté. 

	> [AUZRE.NOTE] Pour exécuter le projet dans l'émulateur Android, vous devez définir au moins un AVD (appareil virtuel Android). Utilisez le gestionnaire AVD pour créer et gérer ces appareils.

4. Dans l'application, tapez un texte explicite, par exemple _Terminer le didacticiel_, puis cliquez sur **Ajouter**.

	![][10]

	Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la demande sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la liste.

	> [AZURE.NOTE] 
   	> Vous pouvez vérifier le code qui se trouve dans le fichier ToDoActivity.cs C# et permet d'accéder au service mobile pour exécuter une requête et insérer des données.

6. De retour dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItems**.

	![][11]

	Cela vous permet de parcourir les données insérées par l'application dans la table.

	![][12]

## <a name="next-steps"> </a>Étapes suivantes
Vous avez terminé les étapes de démarrage rapide. Découvrez ensuite comment effectuer d'autres tâches importantes dans Mobile Services : 

* [Prise en main de la synchronisation des données hors connexion]
  <br/>Découvrez comment le démarrage rapide utilise la synchronisation des données hors connexion afin de rendre l'application réactive et robuste.

* [Prise en main de l'authentification]
  <br/>Découvrez comment authentifier les utilisateurs de votre application avec un fournisseur d'identité.

* [Prise en main des notifications Push] 
  <br/>Découvrez comment envoyer une notification Push très simple à votre application.

<!-- Anchors. -->
[Prise en main de Mobile Services]:#getting-started
[Création d'un service mobile]:#create-new-service
[Définition de l'instance de service mobile]:#define-mobile-service-instance
[Étapes suivantes]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-completed-android.png
[2]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create.png
[3]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page1.png
[4]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page2.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started/obile-services-selection.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-portal-quickstart-xamarin-android.png
[7]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-xs.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-vs.png
[10]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-services-diagram.png


<!-- URLs. -->
[Prise en main des données]: /develop/mobile/tutorials/get-started-with-data-xamarin-android
[Prise en main de la synchronisation des données hors connexion]: mobile-services-xamarin-android-get-started-offline-data.md
[Prise en main de l'authentification]: /develop/mobile/tutorials/get-started-with-users-xamarin-android
[Prise en main des notifications Push]: /develop/mobile/tutorials/get-started-with-push-xamarin-android
[Xamarin.Android]: http://xamarin.com/download
[Kit de développement logiciel (SDK) Mobile Services pour Android]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[WindowsAzure.com]: http://www.windowsazure.com/
[Portail de gestion]: https://manage.windowsazure.com/


<!--HONumber=52--> 