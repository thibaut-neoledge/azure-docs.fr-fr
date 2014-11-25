<properties linkid="develop-mobile-tutorials-get-started-with-push-js-vs2013" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (Android JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android JavaScript app." metaCanonical="http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal"  solutions="" writer="ricksal" manager="" editor=""   />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# <a name="getting-started-with-push"> </a>Prise en main des notifications Push dans Mobile Services

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a><a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-ios-get-started-push" title="iOS">iOS</a><a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-android-get-started-push" title="Android" class="current">Android</a>
<!-- <a href="/fr-fr/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/fr-fr/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title=".NET backend" >.NET backend</a> | <a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-android-get-started-push/" title="JavaScript backend" class="current">JavaScript backend</a></div>

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push à une application Android. Dans ce didacticiel, vous ajoutez des notifications Push au projet de démarrage rapide à l'aide du service Google Cloud Messaging (GCM). Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.

> [WACOM.NOTE]Ce didacticiel présente l'intégration de Mobile Services à Notification Hubs, ce qui correspond à la façon dont vous envoyez des notifications Push depuis votre service mobile. Si vous utilisez un service mobile plus ancien qui utilise un push hérité et qui n'a pas encore été mis à jour pour utiliser Notification Hubs, *il est recommandé d'effectuer une mise à niveau* dans le cadre de ce didacticiel. Si vous décidez de ne pas effectuer de mise à niveau maintenant, vous devez suivre cette version du didacticiel : [Prise en main des notifications Push (hérité)][Prise en main des notifications Push (hérité)].

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Activation de Google Cloud Messaging][Activation de Google Cloud Messaging]
2.  [Configuration de Mobile Services][Configuration de Mobile Services]
3.  [Ajout de notifications Push à votre application][Ajout de notifications Push à votre application]
4.  [Mise à jour des scripts pour l'envoi de notifications Push][Mise à jour des scripts pour l'envoi de notifications Push]
5.  [Insertion de données pour recevoir des notifications][Insertion de données pour recevoir des notifications]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de le lancer, suivez d'abord le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services] ou [Prise en main des données][Prise en main des données] pour connecter votre projet au service mobile.

## <span id="register"></span></a>Activation de Google Cloud Messaging

> [WACOM.NOTE]Pour effectuer cette procédure, vous devez disposer d'un compte Google avec une adresse électronique vérifiée. Pour créer un compte Google, consultez la page [accounts.google.com][accounts.google.com].

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Ensuite, vous allez utiliser cette valeur de clé API afin d'activer Mobile Services pour l'authentification avec GCM et l'envoi des notifications Push au nom de votre application.

## <span id="configure"></span></a>Configuration de Mobile Services pour l'envoi de demandes push

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure], cliquez sur **Mobile Services**, puis sur l'application.

    ![][0]

2.  Cliquez sur l'onglet **Push**, entrez la valeur de **API Key** fournie par GCM dans la procédure précédente, puis cliquez sur **Save**.

    > [WACOM.NOTE]Si vous suivez ce didacticiel en utilisant un ancien service mobile, vous verrez peut-être un lien au bas de l'onglet **Push** indiquant **Activer la transmission push améliorée**. Cliquez dessus pour mettre à niveau votre service mobile et l'intégrer à Notification Hubs. Cette modification est irréversible. Pour plus d'informations sur l'activation de notifications Push améliorées dans un service mobile de production, consultez [ce guide][ce guide].

    ![][1]

    <div class="dev-callout"><b>Important</b>
<p>Lorsque vous d&eacute;finissez vos informations d'identification GCM pour les notifications Push am&eacute;lior&eacute;es dans l'onglet Push du portail, celles-ci sont partag&eacute;es avec Notification Hubs pour configurer le concentrateur de notification avec votre application.</p>
</div>

Votre service mobile et votre application sont désormais configurés pour fonctionner avec GCM et Notification Hubs.

## <span id="add-push"></span></a>Ajout de notifications push à votre application

### Vérification de la version du Kit de développement logiciel (SDK) Android

[WACOM.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version.md)]

L'étape suivante consiste à installer les services Google Play. Google Cloud Messaging a des spécifications requises d'API minimales pour le développement et les tests, auxquelles la propriété **minSdkVersion** du manifeste doit se conformer.

Si vous envisagez de procéder à un test avec un appareil ancien, consultez la rubrique [Configuration du Kit de développement logiciel (SDK) des services Google Play][Configuration du Kit de développement logiciel (SDK) des services Google Play] pour déterminer comment définir cette valeur.

### Ajout de services Google Play au projet

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

### Ajout de code

[WACOM.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push.md)]

## <span id="update-scripts"></span></a>Mise à jour du script d'insertion inscrit dans le portail de gestion

1.  Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

    ![][2]

2.  Dans **todoitem**, cliquez sur l'onglet **Script** et sélectionnez **Insérer**.

    ![][3]

    La fonction appelée lors d'une insertion dans la table **TodoItem** s'affiche.

3.  Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

        function insert(item, user, request) {
        // Define a payload for the Google Cloud Messaging toast notification.
        var payload = 
            '{"data":{"message" : ' + item.text + ' }}';

        request.execute({
            success: function() {
                // If the insert succeeds, send a notification.
                push.gcm.send(null, payload, {
                    success: function(pushResponse) {
                        console.log("Sent push:", pushResponse, payload);
                        request.respond();
                        },              
                    error: function (pushResponse) {
                        console.log("Error Sending push:", pushResponse);
                        request.respond(500, { error: pushResponse });
                        }
                    });
                },
            error: function(err) {
                console.log("request.execute error", err)
                request.respond();
            }
          });
        }

    Cela enregistre un nouveau script d'insertion qui utilise l'[objet gcm][objet gcm] pour envoyer une notification Push à tous les appareils enregistrés après l'insertion.

## <span id="test"></span></a>Test des notifications Push dans votre application

Vous pouvez tester l'application en connectant directement un téléphone Android via un câble USB, ou en utilisant un appareil virtuel dans l'émulateur.

### Configuration de l'émulateur pour le test

Lorsque vous exécutez cette application dans l'émulateur, veillez à utiliser un AVD (appareil virtuel Android) qui prend en charge les API Google.

1.  Redémarrez Eclipse, puis dans l'Explorateur de package, cliquez avec le bouton droit sur le projet, cliquez sur **Propriétés** et sur **Android**, activez **API Google** et cliquez sur **OK**.

    ![][4]

    Cela permet de cibler le projet pour les API Google.

2.  Dans **Fenêtre**, sélectionnez **Gestionnaire d'appareil virtuel Android**, choisissez votre appareil, puis cliquez sur **Modifier**.

    ![][5]

3.  Sélectionnez **API Google** dans **Cible**, puis cliquez sur OK.

    ![][6]

    Cela permet de cibler l'AVD pour utiliser les API Google.

### Exécution du test

1.  Dans le menu **Exécuter** d'Eclipse, cliquez sur **Exécuter** pour démarrer l'application.

2.  Dans l'application, tapez un texte explicite, comme *Nouvelle tâche Mobile Services* puis cliquez sur le bouton **Ajouter**.

    ![][7]

3.  Faites glisser votre doigt du haut vers le bas de l'écran pour ouvrir le centre de notifications de l'appareil et afficher la notification.

Vous avez terminé ce didacticiel.

## <a name="next-steps"> </a>Étapes suivantes

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

-   [Prise en main des données][Prise en main des données]

    En savoir plus sur le stockage et l'interrogation des données à l'aide des services mobiles.

-   [Prise en main de l'authentification][Prise en main de l'authentification]

    En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents utilisant des services mobiles.

-   [Présentation de Notification Hubs][Présentation de Notification Hubs]

    Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

-   [Utilisation de la bibliothèque cliente Android pour Mobile Services][Utilisation de la bibliothèque cliente Android pour Mobile Services]

    En savoir plus sur l'utilisation de Mobile Services avec Android.

-   [Référence de script serveur Mobile Services][Référence de script serveur Mobile Services]

    En savoir plus sur l'implémentation de logique métier dans votre service mobile.

  [Activation de Google Cloud Messaging]: #register
  [Configuration de Mobile Services]: #configure
  [Ajout de notifications Push à votre application]: #add-push
  [Mise à jour des scripts pour l'envoi de notifications Push]: #update-scripts
  [Insertion de données pour recevoir des notifications]: #test
  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-android-get-started/
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-android-get-started-data/
  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
  [ce guide]: http://go.microsoft.com/fwlink/p/?LinkId=391951
  [1]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
  [2]: ./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png
  [3]: ./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png
  [objet gcm]: http://go.microsoft.com/fwlink/p/?LinkId=282645
  [4]: ./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png
  [5]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
  [6]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
  [7]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-android-get-started-users
  [Présentation de Notification Hubs]: /fr-fr/documentation/articles/notification-hubs-overview/
  [Utilisation de la bibliothèque cliente Android pour Mobile Services]: /fr-fr/documentation/articles/mobile-services-android-how-to-use-client-library
  [Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
