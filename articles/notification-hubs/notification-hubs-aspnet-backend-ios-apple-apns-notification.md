---
title: Azure Notification Hubs notifie les utilisateurs pour iOS avec backend .NET
description: "Découvrez comment envoyer des notifications Push aux utilisateurs dans Azure. Exemples de code écrits en Objective-C et l'API .NET pour le serveur principal."
documentationcenter: ios
author: ysxu
manager: erikre
editor: 
services: notification-hubs
ms.assetid: 1f7d1410-ef93-4c4b-813b-f075eed20082
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: 0fa7a886e1ecb0a90b6aebc1dbf9ef0c6ce1acf1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="azure-notification-hubs-notify-users-for-ios-with-net-backend"></a>Azure Notification Hubs notifie les utilisateurs pour iOS avec backend .NET
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Vue d'ensemble
La prise en charge des notifications Push dans Azure vous permet d’accéder à une infrastructure Push conviviale, multi-plateforme et avec montée en charge qui simplifie fortement l’implémentation des notifications Push pour les applications consommateur et entreprise pour les plateformes mobiles. Ce didacticiel explique comment utiliser Azure Notification Hubs pour envoyer des notifications Push à un utilisateur particulier d'une application sur un appareil spécifique. Un code WebAPI principal ASP.NET est utilisé pour authentifier les clients et pour générer les notifications, comme le présente la rubrique d’aide [Inscription auprès du serveur principal de votre application](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend).

> [!NOTE]
> Ce didacticiel repose sur l'hypothèse que vous avez créé et configuré votre concentrateur de notification comme décrit dans [Prise en main de Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md). Ce didacticiel est également un prérequis pour le didacticiel sur les [notifications Push sécurisées (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) .
> Si vous souhaitez utiliser Mobile Apps comme service principal, voir l’article [Mobile Apps concernant la prise en main des notifications Push](../app-service-mobile/app-service-mobile-ios-get-started-push.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Modification de votre application iOS
1. Ouvrez l'application d'affichage Page simple que vous avez créée dans le didacticiel [Prise en main de Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) .
   
   > [!NOTE]
   > Dans cette section, nous partons du principe que vous avez configuré votre projet avec un nom d’organisation vide. Si ce n’est pas le cas, vous devez ajouter le nom de votre organisation à tous les noms de classe.
   > 
   > 
2. Dans Main.storyboard, ajoutez les composants indiqués dans la capture d'écran ci-dessous de la bibliothèque d'objets :
   
    ![][1]
   
   * **Nom d'utilisateur**: champ UITextField avec du texte d'espace réservé, *Entrer le nom d'utilisateur*, juste en dessous de l'étiquette des résultats d'envoi et entre les marges gauche et droite, en dessous de l'étiquette des résultats d'envoi.
   * **Mot de passe**: champ UITextField avec du texte d'espace réservé, *Entrer le mot de passe*, juste en dessous du champ de texte du nom d'utilisateur et entre les marges gauche et droite, en dessous du champ de texte du nom d'utilisateur. Cochez l'option **Sécuriser l'entrée de texte** dans l'inspecteur d'attributs sous *Touche Retour*.
   * **Connexion** : un UIButton étiqueté immédiatement en dessous du champ de texte du mot de passe ; décochez l’option **Activé** dans l’inspecteur d’attributs, sous *Contrôle-Contenu*
   * **WNS**: étiquette et commutateur pour activer l'envoi de la notification du service de notification Windows, si elle a été configurée sur le hub. Consultez le didacticiel [Prise en main de Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
   * **GCM**: étiquette et commutateur pour activer l'envoi de la notification à Google Cloud Messaging, si elle a été configurée sur le hub. Consultez le didacticiel [Prise en main d'Android](notification-hubs-android-push-notification-google-gcm-get-started.md) .
   * **APNS**: étiquette et commutateur pour activer l'envoi de la notification au service de notification de la plateforme Apple.
   * **Nom d'utilisateur du destinataire**: champ UITextField avec du texte d'espace réservé, *Balise du nom d'utilisateur du destinataire*, juste en dessous de l'étiquette GCM et entre les marges gauche et droite, en dessous de l'étiquette GCM.

    Certains composants ont été ajoutés dans le didacticiel [Prise en main de Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) .

1. **Ctrl** et faites glisser les composants de l'affichage vers ViewController.h, puis ajoutez ces nouvelles sorties.
   
        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UITextField *RecipientField;
        @property (weak, nonatomic) IBOutlet UITextField *NotificationField;
   
        // Used to enable the buttons on the UI
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;
   
        // Used to enabled sending notifications across platforms
        @property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;
   
        - (IBAction)LogInAction:(id)sender;
2. Dans ViewController.h, ajoutez le code `#define` suivant juste en dessous de vos instructions d'importation. Remplacez l’espace réservé *<Enter Your Backend Endpoint\>* par l’URL de destination que vous avez utilisée pour déployer votre serveur principal d’application dans la section précédente. For example, *http://votre_serveur_principal.azurewebsites.net*.
   
        #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"
3. Dans votre projet, créez une **classe Cocoa Touch** nommée **RegisterClient** pour communiquer avec le serveur principal ASP.NET que vous avez créé. Créez la classe en héritant de `NSObject`. Ensuite, ajoutez le code qui suit dans RegisterClient.h.
   
        @interface RegisterClient : NSObject
   
        @property (strong, nonatomic) NSString* authenticationHeader;
   
        -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
            andCompletion:(void(^)(NSError*))completion;
   
        -(instancetype) initWithEndpoint:(NSString*)Endpoint;
   
        @end
4. Dans RegisterClient.m, mettez à jour la section `@interface` :
   
        @interface RegisterClient ()
   
        @property (strong, nonatomic) NSURLSession* session;
        @property (strong, nonatomic) NSURLSession* endpoint;
   
        -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                    andCompletion:(void(^)(NSError*))completion;
        -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                    completion:(void(^)(NSString*, NSError*))completion;
        -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
                    tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;
   
        @end
5. Remplacez la section `@implementation` dans RegisterClient.m par le code suivant.

        @implementation RegisterClient

        // Globals used by RegisterClient
        NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

        -(instancetype) initWithEndpoint:(NSString*)Endpoint
        {
            self = [super init];
            if (self) {
                NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
                _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
                _endpoint = Endpoint;
            }
            return self;
        }

        -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
                    andCompletion:(void(^)(NSError*))completion
        {
            [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
        }

        -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                    andCompletion:(void(^)(NSError*))completion
        {
            NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

            NSString *deviceTokenString = [[token description]
                stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
            deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
                                    uppercaseString];

            [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
                completion:^(NSString* registrationId, NSError *error) {
                NSLog(@"regId: %@", registrationId);
                if (error) {
                    completion(error);
                    return;
                }

                [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
                    tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                    if (error) {
                        completion(error);
                        return;
                    }

                    NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                    if (httpResponse.statusCode == 200) {
                        completion(nil);
                    } else if (httpResponse.statusCode == 410 && retry) {
                        [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                    } else {
                        NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

                        completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                    userInfo:nil]);
                    }

                }];
            }];
        }

        -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
                    tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
        {
            NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
                                                    @"Tags": [tags allObjects]};
            NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
                                options:NSJSONWritingPrettyPrinted error:nil];

            NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
                                                encoding:NSUTF8StringEncoding]);

            NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
                                    registrationId];
            NSURL* requestURL = [NSURL URLWithString:endpoint];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"PUT"];
            [request setHTTPBody:jsonData];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                    self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
            [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                if (!error)
                {
                    completion(response, error);
                }
                else
                {
                    NSLog(@"Error request: %@", error);
                    completion(nil, error);
                }
            }];
            [dataTask resume];
        }

        -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                    completion:(void(^)(NSString*, NSError*))completion
        {
            NSString* registrationId = [[NSUserDefaults standardUserDefaults]
                                        objectForKey:RegistrationIdLocalStorageKey];

            if (registrationId)
            {
                completion(registrationId, nil);
                return;
            }

            // request new one & save
            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
                                    _endpoint, token]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"POST"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                    self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSString* registrationId = [[NSString alloc] initWithData:data
                        encoding:NSUTF8StringEncoding];

                    // remove quotes
                    registrationId = [registrationId substringWithRange:NSMakeRange(1,
                                        [registrationId length]-2)];

                    [[NSUserDefaults standardUserDefaults] setObject:registrationId
                        forKey:RegistrationIdLocalStorageKey];
                    [[NSUserDefaults standardUserDefaults] synchronize];

                    completion(registrationId, nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                    userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

        @end

    Le code ci-dessus implémente la logique expliquée dans l'article [Inscription auprès du serveur principal de votre application](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) , et utilise NSURLSession pour passer des appels REST à votre serveur principal d'application et NSUserDefaults pour stocker en local la valeur registrationId renvoyée par le concentrateur de notification.

    Notez que cette classe a besoin que sa propriété **authorizationHeader** soit définie afin de fonctionner correctement. Cette propriété est définie par la classe **ViewController** après la connexion.

1. Dans ViewController.h, ajoutez une instruction `#import` pour RegisterClient.h. Ensuite, ajoutez une déclaration pour le jeton de l'appareil et faites référence à une instance `RegisterClient` dans la section `@interface` :
   
        #import "RegisterClient.h"
   
        @property (strong, nonatomic) NSData* deviceToken;
        @property (strong, nonatomic) RegisterClient* registerClient;
2. Dans ViewController.m, ajoutez une déclaration de méthode privée dans la section `@interface` :
   
        @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>
   
        // create the Authorization header to perform Basic authentication with your app back-end
        -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                        AndPassword:(NSString*)password;
   
        @end

> [!NOTE]
> L'extrait de code suivant n'étant pas un système d'authentification sécurisé, vous devez remplacer l'implémentation de **createAndSetAuthenticationHeaderWithUsername:AndPassword:** par votre mécanisme d'authentification spécifique qui génère un jeton d'authentification devant être consommé par la classe cliente du registre, par exemple, OAuth, Active Directory.
> 
> 

1. Ensuite, dans la section `@implementation` de ViewController.m, ajoutez le code suivant qui ajoute l'implémentation pour la définition du jeton de l'appareil et de l'en-tête de l'authentification.
   
        -(void) setDeviceToken: (NSData*) deviceToken
        {
            _deviceToken = deviceToken;
            self.LogInButton.enabled = YES;
        }
   
        -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                        AndPassword:(NSString*)password;
        {
            NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];
   
            NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];
   
            self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
                                                        encoding:NSUTF8StringEncoding];
        }
   
        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }
   
    Remarquez la façon dont le jeton de l’appareil active le bouton de connexion. Ceci est dû au fait que dans le cadre de l’action de connexion, le contrôleur d’affichage s’inscrit aux notifications Push auprès du serveur principal d’application. De ce fait, l’action de connexion ne doit pas être accessible tant que le jeton de l’appareil n’a pas été correctement configuré. Vous pouvez découpler la connexion de l’inscription aux notifications Push dans la mesure où la connexion se produit avant l’inscription.
2. Dans ViewController.m, utilisez les extraits de code suivants pour implémenter la méthode d'action de votre bouton **Connexion** et une méthode pour envoyer le message de notification à l'aide du serveur principal ASP.NET.
   
       - (IBAction) LogInAction : expéditeur de (id) {/ / créer l’en-tête d’authentification et la définir dans le Registre client NSString * nom d’utilisateur = self. UsernameField.text ;   Le mot de passe NSString * = self. PasswordField.text ;
   
           [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];
   
           selfie __weak ViewController * = self ;   [self.registerClient registerWithDeviceToken:self.deviceToken balises : nil andCompletion:^(NSError* error) {Si ( ! erreur) {dispatch_async(dispatch_get_main_queue(), ^ {selfie. SendNotificationButton.enabled = YES ;               [self MessageBox:@"Success » message:@"Registered avec succès ! »] ;}) ;}}] ;}

        - (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag            Message:(NSString*)message {    NSURLSession* session = [NSURLSession        sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil        delegateQueue:nil];

            Passer de la balise pns et le nom d’utilisateur en tant que paramètres avec l’URL de REST pour le serveur principal d’ASP.NET NSURL * requestURL = [NSURL URLWithString : [NSString stringWithFormat:@"%@/api/notifications? pns = % @& to_tag = % @ », BACKEND_ENDPOINT, pns, usernameTag]] ;

            Demande de NSMutableURLRequest = [NSMutableURLRequest requestWithURL:requestURL] ;    [demande setHTTPMethod:@"POST »] ;

            Obtenir l’authenticationheader fictif à partir du client du Registre authorizationHeaderValue NSString * = [NSString stringWithFormat:@"Basic % @ », self.registerClient.authenticationHeader] ;    [demande setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization »] ;

            Ajouter le corps du message de notification [demande setValue:@"application/json;charset=utf-8 « forHTTPHeaderField:@"Content-Type »] ;    [demande setHTTPBody : [message dataUsingEncoding:NSUTF8StringEncoding]] ;

            Exécutez l’API REST envoyer une notification sur le dataTask ASP.NET principal NSURLSessionDataTask * = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError  *erreur) {NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) réponse ;        Si (erreur || httpResponse.statusCode ! = 200) {NSString* état = [NSString stringWithFormat:@"Error état % @: % d\nError : %@\n», pns, httpResponse.statusCode, erreur] ;            dispatch_async(dispatch_get_main_queue(), ^ {/ / ajout de texte, car tous les appels PNS 3 peuvent également avoir des informations à la vue [stringByAppendingString:status de setText:[self.sendResults.text self.sendResults]] ;            });            NSLog(status) ;        }

                if (data != NULL)
                {
                    xmlParser = [[NSXMLParser alloc] initWithData:data];
                    [xmlParser setDelegate:self];
                    [xmlParser parse];
                }
            }];    [dataTask resume]; }


1. Mettez à jour l'action du bouton **Envoyer une notification** pour utiliser le serveur principal ASP.NET et effectuer l'envoi vers n'importe quel PNS activé par un commutateur.

        - (IBAction)SendNotificationMessage:(id)sender
        {
            //[self SendNotificationRESTAPI];
            [self SendToEnabledPlatforms];
        }


        -(void)SendToEnabledPlatforms
        {
            NSString* json = [NSString stringWithFormat:@"\"%@\"",self.notificationMessage.text];

            [self.sendResults setText:@""];

            if ([self.WNSSwitch isOn])
                [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

            if ([self.GCMSwitch isOn])
                [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

            if ([self.APNSSwitch isOn])
                [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
        }



1. Dans la fonction **ViewDidLoad**, ajoutez ce qui suit pour instancier l'instance de RegisterClient et définir le délégué pour vos champs de texte.
   
       self.UsernameField.delegate = self;
       self.PasswordField.delegate = self;
       self.RecipientField.delegate = self;
       self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
2. À présent, dans **AppDelegate.m**, supprimez tout le contenu de la méthode **application:didRegisterForPushNotificationWithDeviceToken:** et remplacez-le par ce qui suit pour faire en sorte que le contrôleur d’affichage contienne le dernier jeton de l’appareil extrait d’APNs :
   
       // Add import to the top of the file
       #import "ViewController.h"
   
       - (void)application:(UIApplication *)application
                   didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
       {
           ViewController* rvc = (ViewController*) self.window.rootViewController;
           rvc.deviceToken = deviceToken;
       }
3. Enfin, assurez-vous qu' **AppDelegate.m**contient la méthode suivante :
   
       - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
           NSLog(@"%@", userInfo);
           [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
       }

## <a name="test-the-application"></a>Tester l'application
1. Dans XCode, exécutez l’application sur un appareil iOS physique (les notifications Push ne fonctionnent pas dans le simulateur).
2. Dans l’interface utilisateur de l’application iOS, entrez un nom d’utilisateur et un mot de passe. La valeur peut être une chaîne quelconque, mais elle doit être identique pour les deux. Cliquez ensuite sur **Log In**.
   
    ![][2]
3. Une fenêtre contextuelle doit s’afficher pour vous informer que l’inscription a abouti. Cliquez sur **OK**.
   
    ![][3]
4. Dans le champ de texte **Balise de nom d’utilisateur du destinataire* , entrez la balise de nom d’utilisateur utilisée lors de l’enregistrement sur un autre appareil.
5. Entrez un message de notification et cliquez sur **Envoyer une notification**.  Seuls les appareils qui disposent d'un enregistrement avec la balise de nom d'utilisateur du destinataire reçoivent le message de notification.  Il n'est envoyé qu'à ces utilisateurs.
   
    ![][4]

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
