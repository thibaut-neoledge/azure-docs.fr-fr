<properties title="Azure Notification Hubs Secure Push" pageTitle="Azure Notification Hubs Secure Push" metaKeywords="Azure push notifications, Azure notification hubs, secure push" description="Learn how to send secure push notifications to an iOS app from Azure. Code samples written in Objective-C and C#." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="sethm" />

# Notifications Push sécurisées avec Azure Notification Hubs

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/fr-fr/documentation/articles/notification-hubs-windows-dotnet-secure-push/" title="Windows Universal">Windows Universal</a><a href="/fr-fr/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/" title="iOS" class="current">iOS</a>
        <a href="/fr-fr/documentation/articles/notification-hubs-aspnet-backend-android-secure-push/" title="Android">Android</a>
</div>

La prise en charge des notifications Push dans Microsoft Azure vous permet d'accéder à une infrastructure Push conviviale, multiplateforme et avec montée en charge qui simplifie fortement l'implémentation des notifications Push pour les applications grand public et entreprise destinées aux plateformes mobiles.

En raison de contraintes liées à la réglementation ou à la sécurité, une application peut avoir besoin d'inclure dans la notification des informations qui ne peuvent pas être transmises via l'infrastructure de notification Push standard. Ce didacticiel montre comment procéder en envoyant des informations sensibles par l'intermédiaire d'une connexion authentifiée sécurisée entre l'appareil client et le serveur principal de l'application.

Globalement, le processus est le suivant :

1.  Le serveur principal de l'application :
    -   stocke la charge utile sécurisée dans la base de données principale ;
    -   envoie l'ID de cette notification à l'appareil (aucune information sécurisée n'est envoyée).
2.  L'application qui se trouve sur l'appareil, lorsqu'elle reçoit la notification :
    -   L'appareil contacte le serveur principal en demandant la charge utile sécurisée.
    -   L'application peut afficher la charge utile sous la forme d'une notification sur l'appareil.

Il est important de noter que dans le processus précédent (et dans ce didacticiel), nous supposons que l'appareil stocke un jeton d'authentification dans un stockage local après la connexion de l'utilisateur. Ceci garantit une expérience entièrement transparente, car l'appareil peut récupérer la charge utile sécurisée de la notification avec ce jeton. Si votre application ne stocke pas les jetons d'authentification sur l'appareil, ou si ces jetons sont susceptibles d'expirer, lorsque l'application sur l'appareil reçoit la notification, elle doit afficher une notification générique demandant à l'utilisateur de lancer l'application. L'application authentifie alors l'utilisateur et affiche la charge utile de la notification.

Ce didacticiel sur les notifications Push sécurisées montre comment envoyer une notification Push en toute sécurité. Il s'appuie sur le didacticiel **Envoi de notifications à des utilisateurs**. Vous devez donc suivre ce dernier au préalable.

> [AZURE.NOTE] ce didacticiel part du principe que vous avez créé et configuré votre concentrateur de notification comme décrit dans la rubrique [Prise en main de Notification Hubs (iOS)][Prise en main de Notification Hubs (iOS)].

[WACOM.INCLUDE [notification-hubs-aspnet-backend-securepush](../includes/notification-hubs-aspnet-backend-securepush.md)]

## Modification du projet iOS

Maintenant que vous avez modifié le serveur principal de votre application pour qu'il n'envoie que l'*id* des notifications, vous devez modifier votre application iOS pour gérer cette notification et rappeler votre serveur pour récupérer le message sécurisé à afficher.

Pour cela, nous devons écrire la logique permettant de récupérer le contenu sécurisé auprès du serveur principal de l'application.

1.  Dans **AppDelegate.m**, ajoutez une section d'implémentation au début, avec la déclaration qui suit :

        @interface AppDelegate ()
        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        @end

2.  Ensuite, ajoutez le code qui suit dans la section d'implémentation, en remplaçant l'espace réservé `{back-end endpoint}` par le point de terminaison de votre serveur principal que vous avez obtenu précédemment :

        NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        {
            // check if authenticated
            ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
            NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
            if (!authenticationHeader) return;


            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];    
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                    NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                    completion([json objectForKey:@"Payload"], nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

    Cette méthode appelle le serveur principal de votre application pour récupérer le contenu de la notification avec les informations d'identification stockées dans les préférences partagées.

3.  Maintenant, nous devons gérer la notification entrante et utiliser la méthode ci-dessus pour récupérer le contenu à afficher. Nous devons tout d'abord permettre à l'application iOS de s'exécuter en arrière-plan lorsqu'elle reçoit une notification Push. Dans **XCode**, sélectionnez votre projet d'application dans le volet gauche, puis cliquez sur votre cible d'application principale dans la section **Cibles** du volet central.

4.  Cliquez ensuite sur l'onglet **Capacités** en haut du volet central et activez la case à cocher **Notifications distantes**.

    ![][0]

5.  Dans **AppDelegate.m**, ajoutez la méthode suivante pour gérer les notifications Push :

        -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
        {
            NSLog(@"%@", userInfo);

            [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
                if (!error) {
                    // show local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                    localNotification.alertBody = payload;
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];
                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                    completionHandler(UIBackgroundFetchResultNewData);
                } else {
                    completionHandler(UIBackgroundFetchResultFailed);
                }
            }];

        }

    Notez qu'il est préférable de gérer les cas de refus ou les cas concernant les propriétés d'en-tête d'authentification manquantes au niveau du serveur. La gestion spécifique de ces cas dépend surtout de l'expérience utilisateur souhaitée. Une possibilité est d'afficher une notification avec une invite générique demandant à l'utilisateur de s'authentifier afin de récupérer la notification elle-même.

## Exécution de l'application

Pour exécuter l'application, procédez comme suit :

1.  Assurez-vous que **AppBackend** est déployé dans Azure. Si vous utilisez Visual Studio, exécutez l'application API web **AppBackend**. Une page web ASP.NET s'affiche.

2.  Dans XCode, exécutez l'application sur un appareil iOS physique (les notifications Push ne fonctionnent pas dans le simulateur).

3.  Dans l'interface utilisateur de l'application iOS, entrez un nom d'utilisateur et un mot de passe. La valeur peut être une chaîne quelconque, mais elle doit être identique pour les deux.

4.  Dans l'interface utilisateur de l'application iOS, cliquez sur **Log in**. Cliquez ensuite sur **Send push**. Vous devriez voir la notification sécurisée affichée dans le centre de notifications.

  [notification-hubs-aspnet-backend-securepush]: ../includes/notification-hubs-aspnet-backend-securepush.md
  [0]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
