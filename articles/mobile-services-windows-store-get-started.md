<properties pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C# or JavaScript. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Prise en main de Mobile Services

<div class="dev-center-tutorial-selector sublanding">
    <a href="/fr-fr/documentation/articles/mobile-services-windows-store-get-started" title="Windows Store" class="current">Windows Store</a>
    <a href="/fr-fr/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
    <a href="/fr-fr/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a>
    <a href="/fr-fr/documentation/articles/mobile-services-android-get-started" title="Android">Android</a>
    <a href="/fr-fr/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a>
    <a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
    <a href="/fr-fr/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
    <a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
    <a href="/fr-fr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/" title=".NET backend">Serveur principal .NET</a> | 
    <a href="/fr-fr/documentation/articles/mobile-services-windows-store-get-started/"  title="JavaScript backend" class="current">JavaScript backend</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Ce didacticiel pr&eacute;sente l'ajout d'un service principal cloud &agrave; une application Windows Store &agrave; l'aide d'Azure Mobile Services.</p>
<p>Si vous pr&eacute;f&eacute;rez regarder une vid&eacute;o, le clip sur la droite suit la m&ecirc;me proc&eacute;dure que ce didacticiel. Dans la vid&eacute;o, apr&egrave;s avoir pr&eacute;sent&eacute; Mobile Services, Scott Guthrie d&eacute;crit la cr&eacute;ation de votre premier service mobile et explique comment s'y connecter &agrave; partir d'une application Windows Store.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vid&eacute;o</span></a> <span class="time">10:08:00</span></div>

</div>

Dans ce didacticiel, vous allez créer un service mobile et une simple application *To do list* qui stocke les données d'application dans le nouveau service mobile. Le service mobile à créer utilise du code JavaScript pour la logique métier côté serveur. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur dans les langages .NET pris en charge à l'aide de Visual Studio, consultez la version principale .NET de cette rubrique.

Voici une capture d'écran de l'application terminée :

![][]

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Mobile Services pour les applications Windows Store.

> [WACOM.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][].

> Ce didacticiel nécessite Visual Studio 2013. Pour vous connecter à une application Windows Store à l'aide de Visual Studio 2012, suivez la procédure de la rubrique [Prise en main des données dans Mobile Services à l'aide de Visual Studio 2012][].

## Création d'un service mobile

[WACOM.INCLUDE [mobile-services-create-new-service][]]

## Création d'une application Windows Store

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application Windows Store connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Dans l'onglet de démarrage rapide, cliquez sur **Windows** sous **Choisissez une plateforme** et développez **Créer une application Windows Store**.

    ![][1]

    Cette opération affiche les trois étapes faciles permettant de créer une application Windows Store connectée à votre service mobile.

    ![][2]

3.  Si ce n'est pas déjà fait, téléchargez et installez [Visual Studio 2013 Express pour Windows][] sur votre ordinateur local ou sur votre machine virtuelle.

4.  Cliquez sur **Create TodoItem table** pour créer une table permettant de stocker les données d'application.

5.  Sous **Download and run your app**, sélectionnez une langue pour votre application, puis cliquez sur **Télécharger**.

    Cette opération télécharge le projet de votre exemple d'application *To do list* qui est connectée à votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

## Exécution de votre application Windows

La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

1.  Accédez à l'emplacement où vous avez enregistré les fichiers projet compressés, développez les fichiers sur votre ordinateur et ouvrez le fichier solution dans Visual Studio 2013 Express pour Windows.

2.  Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

3.  Dans l'application, tapez un texte explicite, comme *Suivre le didacticiel*, dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

    ![][3]

    Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la deuxième colonne de l'application.

    > [WACOM.NOTE]Vous pouvez vérifier le code (se trouvant dans le fichier MainPage.xaml.cs (projet C#/XAML) ou dans le fichier default.js (projet JavaScript/HTML)) permettant d'accéder au service mobile pour exécuter une requête et insérer des données.

4.  De retour dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItems**.

    ![][4]

    Cela vous permet de parcourir les données insérées par l'application dans la table.

    ![][5]

## Étapes suivantes

Maintenant que vous avez effectué le démarrage rapide, découvrez comment exécuter d'autres tâches importantes dans Mobile Services :

-   **Prise en main des données** ( [C#][] / [JavaScript][] )

    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   **Prise en main de l'authentification** ( [C#][6] / [JavaScript][7] )

    En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

-   **Prise en main des notifications Push** ( [C#][8] / [JavaScript][9] )

    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

  [Windows Store]: /fr-fr/documentation/articles/mobile-services-windows-store-get-started "Windows Store"
  [Windows Phone]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started "Windows Phone"
  [iOS]: /fr-fr/documentation/articles/mobile-services-ios-get-started "iOS"
  [Android]: /fr-fr/documentation/articles/mobile-services-android-get-started "Android"
  [HTML]: /fr-fr/documentation/articles/mobile-services-html-get-started "HTML"
  [Xamarin.iOS]: /fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started "Xamarin.iOS"
  [Xamarin.Android]: /fr-fr/documentation/articles/partner-xamarin-mobile-services-android-get-started "Xamarin.Android"
  [Sencha]: /fr-fr/documentation/articles/partner-sencha-mobile-services-get-started/ "Sencha"
  [PhoneGap]: /fr-fr/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/ "PhoneGap"
  [Appcelerator]: /fr-fr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started "Appcelerator"
  [Serveur principal .NET]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/ ".NET backend"
  [JavaScript backend]: /fr-fr/documentation/articles/mobile-services-windows-store-get-started/ "JavaScript backend"
  [regarder le didacticiel]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services
  []: ./media/mobile-services-windows-store-get-started/mobile-quickstart-completed.png
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [Prise en main des données dans Mobile Services à l'aide de Visual Studio 2012]: /fr-fr/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012/
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-windows-store-get-started/mobile-portal-quickstart.png
  [2]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-steps.png
  [Visual Studio 2013 Express pour Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [3]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-startup.png
  [4]: ./media/mobile-services-windows-store-get-started/mobile-data-tab.png
  [5]: ./media/mobile-services-windows-store-get-started/mobile-data-browse.png
  [C#]: /fr-fr/develop/mobile/tutorials/get-started-with-data-dotnet
  [JavaScript]: /fr-fr/develop/mobile/tutorials/get-started-with-data-js
  [6]: /fr-fr/develop/mobile/tutorials/get-started-with-users-dotnet
  [7]: /fr-fr/develop/mobile/tutorials/get-started-with-users-js
  [8]: /fr-fr/develop/mobile/tutorials/get-started-with-push-dotnet
  [9]: /fr-fr/develop/mobile/tutorials/get-started-with-push-js
