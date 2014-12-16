<properties urlDisplayName="Get Started with Push (iOS)" pageTitle="Prise en main des notifications Push (iOS) | Centre de développement mobile" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Ajout de notifications Push à votre application Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push vers une application iOS via le service de notifications Push Apple (APNS). Dans ce didacticiel, vous allez activer des notifications Push dans le [projet de démarrage rapide](http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-ios-get-started/) à l'aide d'Azure Notification Hubs. Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1. [Génération de la demande de signature de certificat]
2. [Inscription de votre application et activation des notifications Push]
3. [Création d'un profil de mise en service pour l'application]
4. [Configuration de Mobile Services]
5. [Ajout de notifications Push à l'application]
6. [Mise à jour des scripts pour l'envoi de notifications Push]
7. [Insertion de données pour recevoir des notifications]

Ce didacticiel requiert les éléments suivants :

+ [Kit de développement logiciel (SDK) Mobile Services iOS]
+ [XCode 4.5][Installation de Xcode]
+ Un appareil compatible iOS 6,0 (ou version ultérieure)
+ Un abonnement au programme pour développeurs iOS

   > [WACOM.NOTE] En raison de la configuration requise pour les notifications Push, vous devez déployer et tester les notifications Push sur un appareil compatible iOS (iPhone ou iPad) au lieu d'un émulateur.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez effectuer le didacticiel [Prise en main de Mobile Services].


[WACOM.INCLUDE [Activation des notifications Push Apple](../includes/enable-apple-push-notifications.md)]


## Configuration de Mobile Services pour l'envoi de requêtes Push

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## Ajout de notifications Push à votre application

1. Dans QSAppDelegate.m, insérez l'extrait de code suivant pour importer le Kit de développement logiciel (SDK) Mobile Services iOS :

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>

2. Dans QSAppDelegate.m, remplacez la méthode de gestionnaire suivante dans l'implémentation :

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3. Dans QSAppDelegate.m, ajoutez la méthode de gestionnaire suivante à l'implémentation : Veillez à copier les valeurs URL du service mobile et Clé d'application, et les utiliser à la place des espaces réservés :

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            // TODO: update @"MobileServiceUrl" and @"AppKey" placeholders
			MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"];

            [client.push registerNativeWithDeviceToken:deviceToken tags:@[@"uniqueTag"] completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

4. Dans QSAppDelegate.m, ajoutez la méthode de gestionnaire suivante à l'implémentation :

        // Handle any failure to register.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

5. Dans QSAppDelegate.m, ajoutez la méthode de gestionnaire suivante à l'implémentation :  

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

L'application est mise à jour et prend en charge les notifications Push.

## Mise à jour du script de la fonction insert inscrite dans le portail de gestion

1. Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

   	![][21]

2. Dans **todoitem**, cliquez sur l'onglet **Script**, puis sélectionnez **Insérer**.

  	![][22]

   	La fonction appelée lors d'une insertion dans la table **TodoItem** s'affiche.

3. Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

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

   	Ceci inscrit un nouveau script d'insertion, qui utilise [apns object] pour envoyer une notification Push (le texte inséré) à l'appareil fourni dans la demande d'insertion.


   	> [WACOM.NOTE] Ce script reporte l'envoi de la notification pour vous laisser le temps de fermer l'application pour recevoir une notification Push.

## Test des notifications Push dans votre application

1. Appuyez sur le bouton **Exécuter** pour générer le projet, puis démarrez l'application sur un appareil compatible iOS, et enfin cliquez sur **OK** pour accepter les notifications Push.

  	![][23]

    > [WACOM.NOTE] Vous devez accepter explicitement les notifications Push de votre application. Cette demande s'effectue uniquement lors du premier démarrage de l'application.

2. Dans l'application, entrez un texte explicite, tel que _Nouvelle tâche Mobile Services_ puis cliquez sur l'icône Ajouter (**+**).

  	![][24]

3. Vérifiez que vous avez reçu une notification, puis cliquez sur **OK** pour fermer celle-ci.

  	![][25]

4. Répétez l'étape 2 et fermez immédiatement l'application, puis vérifiez que la notification Push suivante est affichée.

  	![][26]

Vous avez terminé ce didacticiel.

## Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application iOS pour utiliser Mobile Services et Notification Hubs afin d'envoyer des notifications Push. Nous vous invitons maintenant à suivre l'un des didacticiels suivants :

+ [Envoi de notifications Push aux utilisateurs authentifiés]
	<br/>En savoir plus sur l'utilisation de balises pour envoyer des notifications Push depuis un service mobile vers des utilisateurs authentifiés uniquement.

+ [Envoi de notifications aux abonnés]
	<br/>En savoir plus sur l'inscription des utilisateurs et la réception des notifications Push pour les catégories qui les intéressent.
<!---
+ [Send template-based notifications to subscribers]
	<br/>En savoir plus sur l'utilisation de modèles pour envoyer des notifications Push depuis un service mobile, sans avoir à gérer des charges utiles propres à la plateforme sur votre serveur principal.
-->
Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

* [Prise en main des données]
  <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de services mobiles.

* [Prise en main de l'authentification]
  <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents à l'aide de services mobiles.

* [Présentation de Notification Hubs]
  <br/>Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

* [Débogage des applications Notification Hubs](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Instructions de dépannage et de débogage des solutions Notification Hubs. 

* [Guide de fonctionnement Mobile Services Objective-C]
  <br/>En savoir plus sur l'utilisation de Mobile Services avec Objective-C et iOS.

* [Référence de script serveur Mobile Services]
  <br/>En savoir plus sur l'implémentation d'une logique métier dans votre service mobile.

<!-- Anchors. -->
[Génération de la demande de signature de certificat]: #certificates
[Inscription de votre application et activation des notifications Push]: #register
[Création d'un profil de mise en service pour l'application]: #profile
[Configuration de Mobile Services]: #configure
[Mise à jour des scripts pour l'envoi de notifications Push]: #update-scripts
[Ajout de notifications Push à l'application]: #add-push
[Insertion de données pour recevoir des notifications]: #test
[Étapes suivantes]:#next-steps

<!-- Images. -->
[5]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png
[17]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

<!-- URLs.   -->
[Installation de Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Portail d'approvisionnement iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Kit de développement logiciel (SDK) Mobile Services iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Service de notifications Push Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-ios-get-started
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-ios-get-started-data
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-ios-get-started-users
[Portail de gestion Azure]: https://manage.windowsazure.com/
[objet apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333

[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293

[Envoi de notifications Push aux utilisateurs authentifiés]: /fr-fr/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/

[Présentation de Notification Hubs]: /fr-fr/documentation/articles/notification-hubs-overview/
[Envoi de notifications aux abonnés]: /fr-fr/documentation/articles/notification-hubs-ios-send-breaking-news/
[Envoi de notifications basées sur un modèle aux abonnés]: /fr-fr/documentation/articles/notification-hubs-ios-send-localized-breaking-news/

[Guide de fonctionnement Mobile Services Objective-C]: /fr-fr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
