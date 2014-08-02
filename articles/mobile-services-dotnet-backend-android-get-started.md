<properties pageTitle="Get Started with Azure Mobile Services for Android apps" metaKeywords="Azure android application, mobile service android, getting started Azure android, azure droid, getting started droid windows" description="Follow this tutorial to get started using Azure Mobile Services for Android development." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Prise en main de Mobile Services
================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started "Windows Store C#") [Windows Store JavaScript](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started "Windows Store JavaScript") [Windows Phone](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started "Windows Phone") [iOS](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started "iOS") [Android](/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started "Android")

[.NET backend](/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started/ ".NET backend") | [JavaScript backend](/en-us/documentation/articles/mobile-services-android-get-started/ "JavaScript backend")

Ce didacticiel présente l'ajout d'un service principal cloud à une application Android à l'aide d'Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application *To do list* qui stocke les données d'application dans le nouveau service mobile. Le service mobile que vous allez créer utilise les langages .NET pris en charge à l'aide de Visual Studio pour la logique métier côté serveur et pour la gestion du service mobile. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur en JavaScript, consultez la [version principale JavaScript](/en-us/documentation/articles/mobile-services-android-get-started/) de cette rubrique.

Voici une capture d'écran de l'application terminée :

![](./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png)

Pour suivre ce didacticiel, vous avez besoin des [Outils de développement Android](https://go.microsoft.com/fwLink/p/?LinkID=280125), qui incluent l'IDE (environnement de développement intégré) Eclipse, le plug-in ADT (outils de développement Android) et la dernière plateforme Android. Android 4.2 ou une version ultérieure est nécessaire.

Le projet de démarrage rapide téléchargé contient le Kit de développement logiciel (SDK) Mobile Services pour Android. Alors que ce projet requiert Android 4.2 ou une version ultérieure, le Kit de développement logiciel (SDK) Mobile Services requiert uniquement Android 2.2 ou une version ultérieure.

**Remarque**

Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28).

Création d'un service mobile
----------------------------

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

Téléchargement du service mobile sur votre ordinateur local
-----------------------------------------------------------

Maintenant que vous avez créé le service mobile, téléchargez votre projet de service mobile personnalisé afin de l'exécuter sur votre ordinateur local ou sur votre machine virtuelle.

1.  Cliquez sur le service mobile que vous venez de créer, puis dans l'onglet de démarrage rapide, cliquez sur **Android** sous **Choisissez une plateforme** et développez **Créer une application Android**.

2.  Si vous ne l'avez pas encore fait, téléchargez et installez [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) ou une version ultérieure.

3.  Cliquez sur **Télécharger** sous **Télécharger et publier votre service dans le cloud**.

    Cela permet de télécharger le projet Visual Studio qui implémente votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

4.  Téléchargez également votre profil de publication, enregistrez le fichier téléchargé sur votre ordinateur local et notez où vous l'enregistrez.

Test du service mobile
----------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

Publication de votre service mobile
-----------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

Création d'une application Android
----------------------------------

Dans cette section, vous allez créer une application Android connectée à votre service mobile.

1.  Dans le [portail de gestion](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Dans l'onglet de démarrage rapide, cliquez sur **Android** sous **Choisissez une plateforme** et développez **Créer une application Android**.

3.  Si ce n'est pas déjà fait, téléchargez et installez les [outils de développement Android](https://go.microsoft.com/fwLink/p/?LinkID=280125) sur votre ordinateur local ou sur votre machine virtuelle.

4.  Cliquez sur **Créer une table TodoItem** pour créer une table permettant de stocker les données d'application.

5.  Sous **Télécharger et exécuter l'application**, cliquez sur **Télécharger**.

Cette opération télécharge le projet de votre exemple d'application *To do list* qui est connectée à votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

Exécution de votre application Android
--------------------------------------

La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

1.  Accédez à l'emplacement où vous avez enregistré les fichiers projet compressés, puis développez les fichiers sur votre ordinateur.

2.  Dans Eclipse, cliquez sur **Fichier**, sur **Importer**, développez **Android**, puis cliquez sur **Code Android existant dans l'espace de travail** et sur **Suivant.**

	![](./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png)

1.  Cliquez sur **Parcourir**, accédez à l'emplacement des fichiers projet développés, cliquez sur **OK**, veillez à ce que le projet TodoActivity soit sélectionné, puis cliquez sur **Terminer**.

	![](./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png)

    Cette opération importe les fichiers de projet dans l'espace de travail actuel.

	![](./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png)

1.  À partir du menu **Exécuter**, cliquez sur **Exécuter** pour démarrer le projet dans l'émulateur Android.

    **Remarque**

    Pour pouvoir exécuter le projet dans l'émulateur Android, vous devez définir au moins un AVD (appareil virtuel Android). Utilisez le gestionnaire AVD pour créer et gérer ces appareils.

2.  Dans l'application, tapez un texte explicite, comme *Suivre le didacticiel*, puis cliquez sur **Ajouter**.

	![][10]

	Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la liste.

    **Remarque**

    Vous pouvez vérifier le code qui se trouve dans le fichier ToDoActivity.java et permet d'accéder au service mobile pour exécuter une requête et insérer des données.


<!-- Anchors. -->
[Getting started with Mobile Services]:#getting-started
[Create a new mobile service]:#create-new-service
[Define the mobile service instance]:#define-mobile-service-instance
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
[1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs.png




[6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Get started with data]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[Get started with authentication]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-push
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Management Portal]: https://manage.windowsazure.com/