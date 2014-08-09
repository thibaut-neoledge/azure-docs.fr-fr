<properties pageTitle="Get Started with Azure Mobile Services for Android apps" metaKeywords="Azure android application, mobile service android, getting started Azure android, azure droid, getting started droid windows" description="Follow this tutorial to get started using Azure Mobile Services for Android development." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Prise en main de Mobile Services
================================

[Windows Store](/fr-fr/documentation/articles/mobile-services-windows-store-get-started "Windows Store") [Windows Phone](/fr-fr/documentation/articles/mobile-services-windows-phone-get-started "Windows Phone") [iOS](/fr-fr/documentation/articles/mobile-services-ios-get-started "iOS") [Android](/fr-fr/documentation/articles/mobile-services-android-get-started "Android") [HTML](/fr-fr/documentation/articles/mobile-services-html-get-started "HTML") [Xamarin.iOS](/fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started "Xamarin.iOS") [Xamarin.Android](/fr-fr/documentation/articles/partner-xamarin-mobile-services-android-get-started "Xamarin.Android") [Sencha](/fr-fr/documentation/articles/partner-sencha-mobile-services-get-started/ "Sencha") [PhoneGap](/fr-fr/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/ "PhoneGap")

[.NET](/fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started/ ".NET") | [JavaScript](/fr-fr/documentation/articles/mobile-services-android-get-started/ "JavaScript")

Ce didacticiel présente l'ajout d'un service principal cloud à une application Android à l'aide d'Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application *To do list* qui stocke les données d'application dans le nouveau service mobile.

Voici une capture d'écran de l'application terminée :

[regarder le didacticiel](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services) [Lire la vidéo](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services)7:26

![](./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png)

Pour suivre ce didacticiel, vous avez besoin des [Outils de développement Android](https://go.microsoft.com/fwLink/p/?LinkID=280125), qui incluent l'IDE (environnement de développement intégré) Eclipse, le plug-in ADT (outils de développement Android) et la dernière plateforme Android. Android 4.2 ou une version ultérieure est nécessaire.

Le projet de démarrage rapide téléchargé contient le Kit de développement logiciel (SDK) Mobile Services pour Android. Alors que ce projet requiert Android 4.2 ou une version ultérieure, le Kit de développement logiciel (SDK) Mobile Services requiert uniquement Android 2.2 ou une version ultérieure.

**Remarque**

Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=AE564AB28).

Création d'un service mobile
----------------------------

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2 data-morhtml="true"\>Création d'une applicationCréation d'une application Android</h2\>
---------------------------------------------------------------------------------------------

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application Android connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Dans l'onglet de démarrage rapide, cliquez sur **Android** sous **Choisissez une plateforme** et développez **Créer une application Android**.

	![][6]
	
	Cela affiche les trois étapes faciles pour créer une application Android connectée à votre service mobile.

	![][7]

3.  Si ce n'est pas déjà fait, téléchargez et installez les [outils de développement Android](https://go.microsoft.com/fwLink/p/?LinkID=280125) sur votre ordinateur local ou sur votre machine virtuelle.

4.  Cliquez sur **Create TodoItem table** pour créer une table permettant de stocker les données d'application.

5.  Sous **Download and run your app**, cliquez sur **Télécharger**.

	Cette opération télécharge le projet de votre exemple d'application *To do list* qui est connectée à votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

Exécution de votre application Android
--------------------------------------

La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

1.  Accédez à l'emplacement où vous avez enregistré les fichiers projet compressés, puis développez les fichiers sur votre ordinateur.

2.  Dans Eclipse, cliquez sur **Fichier**, puis sur **Importer**, développez **Android**, cliquez sur **Code Android existant dans l'espace de travail**, puis sur **Suivant.**

	![](./media/mobile-services-android-get-started/mobile-services-import-android-workspace.png)

3.  Cliquez sur **Parcourir**, accédez à l'emplacement des fichiers projet développés, cliquez sur **OK**, veillez à ce que le projet TodoActivity soit sélectionné, puis cliquez sur **Terminer**.

	![](./media/mobile-services-android-get-started/mobile-services-import-android-project.png)

    Cette opération importe les fichiers projet dans l'espace de travail actuel.

	![](./media/mobile-services-android-get-started/mobile-eclipse-quickstart.png)

4.  À partir du menu **Exécuter**, cliquez sur **Exécuter** pour démarrer le projet dans l'émulateur Android.

    **Remarque**

    Afin d'exécuter le projet dans l'émulateur Android, vous devez définir au moins un AVD (appareil virtuel Android). Utilisez le gestionnaire AVD pour créer et gérer ces appareils.

5.  Dans l'application, tapez un texte explicite, comme *Suivre le didacticiel*, puis cliquez sur **Ajouter**.

	![][10]

	Cette opération envoie une requête POST au nouveau service mobile hébergé sur Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la liste.

    **Remarque**

    Vous pouvez vérifier le code qui se trouve dans le fichier ToDoActivity.java et permet d'accéder au service mobile pour exécuter une requête et insérer des données.

6.  De retour dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItems**.

	![][11]

	Cette opération vous permet de parcourir les données insérées par l'application dans la table.

	![][12]

Étapes suivantes
----------------

Maintenant que vous avez effectué le démarrage rapide, découvrez comment effectuer d'autres tâches importantes dans Mobile Services :

-   [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-android)
    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-android)
    En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

-   [Prise en main des notifications Push](/en-us/develop/mobile/tutorials/get-started-with-push-android)
    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

[0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png





[6]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-android
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-android
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-android
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Management Portal]: https://manage.windowsazure.com/

