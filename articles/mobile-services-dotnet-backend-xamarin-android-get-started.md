<properties linkid="mobile-services-dotnet-backend-xamarin-android-get-started" urlDisplayName="Get Started with Mobile Services for Xamarin Android apps" pageTitle="Get Started with Mobile Services for Xamarin Android apps - Azure Mobile Services" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Xamarin Android development" metaCanonical="" services="" documentationCenter="Mobile" title="Get Started with Mobile Services for Xamarin Android apps" authors="donnam" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# <a name="getting-started"> </a>Prise en main de Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Ce didacticiel vous montre comment ajouter un service principal cloud à une application Xamarin Android en utilisant Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application *To do list* qui stocke les données d'application dans le nouveau service mobile. Le service mobile que vous allez créer utilise les langages .NET pris en charge à l'aide de Visual Studio pour la logique métier côté serveur et pour la gestion du service mobile. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur en JavaScript, consultez la [version principale JavaScript][version principale JavaScript] de cette rubrique.

> [WACOM.NOTE]Cette rubrique montre comment créer un projet de service mobile à l'aide du portail de gestion Azure. Visual Studio 2013 Update 2 vous permet également d'ajouter un nouveau projet de service mobile dans une solution Visual Studio existante. Pour plus d'informations, consultez la page [Démarrage rapide : ajout d'un service mobile (service principal .NET)][Démarrage rapide : ajout d'un service mobile (service principal .NET)]

Voici une capture d'écran de l'application terminée :

![][0]

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Mobile Services pour les applications Xamarin Android.

> [WACOM.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][Version d'évaluation gratuite d'Azure].
> Ce didacticiel requiert [Visual Studio Professional 2013][Visual Studio Professional 2013]. Une version d'évaluation gratuite est disponible.

## Création d'un service mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Création d'une application Xamarin Android

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez télécharger une nouvelle application Xamarin Android et un projet de service pour votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Dans l'onglet de démarrage rapide, cliquez sur **Xamarin** sous **Choisissez une plateforme** et développez **Créer une application Xamarin**.

    ![][1]

    Cette opération affiche les trois étapes faciles permettant de créer une application Xamarin Android connectée à votre service mobile.

    ![][2]

3.  Si ce n'est déjà fait, téléchargez et installez [Visual Studio Professional 2013][Visual Studio Professional 2013] sur votre ordinateur local ou sur votre machine virtuelle.

4.  Si vous ne l'avez pas encore fait, téléchargez et installez [Xamarin Studio][Xamarin Studio] ou Xamarin pour Visual Studio.

5.  Sous **Télécharger et publier votre service dans le cloud**, sélectionnez **Android** et cliquez sur **Télécharger**.

    Ceci entraîne le téléchargement d'une solution contenant les projets du service mobile et de l'exemple d'application *To do list* connectée à votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

6.  Téléchargez votre profil de publication, enregistrez le fichier téléchargé sur votre ordinateur local et notez où vous l'enregistrez.

## Test du service mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publication de votre service mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Exécution de l'application Xamarin Android

La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

1.  Accédez au projet client dans la solution de service mobile, dans Visual Studio ou Xamarin Studio.

    ![][3]

    ![][4]

2.  Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l'application. Vous êtes invité à sélectionner un émulateur ou un périphérique USB connecté.

    <div class="dev-callout">
<strong>Remarque</strong>
<p>Afin d'ex&eacute;cuter le projet dans l'&eacute;mulateur Android, vous devez d&eacute;finir au moins un AVD (appareil virtuel Android). Utilisez le gestionnaire AVD pour cr&eacute;er et g&eacute;rer ces appareils.</p></div>

3.  Dans l'application, tapez un texte explicite, comme *Suivre le didacticiel*, puis cliquez sur l'icône plus (**+**).

    ![][5]

    Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la liste.

    > [WACOM.NOTE]
    > Vous pouvez vérifier le code (se trouvant dans le fichier ToDoActivity.cs C#) qui accède au service mobile pour exécuter une requête et insérer des données.

## Étapes suivantes

Maintenant que vous avez effectué le démarrage rapide, découvrez comment exécuter d'autres tâches importantes dans Mobile Services :

-   [Prise en main de l'authentification][Prise en main de l'authentification]<br />
    En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

-   [Prise en main des notifications Push][Prise en main des notifications Push]<br />
    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.





  [version principale JavaScript]: /fr-fr/documentation/articles/partner-xamarin-mobile-services-android-get-started
  [0]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-completed-android.png
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-android-get-started
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [1]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
  [2]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-steps-xamarin-android.png
  [Xamarin Studio]: http://xamarin.com/download
  [3]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
  [4]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
  [5]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-startup-android.png
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users
  [Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push
