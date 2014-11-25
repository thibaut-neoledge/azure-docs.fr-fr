<properties linkid="develop-mobile-tutorials-get-started-wp8" urlDisplayName="Get Started (WP8)" pageTitle="Get Started with Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# <a name="getting-started"> </a>Prise en main de Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Ce didacticiel présente l'ajout d'un service principal cloud à une application Windows Phone 8 à l'aide d'Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application *To do list* qui stocke les données d'application dans le nouveau service mobile. Le service mobile que vous allez créer utilise les langages .NET pris en charge à l'aide de Visual Studio pour la logique métier côté serveur et pour la gestion du service mobile. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur en JavaScript, consultez la [version principale JavaScript][version principale JavaScript] de cette rubrique.

Voici une capture d'écran de l'application terminée :

![][0]

> [WACOM.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Si vous ne disposez pas déjà d'un compte Azure, vous pouvez obtenir une évaluation gratuite et 10 services mobiles gratuits que vous pouvez utiliser même après l'expiration de l'évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][Version d'évaluation gratuite d'Azure].
> Ce didacticiel requiert [Visual Studio Professional 2013][Visual Studio Professional 2013]. Une version d'évaluation gratuite est disponible. Pour créer une nouvelle application Windows Phone 8.1, vous devez utiliser Visual Studio 2013 Update 2 ou une version ultérieure.

## <a name="create-new-service"> </a>Création d'un service mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Création d'une application Windows Phone

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application Windows Phone 8 connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Dans l'onglet de démarrage rapide, cliquez sur **Windows Phone 8** sous **Choisissez une plateforme** et développez **Créer une application Windows Phone 8**.

    ![][1]

    Cette opération affiche les trois étapes faciles permettant de créer une application Windows Phone connectée à votre service mobile.

    ![][2]

3.  Si ce n'est déjà fait, téléchargez et installez [Visual Studio Professional 2013][Visual Studio Professional 2013] sur votre ordinateur local ou sur votre machine virtuelle.

4.  Sous **Télécharger et publier votre service dans le cloud**, cliquez sur **Télécharger**.

    Ceci entraîne le téléchargement d'une solution contenant les projets du service mobile et de l'exemple d'application *To do list* connectée à votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

5.  Sous **Publier votre service dans le cloud**, téléchargez votre profil de publication, enregistrez le fichier téléchargé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

## Test du service mobile sur votre ordinateur local

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

> [WACOM.NOTE] Une procédure de configuration supplémentaire doit être appliquée pour exécuter une application Windows Phone qui se connecte à un service local. Pour plus d'informations, consultez la rubrique [Connexion à un service Web local à partir de l'émulateur Windows Phone 8][Connexion à un service Web local à partir de l'émulateur Windows Phone 8].

## Publication de votre service mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

1.  Dans le projet d'application Windows Phone, ouvrez le fichier App.xaml.cs, recherchez le code de création d'une instance [MobileServiceClient][MobileServiceClient], placez en commentaire le code de création de ce client à l'aide de *localhost* et annulez le commentaire du code de création du client à l'aide de l'URL du service mobile distant, lequel est semblable à ce qui suit :

        public static MobileServiceClient MobileService = new MobileServiceClient(
                    "https://todolist.azure-mobile.net/",
                    "XXXXXXX-APPLICATION-KEY-XXXXXXXX");

    Le client a désormais accès au service mobile publié sur Azure.

2.  (Facultatif) Si vous créez une application Windows Phone 8.1, cliquez avec le bouton droit sur le projet dans Explorateur de solutions, cliquez sur **Propriétés**, définissez **Windows Phone 8.1** comme **Version cible du système d'exploitation Windows Phone**, puis cliquez sur **Oui** pour mettre à niveau le projet.

3.  Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

4.  Dans l'application, tapez un texte explicite, comme *Suivre le didacticiel*, puis cliquez sur **Enregistrer**.

    Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la liste.

    <div class="dev-callout"> 
    <b>Remarque</b> 
    <p>Vous pouvez v&eacute;rifier le code qui se trouve dans le fichier MainPage.xaml.cs et permet d'acc&eacute;der au service mobile pour ex&eacute;cuter une requ&ecirc;te et ins&eacute;rer des donn&eacute;es.</p> 
    </div>

![][3]

Cette section montre comment exécuter votre nouvelle application cliente sur le service mobile exécuté sous Azure. Avant de pouvoir tester l'application Windows Phone avec le service mobile exécuté sur un ordinateur local, vous devez configurer le serveur Web et le pare-feu de manière à autoriser l'accès depuis votre émulateur ou appareil Windows Phone. Pour plus d'informations, consultez la rubrique [Configuration du serveur Web local pour autoriser les connexions à un service mobile local][Configuration du serveur Web local pour autoriser les connexions à un service mobile local].

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez effectué le démarrage rapide, découvrez comment exécuter d'autres tâches importantes dans Mobile Services :

-   [Prise en main des données][Prise en main des données]

    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de la synchronisation des données hors connexion][Prise en main de la synchronisation des données hors connexion]

    En savoir plus sur la synchronisation des données hors connexion pour rendre votre application évolutive et robuste.

-   [Prise en main de l'authentification][Prise en main de l'authentification]

    En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

-   [Prise en main des notifications Push][Prise en main des notifications Push]

    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Résolution de problèmes liés à un service principal Mobile Services .NET][Résolution de problèmes liés à un service principal Mobile Services .NET]

    En savoir plus sur le diagnostic et la résolution de problèmes liés à un service principal Mobile Services .NET.

  [version principale JavaScript]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started
  [0]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A30A4DDE2&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-phone-get-started%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-portal-quickstart-wp8.png
  [2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-steps-wp8.png
  [Connexion à un service Web local à partir de l'émulateur Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkId=391930
  [MobileServiceClient]: http://msdn.microsoft.com/fr-fr/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
  [3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-startup-wp8.png
  [Configuration du serveur Web local pour autoriser les connexions à un service mobile local]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [Prise en main de la synchronisation des données hors connexion]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-offline-data
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
  [Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
  [Résolution de problèmes liés à un service principal Mobile Services .NET]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
