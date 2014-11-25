<properties pageTitle="Get Started with Azure Mobile Services for iOS apps" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="Follow this tutorial to get started using Azure Mobile Services for iOS development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# <a name="getting-started"> </a>Prise en main de Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Ce didacticiel présente l'ajout d'un service principal cloud à une application iOS à l'aide d'Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application *To do list* qui stocke les données d'application dans le nouveau service mobile. Le service mobile que vous allez créer utilise les langages .NET pris en charge à l'aide de Visual Studio pour la logique métier côté serveur et pour la gestion du service mobile. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur en JavaScript, consultez la [version principale JavaScript][version principale JavaScript] de cette rubrique.

Voici une capture d'écran de l'application terminée :

![][0]

XCode 4.5 et iOS 5.0 ou versions ultérieures sont requis pour suivre ce didacticiel.

<div class="dev-callout"><strong>Remarque</strong> <p>Pour effectuer ce didacticiel, vous avez besoin d&rsquo;un compte Azure. Si vous ne disposez pas d&eacute;j&agrave; d'un compte Azure, vous pouvez obtenir une &eacute;valuation gratuite et 10&nbsp;services mobiles gratuits que vous pouvez utiliser m&ecirc;me apr&egrave;s l'expiration de l'&eacute;valuation. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">Version d'&eacute;valuation gratuite d'Azure</a>.</p></div>

## <a name="create-new-service"> </a>Création d'un service mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Téléchargement du service mobile sur votre ordinateur local

Maintenant que vous avez créé le service mobile, téléchargez votre projet de service mobile personnalisé afin de l'exécuter sur votre ordinateur local ou sur votre machine virtuelle.

1.  Cliquez sur le service mobile que vous venez de créer, puis dans l'onglet de démarrage rapide, cliquez sur **iOS** sous **Choisissez une plateforme** et développez **Créer une application iOS**.

    ![][1]

2.  Si ce n'est pas déjà fait, téléchargez et installez Visual Studio Professional 2013 ou une version ultérieure.

3.  Cliquez sur **Télécharger** sous **Télécharger et publier votre service dans le cloud**.

    Cela permet de télécharger le projet Visual Studio qui implémente votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

4.  Téléchargez également votre profil de publication, enregistrez le fichier téléchargé sur votre ordinateur local et notez où vous l'enregistrez.

## Test du service mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publication de votre service mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Création d'une application iOS

Dans cette section, vous allez créer une application iOS connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Dans l'onglet de démarrage rapide, cliquez sur **iOS** sous **Choisissez une plateforme** et développez **Créer une application iOS**.

3.  Si ce n'est pas déjà fait, téléchargez et installez [Xcode][Xcode] v4.4 ou une version ultérieure.

4.  Sous **Télécharger et exécuter l'application**, cliquez sur **Télécharger**.

    Cette opération télécharge le projet de votre exemple d'application *To do list* qui est connectée à votre service mobile, ainsi que le Kit de développement logiciel (SDK) Mobile Services iOS. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

## Exécution de votre nouvelle application iOS

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

Cette section montre comment exécuter votre nouvelle application cliente sur le service mobile exécuté sous Azure. Avant de pouvoir tester l'application iOS avec le service mobile exécuté sur un ordinateur local, vous devez configurer le serveur Web et le pare-feu de manière à autoriser l'accès depuis votre ordinateur de développement iOS. Pour plus d'informations, consultez la rubrique [Configuration du serveur Web local pour autoriser les connexions à un service mobile local][Configuration du serveur Web local pour autoriser les connexions à un service mobile local].

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

  [version principale JavaScript]: /fr-fr/documentation/articles/mobile-services-ios-get-started
  [0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
  [1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png
  [Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Configuration du serveur Web local pour autoriser les connexions à un service mobile local]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
  [Prise en main de la synchronisation des données hors connexion]: /fr-fr/documentation/articles/mobile-services-ios-get-started-offline-data
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
  [Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push
  [Résolution de problèmes liés à un service principal Mobile Services .NET]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
