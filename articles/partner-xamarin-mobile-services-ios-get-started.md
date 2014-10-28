<properties pageTitle="Get Started with Mobile Services for Xamarin iOS apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Xamarin iOS development." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with Mobile Services" authors="craigd" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="craigd"></tags>

# <a name="getting-started"> </a>Prise en main de Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started][]]

Ce didacticiel vous montre comment ajouter un service principal cloud à une application Xamarin.iOS en utilisant Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application *To do list* qui stocke les données d'application dans le nouveau service mobile.

Si vous préférez regarder une vidéo, sachez que celle ci-dessous suit la même procédure que ce didacticiel.

Vidéo : « Prise en main de Xamarin et d'Azure Mobile Services » avec Craig Dunn, évangéliste du développement pour Xamarin (durée : 10 min 5 s)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

Voici une capture d'écran de l'application terminée :

![][]

Pour suivre ce didacticiel, vous avez besoin de XCode 4.5 et d'iOS 5.0 (ou versions ultérieures), ainsi que de [Xamarin Studio][] pour OS X ou du plug-in Xamarin Visual Studio pour Visual Studio sous Windows.

<div class="dev-callout"><strong>Remarque</strong> <p>Pour effectuer ce didacticiel, vous avez besoin d&rsquo;un compte Azure. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une &eacute;valuation d'Azure et obtenir jusqu'&agrave; 10&nbsp;services mobiles gratuits que vous pourrez conserver apr&egrave;s l'expiration de votre p&eacute;riode d'&eacute;valuation. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-xamarin-ios%2F" target="_blank">Version d'&eacute;valuation gratuite d'Azure</a>.</p></div>

## <a name="create-new-service"> </a>Création d'un service mobile

[WACOM.INCLUDE [mobile-services-create-new-service][]]

## <span class="short-header">Création d'une application</span> Création d'une application Xamarin.iOS

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application Xamarin.iOS connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Sous l'onglet de démarrage rapide, cliquez sur **Xamarin.iOS** sous **Choisissez une plateforme** et développez **Créer une application Xamarin.iOS**.

    ![][1]

    Les trois étapes permettant de créer une application Xamarin.iOS connectée à votre service mobile s'affichent.

    ![][2]

3.  Si vous ne l'avez pas déjà fait, téléchargez et installez [Xcode] v4.4 ou une version ultérieure et [Xamarin Studio][].

4.  Cliquez sur **Create TodoItems table** pour créer une table permettant de stocker les données d'application.

5.  Sous **Download and run app**, cliquez sur **Télécharger**.

    Cette action permet de télécharger le projet de l'exemple d'application *To do list* qui est connecté à votre service mobile et référence le composant Azure Mobile Services pour Xamarin.iOS. Enregistrez le fichier projet compressé sur votre ordinateur local, et notez son emplacement.

## <span class="short-header">Exécution de votre application</span>Exécution de votre nouvelle application Xamarin.iOS

La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

1.  Accédez à l'emplacement où vous avez enregistré les fichiers projet compressés, développez les fichiers sur votre ordinateur et ouvrez le fichier solution **XamarinTodoQuickStart.iOS.sln** dans Xamarin Studio ou Visual Studio.

    ![][3]

    ![][4]

2.  Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l'application dans l'émulateur iPhone (configuration par défaut pour ce projet).

3.  Dans l'application, tapez un texte explicite, comme *Suivre le didacticiel*, puis cliquez sur l'icône plus (**+**).

    ![][5]

    Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la liste.

    <div class="dev-callout"> 
<b>Remarque</b> 
<p>Vous pouvez v&eacute;rifier le code qui se trouve dans le fichier TodoService.cs C# et permet d'acc&eacute;der au service mobile pour ex&eacute;cuter une requ&ecirc;te et ins&eacute;rer des donn&eacute;es.</p> 
</div>

4.  De retour dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItems**.

    ![][6]

    Cela vous permet de parcourir les données insérées par l'application dans la table.

    ![][7]

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
  []: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
  [Xamarin Studio]: http://xamarin.com/download
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A643EE910&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-xamarin-ios%2F
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
  [2]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
  [3]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
  [4]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
  [5]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
  [6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
  [7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png
  [Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-ios
  [Prise en main de la synchronisation des données hors connexion]: /fr-fr/develop/mobile/tutorials/mobile-services-xamarin-ios-get-started-offline-data
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-xamarin-ios
  [Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-xamarin-ios
