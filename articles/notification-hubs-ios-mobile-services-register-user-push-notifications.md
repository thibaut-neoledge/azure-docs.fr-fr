<properties 
	pageTitle="Inscription de l'utilisateur actuel pour les notifications Push à l'aide d'un service mobile - concentrateurs de Notification" 
	description="Découvrez comment demander l'inscription aux notifications Push dans une application iOS avec Azure Notification Hubs lorsque l'inscription est réalisée par Azure Mobile Services." 
	services="mobile-services, notification-hubs" 
	documentationCenter="" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="yuaxu"/>

# Inscription de l'utilisateur actif aux notifications Push à l'aide d'un service mobile

<div class="dev-center-tutorial-selector sublanding">
    <a href="/fr-fr/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/" title="Windows Store C#">Windows Store c#</a><a href="/fr-fr/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/" title="iOS" class="current">iOS</a>
</div>

Cette rubrique vous montre comment demander une inscription aux notifications Push avec Azure Notification Hubs lorsque l'inscription est réalisée par Azure Mobile Services. Cette rubrique étend le didacticiel [notification des utilisateurs avec Notification Hubs]. Vous devez avoir suivi les étapes de ce didacticiel permettant de créer le service mobile authentifié. Pour plus d'informations sur le scénario d'utilisateurs notifier, consultez [notification des utilisateurs avec Notification Hubs].  

1. Dans Xcode, ouvrez le fichier QSTodoService.h dans le projet que vous avez créé lorsque vous avez suivi le didacticiel [Prise en main de l'authentification]et ajoutez la propriété **deviceToken** suivante :

		@property (nonatomic) NSData* deviceToken;

 	Cette propriété stocke le jeton d'appareil.

2. Dans le fichier QSTodoService.m, ajoutez la méthode **getDeviceTokenInHex** suivante :

			- (NSString*)getDeviceTokenInHex {
			    const unsigned *tokenBytes = [[self deviceToken] bytes];
			    NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
			                          ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
			                          ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
			                          ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
			    return hexToken;
			}

	Cette méthode convertit le jeton d'appareil en valeur de chaîne hexadécimale.

3. Dans le fichier QSAppDelegate.m, ajoutez les lignes suivantes du code pour la méthode **didFinishLaunchingWithOptions** :

			[[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

	Cette action active les notifications Push dans l'application.

4. 	Dans le fichier QSAppDelegate.m, ajoutez la méthode suivante :

			- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
			    [QSTodoService defaultService].deviceToken = deviceToken;
			}

	Cela met à jour la propriété **deviceToken**.

	> [AZURE.NOTE] À ce stade, il ne doit pas y avoir d'autre code dans cette méthode. Si vous disposez déjà d'un appel à la méthode **registerNativeWithDeviceToken** ajoutée lorsque vous avez suivi le didacticiel [prise en main des concentrateurs de Notification](/fr-fr/manage/services/notification-hubs/get-started-notification-hubs-ios/"%20target="_blank") , vous devez commenter ou supprimer cet appel.

5.  (Facultatif) Dans le fichier QSAppDelegate.m, ajoutez la méthode de gestionnaire suivante :

			- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
			    NSLog(@"%@", userInfo);
			    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
			                          [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
			                          @"OK" otherButtonTitles:nil, nil];
			    [alert show];
			}

 	Cette méthode affiche une alerte dans l'interface utilisateur lorsque votre application reçoit des notifications alors qu'elle est en cours d'exécution.

6. Dans le fichier QSTodoListViewController.m, ajoutez la méthode **registerForNotificationsWithBackEnd** :

			- (void)registerForNotificationsWithBackEnd {
			    NSString* json = [NSString  stringWithFormat:@"{\"platform\":\"ios\", \"deviceToken\":\"%@\"}", [self.todoService getDeviceTokenInHex] ];

			    [self.todoService.client invokeAPI:@"register_notifications" data:[json dataUsingEncoding:NSUTF8StringEncoding] HTTPMethod:@"POST" parameters:nil headers:nil completion:^(id result, NSHTTPURLResponse *response, NSError *error) {
			        if (error != nil) {
			            NSLog(@"Registration failed: %@", error);
			        } else {
			            // display UIAlert with successful login
			            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
			            [alert show];
			        }
			    }];
			}

	Cette méthode construit une charge utile json qui contient le jeton d'appareil. Elle appelle ensuite l'API personnalisée dans votre Mobile Service pour vous inscrire afin de recevoir des notifications. Cette méthode crée un jeton d'appareil pour les notifications Push et l'envoie avec le type d'appareil à la méthode API personnalisée qui crée une inscription dans Notification Hubs. Cette API personnalisée a été définie dans [notification des utilisateurs avec Notification Hubs].

7.	Enfin, dans le **viewDidAppear** (méthode), ajouter un appel à la nouvelle **registerForNotificationsWithBackEnd** méthode une fois que l'utilisateur authentifié avec succès, comme dans l'exemple suivant :

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

	> [AZURE.NOTE] Cela permet de s'assurer que l'inscription est demandée à chaque chargement de la page. Dans votre application, vous souhaitez effectuer cette inscription régulièrement pour vous assurer de son exactitude.
	
Maintenant que l'application cliente a été mise à jour, revenez à la [notification des utilisateurs avec Notification Hubs] et mettez à jour le service mobile pour envoyer des notifications à l'aide des concentrateurs de Notification.

<!-- Anchors. -->

<!-- Images. -->


<!-- URLs. -->
[Notification des utilisateurs avec Notification Hubs]: /fr-fr/manage/services/notification-hubs/notify-users
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-ios/

[Portail de gestion Azure]: https://manage.windowsazure.com/
[Prise en main des concentrateurs de Notification]: /fr-fr/manage/services/notification-hubs/get-started-notification-hubs-ios/


<!--HONumber=42-->
