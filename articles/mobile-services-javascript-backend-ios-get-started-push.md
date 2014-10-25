<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# Prise en main des notifications Push dans Mobile Services

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a><a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-ios-get-started-push" title="iOS" class="current">iOS</a><a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-android-get-started-push" title="Android" class="current">Android</a><!---<a href="/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>--></div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title=".NET backend" >.NET backend</a> | <a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="JavaScript backend" class="current">JavaScript backend</a></div>

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push vers une application iOS. Dans ce didacticiel, vous allez ajouter des notifications Push à l'aide du service de notifications Push Apple (APNS) au projet de démarrage rapide. Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.

> [WACOM.NOTE]Ce didacticiel démontre l'intégration Mobile Services avec Notification Hubs, qui permet d'envoyer des notifications Push à partir de votre service mobile. Si vous utilisez un service mobile plus ancien, qui utilise les notifications Push existantes et n'a pas encore été mis à niveau pour utiliser Notification Hubs, *nous vous recommandons d'effectuer la mise à niveau* dans le cadre de ce didacticiel. Si vous choisissez de ne pas effectuer la mise à niveau pour l'instant, vous devez suivre cette version du didacticiel : [Prise en main des notifications Push (existantes)][Prise en main des notifications Push (existantes)].

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Génération de la demande de signature de certificat][Génération de la demande de signature de certificat]
2.  [Inscription de votre application et activation des notifications Push][Inscription de votre application et activation des notifications Push]
3.  [Création d'un profil de mise en service pour l'application][Création d'un profil de mise en service pour l'application]
4.  [Configuration de Mobile Services][Configuration de Mobile Services]
5.  [Ajout de notifications Push à l'application][Ajout de notifications Push à l'application]
6.  [Mise à jour des scripts pour l'envoi de notifications Push][Mise à jour des scripts pour l'envoi de notifications Push]
7.  [Insertion de données pour recevoir des notifications][Insertion de données pour recevoir des notifications]

Ce didacticiel requiert les éléments suivants :

-   [Kit de développement logiciel (SDK) Mobile Services iOS][Kit de développement logiciel (SDK) Mobile Services iOS]
-   [XCode 4,5][XCode 4,5]
-   Un appareil compatible iOS 6,0 (ou version ultérieure)
-   Un abonnement au programme pour développeurs iOS

	> [WACOM.NOTE] En raison de la configuration requise pour les notifications Push, vous devez déployer et tester les notifications Push sur un appareil compatible iOS (iPhone ou iPad) au lieu d'un émulateur.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez effectuer le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services].

[WACOM.INCLUDE [Enable Apple Push Notifications][Enable Apple Push Notifications]]

## Configuration de Mobile Services pour l'envoi de requêtes Push

[WACOM.INCLUDE [mobile-services-apns-configure-push][mobile-services-apns-configure-push]]

## Ajout de notifications Push à votre application

1.  Dans QSAppDelegate.m, remplacez la méthode de gestionnaire suivante dans l'implémentation :

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

2.  Dans QSAppDelegate.m, ajoutez la méthode de gestionnaire suivante à l'implémentation :

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            client.push.registerNative(deviceToken, @”uniqueTag”);
        }

3.  Dans QSAppDelegate.m, ajoutez la méthode de gestionnaire suivante à l'implémentation :

        // Handle any failure to register. 
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

4.  Dans QSAppDelegate.m, ajoutez la méthode de gestionnaire suivante à l'implémentation :

        // Because alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

5.  Dans QSTodoListViewController.m, importez le fichier QSAppDelegate.h pour pouvoir utiliser le délégué pour obtenir le jeton de l'appareil :

        #import "QSAppDelegate.h"

6.  Dans QSTodoListViewController.m, modifiez l'action **(IBAction)onAdd** en recherchant la ligne suivante :

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 

	Remplacez-la par le code suivant :

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : [[NSString alloc] initWithData:delegate.deviceToken encoding:NSUTF8StringEncoding]
        };

	Ce code ajoute une référence au **QSAppDelegate** pour obtenir le jeton de l'appareil, puis modifie la charge utile de la demande pour inclure le jeton.

	> [WACOM.NOTE] Vous devez ajouter ce code avant l'appel de la méthode **addItem**.

L'application est mise à jour et prend en charge les notifications Push.

## Mise à jour du script de la fonction insert inscrite dans le portail de gestion

1.  Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

    ![][]

2.  Dans **todoitem**, cliquez sur l'onglet **Script** et sélectionnez **Insérer**.

    ![][1]

    La fonction appelée lors d'une insertion dans la table **TodoItem** s'affiche.

3.  Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate push notifications
            setTimeout(function() {
                push.apns.send(null, {
                    alert: "Alert: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    Ce code inscrit un nouveau script d'insertion, qui utilise l'[objet apns][objet apns] pour envoyer une notification Push (le texte inséré) à l'appareil fourni dans la demande d'insertion.

    > [WACOM.NOTE] Ce script reporte l'envoi de la notification pour vous laisser le temps de fermer l'application afin de recevoir une notification Push.

## Test des notifications Push dans votre application

1.  Appuyez sur le bouton **Démarrer** pour générer le projet, puis démarrez l'application sur un appareil compatible iOS, et enfin cliquez sur **OK** pour accepter les notifications Push.

    ![][2]

    > [WACOM.NOTE] Vous devez accepter explicitement les notifications Push de votre application. Cette demande s'effectue uniquement lors du premier démarrage de l'application.

2.  Dans l'application, entrez un texte explicite, tel que *Nouvelle tâche Mobile Services* puis cliquez sur l'icône Ajouter (**+**).

    ![][3]

3.  Vérifiez que vous avez reçu une notification, puis cliquez sur **OK** pour fermer celle-ci.

    ![][4]

4.  Répétez l'étape 2 et fermez immédiatement l'application, puis vérifiez que la notification Push suivante est affichée.

    ![][5]

Vous avez terminé ce didacticiel.

## Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application iOS pour utiliser Mobile Services et Notification Hubs afin d'envoyer des notifications Push. Ensuite, vous pouvez suivre l'un des didacticiels suivants :

-   [Envoi de notifications Push aux utilisateurs authentifiés][Envoi de notifications Push aux utilisateurs authentifiés]
    Apprenez à utiliser des balises pour envoyer des notifications Push à partir d'un service mobile uniquement à un utilisateur authentifié.

-   [Envoi de notifications de diffusion aux abonnés][Envoi de notifications de diffusion aux abonnés]
    Apprenez comment les utilisateurs peuvent s'inscrire et recevoir des notifications Push pour les catégories qui les intéressent.
    <!--- + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->
    Découvrez-en plus sur Mobile Services et les Hubs de notification dans les rubriques suivantes :

-   [Prise en main des données][Prise en main des données]
    En savoir plus sur le stockage et l'interrogation des données à l'aide des services mobiles.

-   [Prise en main de l'authentification][Prise en main de l'authentification]
    En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents utilisant des services mobiles.

-   [Présentation de Notification Hubs][Présentation de Notification Hubs]
    Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

-   [Guide de référence conceptuelle Mobile Services Objective-C][Guide de référence conceptuelle Mobile Services Objective-C]
     
    En savoir plus sur l'utilisation de Mobile Services avec Objective-C et iOS.

-   [Utilisation des scripts serveur dans Mobile Services][Utilisation des scripts serveur dans Mobile Services]
    En savoir plus sur la mise en œuvre d'une logique métier dans votre service mobile.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs.   -->

  [Windows Store C\#]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push "Windows Store C#"
  [Windows Store JavaScript]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push "Windows Store JavaScript"
  [Windows Phone]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push "Windows Phone"
  [iOS]: /fr-fr/documentation/articles/mobile-services-javascript-backend-ios-get-started-push "iOS"
  [Android]: /fr-fr/documentation/articles/mobile-services-javascript-backend-android-get-started-push "Android"
  [.NET backend]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ ".NET backend"
  [JavaScript backend]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ "JavaScript backend"
  [Prise en main des notifications Push (existantes)]: /fr-fr/documentation/articles/mobile-services-ios-get-started-push/
  [Génération de la demande de signature de certificat]: #certificates
  [Inscription de votre application et activation des notifications Push]: #register
  [Création d'un profil de mise en service pour l'application]: #profile
  [Configuration de Mobile Services]: #configure
  [Ajout de notifications Push à l'application]: #add-push
  [Mise à jour des scripts pour l'envoi de notifications Push]: #update-scripts
  [Insertion de données pour recevoir des notifications]: #test
  [Kit de développement logiciel (SDK) Mobile Services iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4,5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-ios-get-started
  [Enable Apple Push Notifications]: ../includes/enable-apple-push-notifications.md
  [mobile-services-apns-configure-push]: ../includes/mobile-services-apns-configure-push.md
  []: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
  [1]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
  [objet apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333
  [2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
  [3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
  [4]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
  [5]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
  [Envoi de notifications Push aux utilisateurs authentifiés]: /fr-fr/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/
  [Envoi de notifications de diffusion aux abonnés]: /fr-fr/documentation/articles/notification-hubs-ios-send-breaking-news/
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-ios-get-started-data
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-ios-get-started-users
  [Présentation de Notification Hubs]: /fr-fr/documentation/articles/notification-hubs-overview/
  [Guide de référence conceptuelle Mobile Services Objective-C]: /fr-fr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
  [Utilisation des scripts serveur dans Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
