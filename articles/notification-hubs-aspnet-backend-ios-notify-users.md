<properties title="Azure Notification Hubs Notify Users" pageTitle="Azure Notification Hubs Notify Users" metaKeywords="Azure push notifications, Azure notification hubs" description="Learn how to send secure push notifications in Azure. Code samples written in Objective-C using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="sethm"></tags>

# Notification des utilisateurs via Azure Notification Hubs

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/fr-fr/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal">Windows Universal</a><a href="/fr-fr/documentation/articles/notification-hubs-/" title="iOS" class="current">iOS</a>
        <a href="/fr-fr/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

La prise en charge des notifications Push dans Azure vous permet d'accéder à une infrastructure Push conviviale, multi-plateforme et avec montée en charge qui simplifie fortement l'implémentation des notifications Push pour les applications consommateur et entreprise pour les plateformes mobiles. Ce didacticiel explique comment utiliser Azure Notification Hubs pour envoyer des notifications Push à un utilisateur particulier d'une application sur un appareil spécifique. Un code WebAPI principal ASP.NET est utilisé pour authentifier les clients et pour générer les notifications, comme présenté dans la rubrique de conseils [Inscription auprès du serveur principal de votre application][Inscription auprès du serveur principal de votre application]. Ce didacticiel fait intervenir le concentrateur de notification que vous avez créé dans le didacticiel intitulé **Prise en main de Notification Hubs**.

Ce didacticiel est également un prérequis pour le didacticiel **sur les notifications Push sécurisées**. Une fois que vous avez terminé les étapes de ce didacticiel **Notification des utilisateurs**, vous pouvez passer au didacticiel **Notifications Push sécurisées**, qui vous présente comment modifier le code **Notification des utilisateurs** pour envoyer une notification Push de façon sécurisée.

> [AZURE.NOTE] ce didacticiel part du principe que vous avez créé et configuré votre concentrateur de notification comme décrit dans la rubrique [Prise en main de Notification Hubs (iOS)][Prise en main de Notification Hubs (iOS)].

## Création et configuration du concentrateur de notification

Avant de commencer ce didacticiel, vous devez créer un profil d'approvisionnement iOS et un certificat Push de développement, puis créer un concentrateur de notification Azure et le connecter à cette application. Suivez les étapes de la rubrique [Prise en main de Notification Hubs (iOS)][Prise en main de Notification Hubs (iOS)], en particulier les sections 1 à 5.

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers][notification-hubs-aspnet-backend-notifyusers]]

## Modification de votre application iOS

1.  Suivez les étapes de la rubrique [Prise en main de Notification Hubs (iOS)][Prise en main de Notification Hubs (iOS)], sections 1 à 5, pour créer une application d'affichage à une seule page capable de recevoir des notifications Push d'un concentrateur de notification.

> [AZURE.NOTE] Dans cette section, nous partons du principe que vous avez configuré votre projet avec un nom d'organisation vide. Si ce n'est pas le cas, vous devez faire précéder tous les noms de classes du code ci-dessous par le nom de votre organisation.

1.  Dans Main.storyboard, ajoutez les composants suivants de la bibliothèque d'objets :

    -   Un champ UITextField avec comme texte de l'espace réservé **Username**
    -   Un champ UITextField avec comme texte de l'espace réservé **Password**, puis activez l'option **Saisie de texte sécurisée** dans le groupe de propriétés TextField dans le volet de droite
    -   Un bouton UIButton appel **1. Log in**, et désactivez l'option **Enabled** dans le groupe de propriétés Accessibilité dans le volet de droite
    -   Un bouton UIButton appel **2. Send Push**, et désactivez l'option **Enabled** dans le groupe de propriétés Accessibilité dans le volet de droite

    Votre storyboard doit ressembler à ce qui suit :

    ![][]

2.  Créez des outlets pour les deux UITextFields et les boutons UIButtons dans la partie interface de votre ViewController.m

        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

3.  Créez des actions pour vos deux boutons dans votre implémentation ViewController.m :			
		- (IBAction)LogInAction:(id)sender { }
		- (IBAction)SendPushAction:(id)sender { }

4.  Tout d'abord, nous allons créer une classe RegisterClient pour assurer l'interface avec notre serveur principal. Créez une classe Objective-C appelée RegisterClient, qui hérite de NSObject. Puis ajoutez le code qui suit dans la section interface de RegisterClient.h :

        @property (strong, nonatomic) NSString* authenticationHeader;
        -(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;

5.  Dans RegisterClient.m, ajoutez la section d'interface suivante :

        @interface RegisterClient ()

        @property (strong, nonatomic) NSURLSession* session;
        -(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
        -(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
        -(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSString*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;

        @end

6.  Ensuite, ajoutez le code qui suit dans la section d'implémentation et remplacez l'espace réservé *{backend endpoint}* par le point de terminaison que vous avez utilisé pour déployer votre serveur principal d'application dans la section précédente.

        NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";
        NSString *const BackEndEndpoint = @"{backend endpoint}/api/register";

        - (instancetype)init
        {
            self = [super init];
            if (self) {
                NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
                _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
            }
            return self;
        }

        -(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;
        {
            [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
        }

        -(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
        {
            NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

            NSString *deviceTokenString = [[token description] stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
            deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""] uppercaseString];

            [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString completion:^(NSString* registrationId, NSError *error) {
                NSLog(@"regId: %@", registrationId);
                if (error) {
                    completion(error);
                    return;
                }

                [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
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
                        NSLog(@"Registration error with response status: %ld", (long) httpResponse.statusCode);

                        completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode userInfo:nil]);
                    }

                }];
            }];
        }

        -(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSData*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;
        {
            NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token, @"Tags": [tags allObjects]};
            NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration options:NSJSONWritingPrettyPrinted error:nil];

            NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData encoding:NSUTF8StringEncoding]);

            NSString* endpoint = [NSString stringWithFormat:@"%@/%@", BackEndEndpoint, registrationId];
            NSURL* requestURL = [NSURL URLWithString:endpoint];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"PUT"];
            [request setHTTPBody:jsonData];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
            [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
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

        -(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
        {
            NSString* registrationId = [[NSUserDefaults standardUserDefaults] objectForKey:RegistrationIdLocalStorageKey];

            if (registrationId)
            {
                completion(registrationId, nil);
                return;
            }

            // request new one & save
            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@?handle=%@", BackEndEndpoint, token]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"POST"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSString* registrationId = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];

                    // remove quotes
                    registrationId = [registrationId substringWithRange: NSMakeRange(1, [registrationId length]-2)];

                    [[NSUserDefaults standardUserDefaults] setObject:registrationId forKey:RegistrationIdLocalStorageKey];
                    [[NSUserDefaults standardUserDefaults] synchronize];

                    completion(registrationId, nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

    Le code ci-dessus implémente la logique expliquée dans l'article [Inscription auprès du serveur principal de votre application][Inscription auprès du serveur principal de votre application], et utilise NSURLSession pour passer des appels REST à votre serveur principal d'application et NSUserDefaults pour stocker en local la valeur registrationId renvoyée par le concentrateur de notification.

    Notez que cette classe a besoin que sa propriété **authorizationHeader** soit définie afin de fonctionner correctement. Cette propriété est définie par la classe **ViewController** après la connexion.

7.  Dans ViewController.h, ajoutez la propriété suivante, qui contiendra le jeton de l'appareil :

        @property (strong, nonatomic) NSData* deviceToken;

8.  Dans ViewController.m, faites de la classe ViewController un UITextFieldDelegate, ajoutez une propriété pour faire référence à une instance RegisterClient, puis ajoutez une méthode de déclaration privée. La section de votre interface doit être :

        @interface ViewController () <UITextFieldDelegate>
        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

        @property (strong, nonatomic) RegisterClient* registerClient;

        -(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;
        @end

    Cette méthode privée sera utilisée pour créer l'en-tête Authorization afin d'assurer une authentification de base auprès du serveur principal de votre application.

> [AZURE.NOTE] Ceci n'est pas un schéma d'authentification sécurisé. Vous devez remplacer l'implémentation de **createAndSetAuthenticationHeaderWithUsername:AndPassword:** par votre mécanisme d'authentification spécifique qui génère un jeton d'authentification destiné à votre classe de client d'inscription, par exemple OAuth, Active Directory.

1.  Ensuite, dans la section d'implémentation de ViewController.m, ajoutez le code qui suit :

        -(void) setDeviceToken: (NSData*) deviceToken
        {
            _deviceToken = deviceToken;
            self.LogInButton.enabled = YES;
        }

        -(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;
        {
            NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

            NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

            self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData encoding:NSUTF8StringEncoding];
        }

        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }

    Remarquez la façon dont le jeton de l'appareil active le bouton de connexion. Ceci est dû au fait que dans le cadre de l'action de connexion, le contrôleur d'affichage s'inscrit aux notifications Push avec le serveur principal d'application. Nous voulons donc éviter que l'utilisateur utilise le bouton de connexion avant que le jeton de l'appareil ne soit correctement configuré. Dans votre application, vous pouvez découpler la connexion de la notification Push, tant que la connexion se produit avant la notification.

2.  Dans ViewController.m, ajoutez une constante pour le point de terminaison de votre serveur principal et renseignez les implémentations des méthodes d'action de vos UIButtons. Remplacez bien l'espace réservé par le point de terminaison du serveur principal que vous avez utilisé dans la section précédente.

        - (IBAction)LogInAction:(id)sender {
            // create authentication header and set it in register client
            NSString* username = self.UsernameField.text;
            NSString* password = self.PasswordField.text;

            [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

            __weak ViewController* selfie = self;
            [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil andCompletion: ^(NSError* error) {
                if (!error) {
                    dispatch_async(dispatch_get_main_queue(), ^{
                        selfie.SendPushButton.enabled = YES;

                        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Success" message:
                                              @"Registered successfully!" delegate:nil cancelButtonTitle:
                                              @"OK" otherButtonTitles:nil, nil];
                        [alert show];
                    });
                }
            }];
        }

        NSString *const SendNotificationEndpoint = @"{backend endpoint}/api/notifications";

        - (IBAction)SendPushAction:(id)sender {
            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:SendNotificationEndpoint];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"POST"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.registerClient.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || httpResponse.statusCode != 200)
                {
                    NSLog(@"Error status: %d, request: %@", httpResponse.statusCode, error);
                }
            }];
            [dataTask resume];
        }

3.  Enfin, dans **ViewDidLoad**, ajoutez le code qui suit pour instancier l'instance RegisterClient et définir le délégué pour vos champs de texte.

        self.UsernameField.delegate = self;
        self.PasswordField.delegate = self;
        self.registerClient = [[RegisterClient alloc] init];

4.  Maintenant, dans votre méthode **AppDelegate.m**, supprimez tout le contenu de la méthode **application:didRegisterForPushNotificationWithDeviceToken:** et remplacez-le par ce qui suit :

        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;

    Ceci permet de s'assurer que le contrôleur d'affichage contient le dernier jeton de l'appareil récupéré des APN.

5.  Toujours dans **AppDelegate.m**, assurez-vous que vous disposez de la méthode suivante :

        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                  [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
                                  @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

## Exécution de l'application

Pour exécuter l'application, procédez comme suit :

1.  Assurez-vous que **AppBackend** est déployé dans Azure. Si vous utilisez Visual Studio, exécutez l'application API web **AppBackend**. Une page web ASP.NET s'affiche.

2.  Dans XCode, exécutez l'application sur un appareil iOS physique (les notifications Push ne fonctionnent pas dans le simulateur).

3.  Dans l'interface utilisateur de l'application iOS, entrez un nom d'utilisateur et un mot de passe. La valeur peut être une chaîne quelconque, mais elle doit être identique pour les deux.

4.  Dans l'interface utilisateur de l'application iOS, cliquez sur **Log in**. Cliquez ensuite sur **Send push**.

  [Windows Universal]: /fr-fr/documentation/articles/notification-hubs-windows-dotnet-notify-users/ "Windows Universal"
  [iOS]: /fr-fr/documentation/articles/notification-hubs-/ "iOS"
  [Android]: /fr-fr/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/ "Android"
  [Inscription auprès du serveur principal de votre application]: http://msdn.microsoft.com/en-us/library/dn743807.aspx
  [Prise en main de Notification Hubs (iOS)]: http://azure.microsoft.com/fr-fr/documentation/articles/notification-hubs-ios-get-started/
  [notification-hubs-aspnet-backend-notifyusers]: ../includes/notification-hubs-aspnet-backend-notifyusers.md
  []: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users1.png
