<properties linkid="develop-mobile-tutorials-get-started-wp8" urlDisplayName="Get Started (WP8)" pageTitle="Get Started with Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Prise en main de Mobile Services
================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started "Windows Store C#")[Windows Store JavaScript](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started "Windows Store JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started "iOS") [Android](/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started "Android")
[.NET backend](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/ ".NET backend") | [JavaScript backend](/en-us/documentation/articles/mobile-services-windows-phone-get-started/ "JavaScript backend")

Ce didacticiel présente l'ajout d'un service principal cloud à une application Windows Phone 8 à l'aide d'Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application *To do list* qui stocke les données d'application dans le nouveau service mobile. Le service mobile que vous allez créer utilise les langages .NET pris en charge à l'aide de Visual Studio pour la logique métier côté serveur et pour la gestion du service mobile. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur en JavaScript, consultez la [version principale JavaScript](/en-us/documentation/articles/mobile-services-windows-phone-get-started) de cette rubrique.

Voici une capture d'écran de l'application terminée :

![](./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png)

> [WACOM.NOTE] Pour suivre ce didacticiel, vous devez disposer d'un compte Azure sur lequel la fonctionnalité Azure Mobile Services est activée. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A30A4DDE2&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-phone-get-started%2F).
> Ce didacticiel requiert [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=257546). Une version d'évaluation gratuite est disponible.

Création d'un service mobile
----------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

Création d'une application Windows Phone
----------------------------------------

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application Windows Phone 8 connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Dans l'onglet de démarrage rapide, cliquez sur **Windows Phone 8** sous **Choisissez une plateforme** et développez **Créer une application Windows Phone 8**.

	![][6]

	Cette opération affiche les trois étapes faciles permettant de créer une application Windows Phone connectée à votre service mobile.

	![](./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-steps-wp8.png)

3.  Si ce n'est déjà fait, téléchargez et installez [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=257546) sur votre ordinateur local ou sur votre machine virtuelle.

4.  Sous **Télécharger et publier votre service dans le cloud**, cliquez sur **Télécharger**.

	Ceci entraîne le téléchargement d'une solution contenant les projets du service mobile et de l'exemple d'application *To do list* connectée à votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

5.  Sous **Publier votre service dans le cloud**, téléchargez votre profil de publication, enregistrez le fichier téléchargé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

Test du service mobile sur votre ordinateur local
-------------------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

> [WACOM.NOTE] Une procédure de configuration supplémentaire doit être appliquée pour exécuter une application Windows Phone qui se connecte à un service local. Pour plus d'informations, consultez la rubrique [Connexion à un service Web local à partir de l'émulateur Windows Phone 8](http://go.microsoft.com/fwlink/p/?LinkId=391930).

Publication de votre service mobile
-----------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

1.  Dans le projet d'application Windows Phone, ouvrez le fichier App.xaml.cs, recherchez le code de création d'une instance [MobileServiceClient](http://msdn.microsoft.com/en-us/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx), placez en commentaire le code de création de ce client à l'aide de *localhost* et annulez le commentaire du code de création du client à l'aide de l'URL du service mobile distant, lequel est semblable à ce qui suit :

        <pre><code>public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXXXXX-APPLICATION-KEY-XXXXXXXX");</code></pre>

    Le client a désormais accès au service mobile publié sur Azure.

2.  Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

3.  Dans l'application, tapez un texte explicite, comme *Suivre le didacticiel*, puis cliquez sur **Enregistrer**.

    Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la liste.

    **Remarque**

    Vous pouvez vérifier le code qui se trouve dans le fichier MainPage.xaml.cs et permet d'accéder au service mobile pour exécuter une requête et insérer des données.

![](./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-startup-wp8.png)

Cette section montre comment exécuter votre nouvelle application cliente sur le service mobile exécuté sous Azure. Avant de pouvoir tester l'application Windows Phone avec le service mobile exécuté sur un ordinateur local, vous devez configurer le serveur Web et le pare-feu de manière à autoriser l'accès depuis votre émulateur ou appareil Windows Phone. Pour plus d'informations, consultez la rubrique [Configuration du serveur Web local pour autoriser les connexions à un service mobile local](/en-us/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).

Étapes suivantes
----------------

Maintenant que vous avez effectué le démarrage rapide, découvrez comment exécuter d'autres tâches importantes dans Mobile Services :

-   [Prise en main des données](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data)
    <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de l'authentification](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users)
    <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

-   [Prise en main des notifications Push](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push)
    <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

<!-- Anchors. -->
[Getting started with Mobile Services]:#getting-started
[Create a new mobile service]:#create-new-service
[Define the mobile service instance]:#define-mobile-service-instance
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png

[6]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-portal-quickstart-wp8.png
[7]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-steps-wp8.png
[8]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-service-startup.pnG

[10]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-startup-wp8.png
[11]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-data-browse.png


<!-- URLs. -->
[Get started with data]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
[Get started with authentication]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[Management Portal]: https://manage.windowsazure.com/
[JavaScript backend version]: /en-us/documentation/articles/mobile-services-windows-phone-get-started
[How to connect to a local web service from the Windows Phone 8 emulator]: http://go.microsoft.com/fwlink/p/?LinkId=391930
