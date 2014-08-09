<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-mobile-service-ios" urlDisplayName="Notify iOS app users by using Mobile Services" pageTitle="Register the current user for push notifications by using a mobile service - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification iOS app" description="Learn how to request push notification registration in an iOS app with Azure Notification Hubs when registeration is performed by Azure Mobile Services." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Register the current user for push notifications by using a mobile service" authors="" solutions="" manager="" editor="" />

Inscription de l'utilisateur actif aux notifications Push à l'aide d'un service mobile
======================================================================================

[Windows Store C\#](/fr-fr/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/ "Windows Store C#")[iOS](/fr-fr/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/ "iOS")

Cette rubrique vous montre comment demander une inscription aux notifications Push avec Azure Notification Hubs lorsque l'inscription est réalisée par Azure Mobile Services. Cette rubrique s'inscrit dans le prolongement du didacticiel [Notification des utilisateurs avec Notification Hubs](/en-us/manage/services/notification-hubs/notify-users). Vous devez avoir suivi les étapes de ce didacticiel permettant de créer le service mobile authentifié. Pour plus d'informations sur les scénarios de notification des utilisateurs, consultez la rubrique [Notification des utilisateurs avec Notification Hubs](/en-us/manage/services/notification-hubs/notify-users).

1.  Dans Xcode, ouvrez le fichier QSTodoService.h dans le projet que vous avez créé en suivant le didacticiel [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-ios/), et ajoutez la propriété **deviceToken** suivante :

         @property (nonatomic) NSData* deviceToken;

	Cette propriété stocke le jeton d'appareil.

2.  Dans le fichier QSTodoService.m, ajoutez la méthode **getDeviceTokenInHex** suivante :

             - (NSString*)getDeviceTokenInHex {
                 const unsigned *tokenBytes = [[self deviceToken] bytes];
                 NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                       ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                       ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                       ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
                 return hexToken;
             }

    Cette méthode convertit le jeton d'appareil en valeur de chaîne hexadécimale.

3.  Dans le fichier QSAppDelegate.m, ajoutez les lignes de code suivantes à la méthode **didFinishLaunchingWithOptions** :

			[[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

    Cette action active les notifications Push dans l'application.

4.  Dans le fichier QSAppDelegate.m, ajoutez la méthode suivante :

         - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
                [QSTodoService defaultService].deviceToken = deviceToken;
            }

    Cette action met à jour la propriété **deviceToken**.

    **Remarque**

    À ce stade, il ne doit pas y avoir d'autre code dans cette méthode. S'il existe déjà un appel à la méthode **registerNativeWithDeviceToken** que vous avez ajoutée lorsque vous avez suivi le didacticiel [Prise en main de Notification Hubs](/en-us/manage/services/notification-hubs/get-started-notification-hubs-ios/), vous devez placer l'appel en commentaire ou le supprimer.

5.  (Facultatif) Dans le fichier QSAppDelegate.m, ajoutez la méthode de gestionnaire suivante :

            - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
                NSLog(@"%@", userInfo);
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            }

	Cette méthode affiche une alerte dans l'interface utilisateur lorsque votre application reçoit des notifications alors qu'elle est en cours d'exécution.

6.  Dans le fichier QSTodoListViewController.m, ajoutez la méthode **registerForNotificationsWithBackEnd** :

             - (void)registerForNotificationsWithBackEnd {    
                 NSString* json = [NSString  stringWithFormat:@"{     - (void)registerForNotificationsWithBackEnd {    
                 NSString* json = [NSString  stringWithFormat:@"{\"platform\":\"ios\", \"deviceToken\":\"%@\"}", [self.todoService getDeviceTokenInHex] ];
                    
                 [self.todoService.client invokeAPI:@"register_notifications" data:[json dataUsingEncoding:NSUTF8StringEncoding] HTTPMethod:@"POST" parameters:nil headers:nil completion:^(id result, NSHTTPURLResponse *response, NSError *error) {
                     if (error != nil) {
                         NSLog(@"Registration failed: %@", error);
                     } else {
                         // affichez UIAlert après avoir établi la connexion
                         UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
                         [alert show];
                     }
                 }];
             }

    Cette méthode construit une charge utile json qui contient le jeton d'appareil. Elle appelle ensuite l'API personnalisée dans votre Mobile Service pour vous inscrire afin de recevoir des notifications. Cette méthode crée un jeton d'appareil pour les notifications Push et l'envoie avec le type d'appareil à la méthode API personnalisée qui crée une inscription dans Notification Hubs. Cette API personnalisée a été définie dans le cadre du didacticiel [Notification des utilisateurs avec Notification Hubs](/en-us/manage/services/notification-hubs/notify-users).

2.  Pour terminer, dans la méthode **viewDidAppear**, ajoutez un appel à cette nouvelle méthode **registerForNotificationsWithBackEnd** après l'authentification de l'utilisateur, comme dans l'exemple suivant :

            - (void)viewDidAppear:(BOOL)animated
            {
                MSClient *client = self.todoService.client;
                
                if (client.currentUser != nil) {
                    return;
                }
                    
                [client loginWithProvider:@"microsoftaccount" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                    [self refresh];
                    [self registerForNotificationsWithBackEnd];
                }];
            }

    **Remarque**

    Cela permet de s'assurer que l'inscription est demandée à chaque chargement de la page. Dans votre application, vous souhaitez effectuer cette inscription régulièrement pour vous assurer de son exactitude.

Maintenant que l'application cliente est à jour, retournez au didacticiel [Notification des utilisateurs avec Notification Hubs](/en-us/manage/services/notification-hubs/notify-users) et mettez le service mobile à jour pour qu'il envoie des notifications à l'aide de Notification Hubs.

<!-- Anchors. -->

<!-- Images. -->


<!-- URLs. -->
[Notify users with Notification Hubs]: /en-us/manage/services/notification-hubs/notify-users
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-ios/

[Azure Management Portal]: https://manage.windowsazure.com/
[Get Started with Notification Hubs]: /en-us/manage/services/notification-hubs/get-started-notification-hubs-ios/
