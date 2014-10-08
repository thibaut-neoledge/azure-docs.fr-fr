<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-push-android" urlDisplayName="Get Started with Push" pageTitle="Get started with push (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android .Net app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Prise en main des notifications Push dans Mobile Services

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title="Windows Store C#">Windows Store C#</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Windows Phone">Windows Phone</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="iOS">iOS</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Android" class="current">Android</a>
<!---<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title=".NET backend" class="current">.NET backend</a> | <a href="/en-us/documentation/articles/mobile-services-javascript-backend-android-get-started-push/"  title="JavaScript backend">JavaScript backend</a></div>

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push à une application Android. Dans ce didacticiel, vous ajoutez des notifications Push au projet de démarrage rapide à l'aide du service Google Cloud Messaging (GCM). Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.

Ce didacticiel vous familiarise avec les étapes suivantes :

1.  [Activation de Google Cloud Messaging][]
2.  [Configuration du service mobile pour l'envoi de demandes push][]
3.  [Mise à jour du serveur pour l'envoi de notifications Push][]
4.  [Ajout de notifications Push à votre application][]
5.  [Activation des notifications push pour les tests en local][]
6.  [Test de l'application avec le service mobile publié][]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de le lancer, suivez d'abord le didacticiel [Prise en main de Mobile Services][] ou [Prise en main des données][] pour connecter votre projet au service mobile. En tant que tel, ce didacticiel requiert également Visual Studio 2013.

> [WACOM.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][].

## <span id="register"></span></a>Activation de Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM][]]

## <span id="configure"></span></a>Configuration de Mobile Services pour l'envoi de demandes push

1.  Connectez-vous au [portail de gestion Azure][], cliquez sur **Mobile Services**, puis sur l'application.

    ![][]

2.  Cliquez sur l'onglet **Push**, puis entrez la valeur de **Clé API** fournie par GCM dans la procédure précédente, puis cliquez sur **Enregistrer**.

    ![][1]

    <div class="dev-callout"><b>Important</b>
<p>Lorsque vous d&eacute;finissez vos informations d'identification GCM pour les notifications Push am&eacute;lior&eacute;es dans l'onglet Push du portail, celles-ci sont partag&eacute;es avec Notification Hubs pour configurer le concentrateur de notification avec votre application.</p>
</div>

Votre service mobile est désormais configuré pour utiliser GCM et Notification Hubs.

## <a name="download-the-service"></a><span class="short-header">Téléchargement du service</span>Téléchargement du service sur votre ordinateur local

[WACOM.INCLUDE [mobile-services-download-service-locally][]]

## <a name="test-the-service"></a><span class="short-header">Test du service</span>Test du service mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][]]

## <span id="update-server"></span></a>Mise à jour du serveur pour l'envoi de notifications push

1.  Dans l'Explorateur de solutions de Visual Studio, développez le dossier **Contrôleurs** du projet de service mobile. Ouvrez TodoItemController.cs. Au début du fichier, ajoutez l'instruction `using` suivante :

        using System;
        using System.Collections.Generic;

2.  Mettez à jour la définition de méthode `PostTodoItem` avec le code suivant :

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };
            GooglePushMessage message = new GooglePushMessage(data, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Ce code envoie une notification Push (avec le texte de l'élément inséré) après l'insertion d'un élément ToDo. En cas d'erreur, le code ajoute une entrée dans le journal des erreurs consultable à partir de l'onglet **Journaux** du service mobile sur le portail de gestion.

## <a name="publish-the-service"></a><span class="short-header">Publication du service</span>Publication du service mobile sur Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][]]

## <a name="update-app"></a>Ajout de notifications push à votre application

### Vérification de la version du Kit de développement logiciel (SDK) Android

[WACOM.INCLUDE [mobile-services-verify-android-sdk-version][]]

L'étape suivante consiste à installer les services Google Play. Google Cloud Messaging a des spécifications requises d'API minimales pour le développement et les tests, auxquelles la propriété **minSdkVersion** du manifeste doit se conformer.

Si vous envisagez de procéder à un test avec un appareil ancien, consultez la rubrique [Configuration du Kit de développement logiciel (SDK) des services Google Play] pour déterminer comment définir cette valeur et définissez-la correctement.

### Ajout de services Google Play au projet

[WACOM.INCLUDE [Add Play Services][]]

### Ajout de code

[WACOM.INCLUDE [mobile-services-android-getting-started-with-push][]]

## <a name="test-app"></a><span class="short-header">Test de l'application</span>Test de l'application avec le service mobile publié

Vous pouvez tester l'application en connectant directement un téléphone Android via un câble USB, ou en utilisant un appareil virtuel dans l'émulateur.

### Si vous utilisez l'émulateur à des fins de test...

Assurez-vous d'utiliser un appareil virtuel Android (AVD) prenant en charge les API Google.

1.  Dans **Fenêtre**, sélectionnez **Gestionnaire d'appareil virtuel Android**, sélectionnez votre appareil, cliquez sur **Modifier** (ou **Nouveau** si vous n'avez aucun appareil).

    ![][2]

2.  Sélectionnez **API Google** (ou **API Google x86**) dans **Cible**, puis cliquez sur OK.

    ![][3]

    Cela permet de cibler l'AVD pour utiliser les API Google. Si vous avez installé plusieurs versions du Kit de développement logiciel (SDK) Android, vérifiez que le niveau d'API correspond à celui que vous avez défini précédemment dans les propriétés de projet.

### <span id="local-testing"></span></a>Activation des notifications push pour les tests en local

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push][]]

### Exécution du test

1.  Dans le menu **Exécuter** d'Eclipse, cliquez sur **Exécuter** pour démarrer l'application.

2.  Dans l'application, tapez un texte explicite, comme *Nouvelle tâche Mobile Services* puis cliquez sur le bouton **Ajouter**.

    ![][4]

3.  Faites glisser votre doigt du haut vers le bas de l'écran pour ouvrir le centre de notifications de l'appareil et afficher la notification.

Vous avez terminé ce didacticiel.

## <a name="next-steps"> </a>Étapes suivantes

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.   + [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

-   [Prise en main des données][]
    En savoir plus sur le stockage et l'interrogation des données à l'aide des services mobiles.

-   [Prise en main de l'authentification][]
    En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents utilisant des services mobiles.

-   [Présentation de Notification Hubs][]
    Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

-   [Utilisation de la bibliothèque cliente Android pour Mobile Services][]
    En savoir plus sur l'utilisation de Mobile Services avec Android.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [Windows Store C\#]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/ "Windows Store C#"
  [Windows Store JavaScript]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/ "Windows Store JavaScript"
  [Windows Phone]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/ "Windows Phone"
  [iOS]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ "iOS"
  [Android]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ "Android"
  [.NET backend]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ ".NET backend"
  [JavaScript backend]: /en-us/documentation/articles/mobile-services-javascript-backend-android-get-started-push/ "JavaScript backend"
  [Activation de Google Cloud Messaging]: #register
  [Configuration du service mobile pour l'envoi de demandes push]: #configure
  [Mise à jour du serveur pour l'envoi de notifications Push]: #update-server
  [Ajout de notifications Push à votre application]: #update
  [Activation des notifications push pour les tests en local]: #local-testing
  [Test de l'application avec le service mobile publié]: #test-app
  [Prise en main de Mobile Services]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started
  [Prise en main des données]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F
  [Enable GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [portail de gestion Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
  [1]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
  [mobile-services-download-service-locally]: ../includes/mobile-services-download-service-locally.md
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [mobile-services-verify-android-sdk-version]: ../includes/mobile-services-verify-android-sdk-version.md
  [Add Play Services]: ../includes/mobile-services-add-Google-play-services.md
  [mobile-services-android-getting-started-with-push]: ../includes/mobile-services-android-getting-started-with-push.md
  [2]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
  [3]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [4]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
  [Prise en main de l'authentification]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
  [Présentation de Notification Hubs]: /en-us/documentation/articles/notification-hubs-overview/
  [Utilisation de la bibliothèque cliente Android pour Mobile Services]: /en-us/documentation/articles/mobile-services-android-how-to-use-client-library
