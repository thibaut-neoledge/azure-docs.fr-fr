<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Prise en main des notifications Push dans Mobile Services

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Windows Phone">Windows Phone</a><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="iOS" class="current">iOS</a><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Android" class="current">Android</a>
<!-- <a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
</div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title=".NET backend" class="current">.NET backend</a> | <a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/"  title="JavaScript backend">JavaScript backend</a></div>

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push vers une application iOS. Dans ce didacticiel, vous allez ajouter des notifications Push à l'aide du service de notifications Push Apple (APNS) au projet de démarrage rapide. Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Génération de la demande de signature de certificat][Génération de la demande de signature de certificat]
2.  [Inscription de votre application et activation des notifications Push][Inscription de votre application et activation des notifications Push]
3.  [Création d'un profil de mise en service pour l'application][Création d'un profil de mise en service pour l'application]
4.  [Téléchargement du service en local][Téléchargement du service en local]
5.  [Test du service mobile][Test du service mobile]
6.  [Mise à jour du serveur pour l'envoi de notifications Push][Mise à jour du serveur pour l'envoi de notifications Push]
7.  [Publication du service mobile sur Azure][Publication du service mobile sur Azure]
8.  [Ajout de notifications Push à l'application][Ajout de notifications Push à l'application]
9.  [Mise à jour des scripts pour l'envoi de notifications Push][Mise à jour des scripts pour l'envoi de notifications Push]
10. [Activation des notifications push pour les tests en local][Activation des notifications push pour les tests en local]
11. [Test de l'application avec le service mobile publié][Test de l'application avec le service mobile publié]

Ce didacticiel requiert les éléments suivants :

-   [Kit de développement logiciel (SDK) Mobile Services iOS][Kit de développement logiciel (SDK) Mobile Services iOS]
-   [XCode 4,5][XCode 4,5]
-   Un appareil compatible iOS 6,0 (ou version ultérieure)
-   Un abonnement au programme pour développeurs iOS

	> [WACOM.NOTE] En raison de la configuration requise pour les notifications Push, vous devez déployer et tester les notifications Push sur un appareil compatible iOS (iPhone ou iPad) au lieu d'un émulateur.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez effectuer le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services].

[WACOM.INCLUDE [Activation des notification Push Apple](../includes/enable-apple-push-notifications.md)]

## Configuration de Mobile Services pour l'envoi de requêtes Push

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## <a name="download-the-service"></a><span class="short-header">Téléchargement du service</span>Téléchargement du service sur votre ordinateur local

[WACOM.INCLUDE [mobile-services-ios-download-service-locally](../includes/mobile-services-ios-download-service-locally.md)]

## <a name="test-the-service"></a><span class="short-header">Test du service</span>Test du service mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## <span id="update-server"></span></a>Mise à jour du serveur pour l'envoi de notifications push

1.  Dans l'Explorateur de solutions de Visual Studio, développez le dossier **Contrôleurs** du projet de service mobile. Ouvrez TodoItemController.cs. Au début du fichier, ajoutez l'instruction `using` suivante :

        using System;
        using System.Collections.Generic;

2.  Mettez à jour la définition de méthode `PostTodoItem` avec le code suivant :

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            ApplePushMessage message = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));

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

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

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

	Ceci ajoute une référence au **QSAppDelegate** pour obtenir le jeton de l'appareil, puis modifie la charge utile de la demande pour inclure de jeton.

	> [WACOM.NOTE] Vous devez ajouter ce code avant l'appel de la méthode **addItem**.

L'application est mise à jour et prend en charge les notifications Push.

## <span id="local-testing"></span></a>Activation des notifications push pour les tests en local

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## Test des notifications Push dans votre application

1.  Appuyez sur le bouton **Démarrer** pour générer le projet, puis démarrez l'application sur un appareil compatible iOS, et enfin cliquez sur **OK** pour accepter les notifications Push.

    ![][0]

    > [WACOM.NOTE] Vous devez accepter explicitement les notifications Push de votre application. Cette demande s'effectue uniquement lors du premier démarrage de l'application.

2.  Dans l'application, entrez un texte explicite, tel que *Nouvelle tâche Mobile Services* puis cliquez sur l'icône Ajouter (**+**).

    ![][1]

3.  Vérifiez que vous avez reçu une notification, puis cliquez sur **OK** pour fermer celle-ci.

    ![][2]

4.  Répétez l'étape 2 et fermez immédiatement l'application, puis vérifiez que la notification Push suivante est affichée.

    ![][3]

Vous avez terminé ce didacticiel.

## Étapes suivantes

Ce didacticiel a présenté les bases de la procédure à suivre pour permettre à une application iOS d'utiliser Mobile Services et Notification Hubs pour envoyer des notifications Push. Vous pouvez maintenant passer au didacticiel suivant, [Envoi de notifications Push aux utilisateurs authentifiés][Envoi de notifications Push aux utilisateurs authentifiés], qui explique comment utiliser des balises pour envoyer des notifications Push d'un service mobile à un utilisateur authentifié.

<!--+ [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

-   [Prise en main des données][Prise en main des données]
    
	En savoir plus sur le stockage et l'interrogation des données à l'aide des services mobiles.

-   [Prise en main de l'authentification][Prise en main de l'authentification]
    
	En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents utilisant des services mobiles.

-   [Présentation de Notification Hubs][Présentation de Notification Hubs]
    
	Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.



  [Génération de la demande de signature de certificat]: #certificates
  [Inscription de votre application et activation des notifications Push]: #register
  [Création d'un profil de mise en service pour l'application]: #profile
  [Téléchargement du service en local]: #download-the-service-locally
  [Test du service mobile]: #test-the-service
  [Mise à jour du serveur pour l'envoi de notifications Push]: #update-server
  [Publication du service mobile sur Azure]: #publish-mobile-service
  [Ajout de notifications Push à l'application]: #add-push
  [Mise à jour des scripts pour l'envoi de notifications Push]: #update-scripts
  [Activation des notifications push pour les tests en local]: #local-testing
  [Test de l'application avec le service mobile publié]: #test-app
  [XCode 4,5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started
  [Activation des notification Push Apple]: ../includes/enable-apple-push-notifications.md
  [mobile-services-apns-configure-push]: ../includes/mobile-services-apns-configure-push.md
  [mobile-services-ios-download-service-locally]: ../includes/mobile-services-ios-download-service-locally.md
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [0]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
  [1]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
  [2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
  [3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
  [Envoi de notifications Push aux utilisateurs authentifiés]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
  [Présentation de Notification Hubs]: /fr-fr/documentation/articles/notification-hubs-overview/
