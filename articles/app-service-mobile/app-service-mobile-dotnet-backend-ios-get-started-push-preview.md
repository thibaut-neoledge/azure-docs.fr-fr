<properties 
	pageTitle="Ajout de notifications push à votre application iOS à l'aide d'Azure App Service" 
	description="Découvrez comment utiliser Azure App Service pour envoyer des notifications push à votre application iOS." 
	services="app-service\mobile" 
	documentationCenter="ios" 
	manager="dwrede"
	editor="" 
	authors="ysxu"/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article"
	ms.date="02/19/2015" 
	ms.author="yuaxu"/>


# Ajout de notifications push à votre application iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../../includes/app-service-mobile-selector-get-started-push-preview.md)]

Cette rubrique montre comment utiliser Azure App Service pour envoyer des notifications push à une application iOS à l'aide du service de notification push Apple (APNS). À la fin, votre backend .NET enverra une notification push à votre application ToDo de démarrage rapide chaque fois qu'un enregistrement sera inséré. L'application est compatible avec iOS 8 et les versions antérieures.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1. [Génération de la demande de signature de certificat iOS]
2. [Inscription de votre application et activation des notifications Push]
3. [Création d’un profil de mise en service pour l’application]
4. [Configuration du serveur principal mobile pour l’envoi de demandes push]
5. [Mise à jour du serveur pour l’envoi de notifications Push](#update-server)
6. [Publication du serveur principal mobile dans Azure]
7. [Ajout de notifications Push à l’application]
8. [Test de l’application]

Ce didacticiel requiert les éléments suivants :

+ [Kit de développement logiciel (SDK) Azure Mobile App iOS]
+ [Nuget – Azure Notification Hubs]
+ [XCode 6.0][Install Xcode]
+ Un appareil compatible iOS 6,0 (ou version ultérieure)
+ Un abonnement au programme pour développeurs iOS

   >[AZURE.NOTE]En raison de la configuration requise pour les notifications Push, vous devez déployer et tester les notifications Push sur un appareil compatible iOS (iPhone ou iPad) au lieu d'un émulateur.

Ce didacticiel est basé sur le démarrage rapide d'App Service Mobile App. Avant de commencer, vous devez suivre le didacticiel [Prise en main des applications mobiles App Service].

[AZURE.INCLUDE [Activer les notifications Push Apple](../../includes/enable-apple-push-notifications.md)]

## Configuration de l'application mobile pour l'envoi de demandes push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push-preview](../../includes/app-service-mobile-apns-configure-push-preview.md)]

##<a id="update-server"></a>Mise à jour du serveur pour l’envoi de notifications Push

1. Dans Visual Studio, cliquez avec le bouton droit sur la solution, puis cliquez sur **Manage NuGet Packages**.

2. Recherchez **Microsoft.Azure.NotificationHubs** et cliquez sur **Installer** pour tous les projets dans la solution.

3. Dans l’Explorateur de solutions de Visual Studio, développez le dossier **Contrôleurs** du projet de serveur principal mobile. Ouvrez TodoItemController.cs. Au début du fichier, ajoutez les instructions `using` suivantes :

		using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;

4. Ajoutez l’extrait de code suivant à la méthode `PostTodoItem` après l’appel à **InsertAsync** :

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // iOS payload
        var appleNotificationPayload = "{"aps":{"alert":"" + item.Text + ""}}";

        await Hub.Push.SendAppleNativeNotificationAsync(appleNotificationPayload);

    Ce code indique au hub de notifications associé à cette application mobile d'envoyer une notification push après l'insertion d'une tâche.


## <a name="publish-the-service"></a>Publication du serveur principal mobile sur Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Ajout de notifications Push à votre application
1. Téléchargez et ajoutez une référence au Kit de développement logiciel (SDK) de client App Service Mobile App dans xcode.

2. Dans **QSAppDelegate.m**, ajoutez le code suivant dans **application:didFinishLaunchingWithOptions** pour inscrire le client auprès du service de notification push Apple :

        // register iOS8 or previous devices for notifications
        if ([[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && 	
        	[[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)]) {
            [[UIApplication sharedApplication] registerForRemoteNotifications];
        } else {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
        }

3. Dans le même fichier, ajoutez la méthode de gestionnaire suivante au sein de l’implémentation **QSAppDelegate** :

        // registration with APNs is successful
        - (void)application:(UIApplication *)application 
            didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {

            // make sure you have imported "QSTodoService.h"
            QSTodoService *todoService = [QSTodoService defaultService];
            MSClient *client = todoService.client;

            [client.push registerDeviceToken:deviceToken completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

4. Ensuite, ajoutez la méthode de gestionnaire d'échec au sein de l'implémentation :

        // handle any failure to register
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

5. Dans QSAppDelegate.m, ajoutez la méthode de gestionnaire suivante à l'implémentation :

        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application 
              didReceiveRemoteNotification:(NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            
            NSDictionary *apsPayload = userInfo[@"aps"];
            NSString *alertString = apsPayload[@"alert"];
    
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" 
                                                            message:alertString 
                                                           delegate:nil 
                                                  cancelButtonTitle:@"OK" 
                                                  otherButtonTitles:nil];
            [alert show];
        }

L'application est mise à jour et prend en charge les notifications Push.

## Test des notifications Push dans votre application

1. Appuyez sur le bouton **Démarrer** pour générer le projet, puis démarrez l'application sur un appareil compatible iOS, et enfin cliquez sur **OK** pour accepter les notifications Push.

  	![][23]

    > [AZURE.NOTE]Vous devez accepter explicitement les notifications Push de votre application. Cette demande s'effectue uniquement lors du premier démarrage de l'application.

2. Dans l’application, entrez un texte explicite, tel que _Nouvelle tâche Mobile Services_ puis cliquez sur l’icône Ajouter (**+**).

  	![][24]

3. Vérifiez que vous avez reçu une notification, puis cliquez sur **OK** pour fermer celle-ci.

  	![][25]

4. Répétez l'étape 2 et fermez immédiatement l'application, puis vérifiez que la notification Push suivante est affichée.

  	![][26]

Vous avez terminé ce didacticiel.

<!-- Anchors.  -->
[Génération de la demande de signature de certificat iOS]: #certificates
[Inscription de votre application et activation des notifications Push]: #register
[Création d’un profil de mise en service pour l’application]: #profile
[Ajout de notifications Push à l’application]: #add-push
[Configuration du serveur principal mobile pour l’envoi de demandes push]: #configure
[Update the server to send push notifications]: #update-server
[Publication du serveur principal mobile dans Azure]: #publish-mobile-service
[Test de l’application]: #test-the-service

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
[23]: ./media/app-service-mobile-dotnet-backend-ios-get-started-push-preview/mobile-quickstart-push1-ios.png
[24]: ./media/app-service-mobile-dotnet-backend-ios-get-started-push-preview/mobile-quickstart-push2-ios.png
[25]: ./media/app-service-mobile-dotnet-backend-ios-get-started-push-preview/mobile-quickstart-push3-ios.png
[26]: ./media/app-service-mobile-dotnet-backend-ios-get-started-push-preview/mobile-quickstart-push4-ios.png
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

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Kit de développement logiciel (SDK) Azure Mobile App iOS]: https://go.microsoft.com/fwLink/?LinkID=529823
[Nuget – Azure Notification Hubs]: https://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: ../mobile-services-dotnet-backend-ios-get-started.md
[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!--HONumber=54--> 