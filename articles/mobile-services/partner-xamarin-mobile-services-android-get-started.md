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
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/25/2015" 
	ms.author="donnam"/>

# <a name="getting-started"></a>Prise en main de Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Ce didacticiel vous montre comment ajouter un service principal cloud à une application Xamarin.Android en utilisant Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application <em>To do list</em> qui stocke les données d'application dans le nouveau service mobile.</p>
<p>Voici une capture d'écran de l'application terminée&#160;:</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-xamarin-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vidéo</span></a> <span class="time">10:05:00</span></div>
</div>

![][0]

Pour suivre ce didacticiel, vous avez besoin de [Xamarin.Android], qui installe Xamarin Studio et un plug-in Visual Studio (sous Windows), ainsi que la dernière plateforme Android. Le Kit de développement logiciel (SDK) Android 4.2 ou une version ultérieure est nécessaire.

Le projet de démarrage rapide téléchargé contient le composant Azure Mobile Services pour Xamarin.Android. Alors que ce projet cible Android 4.2 ou une version ultérieure, le Kit de développement logiciel (SDK) Mobile Services requiert uniquement Android 2.2 ou une version ultérieure.

> [AZURE.IMPORTANT]Pour suivre ce didacticiel, vous avez besoin d’un compte Azure. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10services mobiles gratuits que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5"%20target="_blank).

## <a name="create-new-service"> </a>Création d'un service mobile

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Création d'une application Xamarin.Android

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application Xamarin.Android connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2. Sous l'onglet de démarrage rapide, cliquez sur **Xamarin.Android** sous **Choisissez une plateforme** et développez **Créer une application Android**.

	![][6]

	Les trois étapes permettant de créer une application Xamarin.Android connectée à votre service mobile s'affichent.

	![][7]

3. Cliquez sur **Create TodoItem table** pour créer une table permettant de stocker les données d'application.

4. Sous **Download and run app**, cliquez sur **Télécharger**.

	Cette opération télécharge le projet de votre exemple d'application _To do list_ qui est connectée à votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

## Exécution de votre application Android

La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

1. Accédez à l'emplacement où vous avez enregistré les fichiers projet compressés, puis développez les fichiers sur votre ordinateur.

2. Dans Xamarin Studio ou Visual Studio, cliquez sur **Fichier**, puis sur **Ouvrir**, accédez aux exemples de fichiers non compressés et sélectionnez **XamarinTodoQuickStart.Android.sln** pour l'ouvrir.

 	![][8]

	![][9]

3. Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l'application. Vous êtes invité à sélectionner un émulateur ou un périphérique USB connecté.

	> [AZURE.NOTE]Afin d'exécuter le projet dans l'émulateur Android, vous devez définir au moins un AVD (appareil virtuel Android). Utilisez le gestionnaire AVD pour créer et gérer ces appareils.

4. Dans l'application, tapez un texte explicite, comme _Suivre le didacticiel_, puis cliquez sur **Ajouter**.

	![][10]

	Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la liste.

	> [AZURE.NOTE]Vous pouvez vérifier le code qui se trouve dans le fichier ToDoActivity.cs C# et permet d'accéder au service mobile pour exécuter une requête et insérer des données.

6. De retour dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItems**.

	![][11]

	Cela vous permet de parcourir les données insérées par l'application dans la table.

	![][12]

## <a name="next-steps"> </a>Étapes suivantes
Vous avez terminé les étapes de démarrage rapide. Découvrez ensuite comment effectuer d’autres tâches importantes dans Mobile Services :

* [Prise en main de la synchronisation des données hors connexion] <br/>Découvrez comment le démarrage rapide utilise la synchronisation des données hors connexion afin de rendre l'application réactive et robuste.

* [Prise en main de l'authentification] <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

* [Prise en main des notifications Push] <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

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
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-android
[Prise en main de la synchronisation des données hors connexion]: mobile-services-xamarin-android-get-started-offline-data.md
[Prise en main de l'authentification]: /develop/mobile/tutorials/get-started-with-users-xamarin-android
[Prise en main des notifications Push]: /develop/mobile/tutorials/get-started-with-push-xamarin-android
[Xamarin.Android]: http://xamarin.com/download
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Azure]: http://azure.microsoft.com/
[Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=Oct15_HO1-->