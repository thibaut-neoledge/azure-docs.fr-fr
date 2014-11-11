<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app (legacy push)." metaCanonical="http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Prise en main des notifications Push dans Mobile Services (Push hérité)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store&nbsp;C#">Windows Store&nbsp;C#</a>
    <a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/fr-fr/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/fr-fr/documentation/articles/mobile-services-ios-get-started-push" title="iOS" class="current">iOS</a>
    <a href="/fr-fr/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
    <a href="/fr-fr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title=".NET backend">.NET backend</a> | <a href="/fr-fr/documentation/articles/mobile-services-ios-get-started-push/"  title="JavaScript backend" class="current">JavaScript backend</a></div>

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push vers une application iOS. Dans ce didacticiel, vous allez ajouter des notifications Push à l'aide du service de notifications Push Apple (APNS) au projet de démarrage rapide. Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.

> [WACOM.NOTE]Cette rubrique prend en charge les services mobiles *existants* qui n'ont *pas encore été mis à niveau* pour utiliser l'intégration de Notification Hubs. Lorsque vous créez un *nouveau* service mobile, cette fonctionnalité intégrée est automatiquement activée. Pour les nouveaux services mobiles, consultez la rubrique [Prise en main des notifications Push][Prise en main des notifications Push].
>
> Mobile Services s'intègre à Azure Notification Hubs pour prendre en charge une fonctionnalité de notification Push supplémentaire, comme les modèles, les plateformes multiples et la mise à l'échelle améliorée. *Vous devez mettre à niveau vos services mobiles existants pour utiliser Notification Hubs lorsque cela est possible*. Une fois la mise à niveau effectuée, consultez cette version de la [Prise en main des notifications Push][Prise en main des notifications Push].

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
-   [Xcode 4.5][Xcode 4.5]
-   Un appareil compatible iOS 5.0 (ou version ultérieure)
-   Un abonnement au programme pour développeurs iOS

> [WACOM.NOTE] En raison de la configuration requise pour les notifications Push, vous devez déployer et tester les notifications Push sur un appareil compatible iOS (iPhone ou iPad) au lieu d'un émulateur.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez effectuer le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services].

[WACOM.INCLUDE [Activation des notifications Push Apple](../includes/enable-apple-push-notifications.md)]

## Configuration de Mobile Services pour l'envoi de requêtes Push

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## Ajout de notifications Push à votre application

1.  Dans Xcode, ouvrez le fichier QSAppDelegate.h puis ajoutez la propriété suivante sous la propriété \***window** :

        @property (strong, nonatomic) NSString *deviceToken;

    > [WACOM.NOTE] Lorsque le schéma dynamique est activé pour votre service mobile, une nouvelle colonne « deviceToken » est ajouté automatiquement à la table **TodoItem** lorsqu'un nouvel élément contenant cette propriété est inséré.

2.  Dans QSAppDelegate.m, remplacez la méthode de gestionnaire suivante dans l'implémentation :

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3.  Dans QSAppDelegate.m, ajoutez la méthode de gestionnaire suivante à l'implémentation :

        // We are registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
            self.deviceToken = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
        }

4.  Dans QSAppDelegate.m, ajoutez la méthode de gestionnaire suivante à l'implémentation :

        // Handle any failure to register. In this case we set the deviceToken to an empty
        // string to prevent the insert from failing.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
            self.deviceToken = @"";
        }

5.  Dans QSAppDelegate.m, ajoutez la méthode de gestionnaire suivante à l'implémentation :

        // Because toast alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

6.  Dans QSTodoListViewController.m, importez le fichier QSAppDelegate.h pour pouvoir utiliser le délégué pour obtenir le jeton de l'appareil :

        #import "QSAppDelegate.h"

7.  Dans QSTodoListViewController.m, modifiez l'action **(IBAction)onAdd** en recherchant la ligne suivante :

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 

	Remplacez-la par le code suivant :

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : delegate.deviceToken
        };

    This adds a reference to the  **QSAppDelegate** to obtain the device token and then modifies the request payload to include that device token.

    > [WACOM.NOTE] Vous devez ajouter ce code avant d'appeler la méthode <strong>addItem</strong>.

L'application est mise à jour et prend en charge les notifications Push.

## Mise à jour du script de la fonction insert inscrite dans le portail de gestion

1.  Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

    ![][0]

2.  Dans **todoitem**, cliquez sur l'onglet **Script** et sélectionnez **Insérer**.

    ![][1]

    La fonction appelée lors d'une insertion dans la table **TodoItem** s'affiche.

3.  Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate toast notifications
            setTimeout(function() {
                push.apns.send(item.deviceToken, {
                    alert: "Toast: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    Ceci inscrit un nouveau script d'insertion, qui utilise [apns object][apns object] pour envoyer une notification Push (le texte inséré) à l'appareil fourni dans la demande d'insertion.

    > [WACOM.NOTE] Ce script reporte l'envoi de la notification pour vous laisser le temps de fermer l'application pour recevoir une notification toast.

## Test des notifications Push dans votre application

1.  Appuyez sur le bouton **Démarrer** pour générer le projet, puis démarrez l'application sur un appareil compatible iOS, et enfin cliquez sur **OK** pour accepter les notifications Push.

    ![][2]

    > [WACOM.NOTE] Vous devez accepter explicitement les notifications Push de votre application. Cette demande s'effectue uniquement lors du premier démarrage de l'application.

2.  Dans l'application, entrez un texte explicite, tel que *Nouvelle tâche Mobile Services* puis cliquez sur l'icône Ajouter (**+**).

    ![][3]

3.  Vérifiez que vous avez reçu une notification, puis cliquez sur **OK** pour fermer celle-ci.

    ![][4]

4.  Répétez l'étape 2 et fermez immédiatement l'application, puis vérifiez que la notification toast suivante est affichée.

    ![][5]

Vous avez terminé ce didacticiel.

## Étapes suivantes

Dans cet exemple simple, un utilisateur reçoit une notification Push avec les données venant d'être insérées. Le jeton de l'appareil utilisé par APNS est fourni au service mobile par le client dans la requête. Dans le didacticiel suivant, [Notifications Push pour les utilisateurs d'application][Notifications Push pour les utilisateurs d'application], vous allez créer une table Devices distincte dans laquelle stocker les jetons d'appareil et envoyer une notification Push à tous les canaux stockés en cas d'insertion.

  [Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
  [Génération de la demande de signature de certificat]: #certificates
  [Inscription de votre application et activation des notifications Push]: #register
  [Création d'un profil de mise en service pour l'application]: #profile
  [Configuration de Mobile Services]: #configure
  [Ajout de notifications Push à l'application]: #add-push
  [Mise à jour des scripts pour l'envoi de notifications Push]: #update-scripts
  [Insertion de données pour recevoir des notifications]: #test
  [Xcode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-ios
  [Activation des notifications Push Apple]: ../includes/enable-apple-push-notifications.md
  [mobile-services-apns-configure-push]: ../includes/mobile-services-apns-configure-push.md
  [0]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
  [1]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
  [apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
  [2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
  [3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
  [4]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
  [5]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
  [Notifications Push pour les utilisateurs d'application]: /fr-fr/develop/mobile/tutorials/push-notifications-to-users-ios
