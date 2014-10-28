<properties pageTitle="Get Started with Mobile Services for Xamarin.Android" metaKeywords="Azure Xamarin.Android application, mobile service XamarinAndroid, getting started Azure Xamarin.Android" writer="craigd" description="Learn how to use Azure Mobile Services with your Xamarin.Android app." metaCanonical="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="donnam" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="donnam"></tags>

# <a name="getting-started"></a>Prise en main de Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started][]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Ce didacticiel vous montre comment ajouter un service principal cloud &agrave; une application Xamarin.Android en utilisant Azure Mobile Services. Dans ce didacticiel, vous allez cr&eacute;er un service mobile et une simple application <em>To do list</em> qui stocke les donn&eacute;es d'application dans le nouveau service mobile.</p>
<p>Voici une capture d'&eacute;cran de l'application termin&eacute;e&nbsp;:</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-xamarin-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vid&eacute;o</span></a> <span class="time">10:05:00</span></div>

</div>

![][]

Pour suivre ce didacticiel, vous avez besoin de [Xamarin.Android][], qui installe Xamarin Studio et un plug-in Visual Studio (sous Windows), ainsi que la dernière plateforme Android. Le Kit de développement logiciel (SDK) Android 4.2 ou une version ultérieure est nécessaire.

Le projet de démarrage rapide téléchargé contient le composant Azure Mobile services pour Xamarin.Android. Même si ce projet cible Android 4.2 ou une version ultérieure, le Kit de développement logiciel (SDK) Mobile Services n'a besoin que d'Android 2.2 (ou une version ultérieure).

<div class="dev-callout"><strong>Remarque</strong> <p>Pour effectuer ce didacticiel, vous avez besoin d&rsquo;un compte Azure. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une &eacute;valuation d'Azure et obtenir jusqu'&agrave; 10&nbsp;services mobiles gratuits que vous pourrez conserver apr&egrave;s l'expiration de votre p&eacute;riode d'&eacute;valuation. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A9C9624B5" target="_blank">Version d'&eacute;valuation gratuite d'Azure</a>.</p></div>

## <a name="create-new-service"> </a>Création d'un service mobile

[WACOM.INCLUDE [mobile-services-create-new-service][]]

## 

## <span class="short-header">Création d'une application</span>Création d'une application Xamarin.Android

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application Xamarin.Android connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Sous l'onglet de démarrage rapide, cliquez sur **Xamarin.Android** sous **Choisissez une plateforme** et développez **Créer une application Android**.

    ![][1]

    Les trois étapes permettant de créer une application Xamarin.Android connectée à votre service mobile s'affichent.

    ![][2]

3.  Cliquez sur **Create TodoItem table** pour créer une table permettant de stocker les données d'application.

4.  Sous **Download and run app**, cliquez sur **Télécharger**.

    Cette opération télécharge le projet de votre exemple d'application *To do list* qui est connectée à votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

## Exécution de votre application Android

La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

1.  Accédez à l'emplacement où vous avez enregistré les fichiers projet compressés, puis développez les fichiers sur votre ordinateur.

2.  Dans Xamarin Studio ou Visual Studio, cliquez sur **Fichier**, puis sur **Ouvrir**, accédez aux exemples de fichiers non compressés et sélectionnez **XamarinTodoQuickStart.Android.sln** pour l'ouvrir.

    ![][3]

    ![][4]

3.  Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l'application. Vous êtes invité à sélectionner un émulateur ou un périphérique USB connecté.

    <div class="dev-callout"><strong>Remarque</strong> <p>Afin d'ex&eacute;cuter le projet dans l'&eacute;mulateur Android, vous devez d&eacute;finir au moins un AVD (appareil virtuel Android). Utilisez le gestionnaire AVD pour cr&eacute;er et g&eacute;rer ces appareils.</p></div>

4.  Dans l'application, tapez un texte explicite, comme *Suivre le didacticiel*, puis cliquez sur **Ajouter**.

    ![][5]

    Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la liste.

    > [WACOM.NOTE]
    > Vous pouvez vérifier le code (se trouvant dans le fichier C# ToDoActivity.cs) qui accède au service mobile pour exécuter une requête et insérer des données.

5.  De retour dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItems**.

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
  [regarder le didacticiel]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services
  []: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-completed-android.png
  [Xamarin.Android]: http://xamarin.com/download
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A9C9624B5
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-portal-quickstart-xamarin-android.png
  [2]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-steps-xamarin-android.png
  [3]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-xs.png
  [4]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-vs.png
  [5]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-startup-android.png
  [6]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-tab.png
  [7]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-browse.png
  [Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-android
  [Prise en main de la synchronisation des données hors connexion]: /fr-fr/documentation/articles/mobile-services-xamarin-android-get-started-offline-data
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-xamarin-android
  [Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-xamarin-android
