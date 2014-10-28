<properties pageTitle="Get Started with Azure Mobile Services for iOS apps" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="Follow this tutorial to get started using Azure Mobile Services for iOS development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="hero-article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# <a name="getting-started"> </a>Prise en main de Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started][]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Ce didacticiel pr&eacute;sente l'ajout d'un service principal cloud &agrave; une application iOS &agrave; l'aide d'Azure Mobile Services.</p>
<p>Si vous pr&eacute;f&eacute;rez regarder une vid&eacute;o, le clip sur la droite suit la m&ecirc;me proc&eacute;dure que ce didacticiel.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-ios-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vid&eacute;o</span></a> <span class="time">09:38:00</span></div>

</div>

Dans ce didacticiel, vous allez créer un service mobile et une simple application *To do list* qui stocke les données d'application dans le nouveau service mobile. Le service mobile à créer utilise du code JavaScript pour la logique métier côté serveur. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur dans les langages .NET pris en charge à l'aide de Visual Studio, consultez la [version principale .NET][] de cette rubrique.

Voici une capture d'écran de l'application terminée :

![][]

XCode 4.5 et iOS 5.0 ou versions ultérieures sont requis pour suivre ce didacticiel.

<div class="dev-callout"><strong>Remarque</strong> <p>Pour effectuer ce didacticiel, vous avez besoin d&rsquo;un compte Azure. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une &eacute;valuation d'Azure et obtenir jusqu'&agrave; 10&nbsp;services mobiles gratuits que vous pourrez conserver apr&egrave;s l'expiration de votre p&eacute;riode d'&eacute;valuation. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">Version d'&eacute;valuation gratuite d'Azure</a>.</p></div>

## <a name="create-new-service"> </a>Création d'un service mobile

[WACOM.INCLUDE [mobile-services-create-new-service][]]

## <span class="short-header">Création d'une application</span>Création d'une application iOS

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application iOS connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Dans l'onglet de démarrage rapide, cliquez sur **iOS** sous **Choisissez une plateforme** et développez **Créer une application iOS**.

    ![][1]

    Cette opération affiche les trois étapes faciles permettant de créer une application iOS connectée à votre service mobile.

    ![][2]

3.  Si ce n'est pas déjà fait, téléchargez et installez [Xcode][] v4.4 ou une version ultérieure.

4.  Cliquez sur **Create TodoItems table** pour créer une table permettant de stocker les données d'application.

5.  Sous **Télécharger et exécuter l'application**, cliquez sur **Télécharger**.

    Cette opération télécharge le projet de votre exemple d'application *To do list* qui est connectée à votre service mobile, ainsi que le Kit de développement logiciel (SDK) Mobile Services iOS. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

## Exécution de votre nouvelle application iOS

[WACOM.INCLUDE [mobile-services-ios-run-app][]]

1.  

    De retour dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItems**.

    <p>

</p>
![][3]

Cela vous permet de parcourir les données insérées par l'application dans la table.

</p>
![][4]

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez effectué le démarrage rapide, découvrez comment exécuter d'autres tâches importantes dans Mobile Services :

-   [Prise en main des données][]

    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de la synchronisation des données hors connexion][]

    En savoir plus sur la synchronisation des données hors connexion pour rendre votre application réactive et robuste.

-   [Prise en main de l'authentification][]

    En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

-   [Prise en main des notifications Push][]

    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [regarder le didacticiel]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart
  [version principale .NET]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started
  []: ./media/mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
  [2]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
  [Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [mobile-services-ios-run-app]: ../includes/mobile-services-ios-run-app.md
  [3]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
  [4]: ./media/mobile-services-ios-get-started/mobile-data-browse.png
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-ios-get-started-data/
  [Prise en main de la synchronisation des données hors connexion]: /fr-fr/documentation/articles/mobile-services-ios-get-started-offline-data
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-ios-get-started-users/
  [Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
