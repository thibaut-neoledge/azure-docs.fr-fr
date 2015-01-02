<properties title="Azure Notification Hubs Notify Users" pageTitle="Notification des utilisateurs via Azure Notification Hubs" metaKeywords="Azure push notifications, Azure notification hubs" description="Learn how to send secure push notifications in Azure. Code samples written in Objective-C using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="yuaxu" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="11/22/2014" ms.author="yuaxu" />

#Notification des utilisateurs via Azure Notification Hubs

<div class="dev-center-tutorial-selector sublanding">
    	<a href="/fr-fr/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/" title="Windows Universal">Windows universel</a><a href="/fr-fr/documentation/articles/notification-hubs-/" title="iOS" class="current">iOS</a>
		<a href="/fr-fr/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

La prise en charge des notifications Push dans Azure vous permet d'accéder à une infrastructure Push conviviale, multi-plateforme et avec montée en charge qui simplifie fortement l'implémentation des notifications Push pour les applications consommateur et entreprise pour les plateformes mobiles. Ce didacticiel explique comment utiliser Azure Notification Hubs pour envoyer des notifications Push à un utilisateur particulier d'une application sur un appareil spécifique. Un code WebAPI principal ASP.NET est utilisé pour authentifier les clients et pour générer les notifications, comme présenté dans la rubrique de conseils [Inscription auprès du serveur principal de votre application](http://msdn.microsoft.com/fr-fr/library/dn743807.aspx).

> [AZURE.NOTE] Ce didacticiel part du principe que vous avez créé et configuré votre concentrateur de notification comme décrit dans la rubrique [Prise en main de Notification Hubs (iOS)](http://azure.microsoft.com/fr-fr/documentation/articles/notification-hubs-ios-get-started/). Ce didacticiel est également un prérequis au didacticiel sur les [notifications Push sécurisées (iOS)](http://azure.microsoft.com/fr-fr/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/) .
> Si vous utilisez Mobile Services comme service principal, consultez la [version consacrée à Mobile Services](/fr-fr/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/) de ce didacticiel.


## Création et configuration du concentrateur de notification

Suivez les sections 1 à 5 de la rubrique [Prise en main de Notification Hubs (iOS)](http://azure.microsoft.com/fr-fr/documentation/articles/notification-hubs-ios-get-started/). Pour obtenir des ressources supplémentaires sur l'approvisionnement d'appareils iOS, consultez le guide sur le site [Big Nerd Ranch](http://www.bignerdranch.com/we-teach/how-to-prepare/ios-device-provisioning.html).

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Modification de votre application iOS

1. Ouvrez l'application d'affichage Page simple que vous avez créée dans les sections 1 à 5 de la rubrique [Prise en main de Notification Hubs (iOS)](http://azure.microsoft.com/fr-fr/documentation/articles/notification-hubs-ios-get-started/).

> [AZURE.NOTE] Dans cette section, nous partons du principe que vous avez configuré votre projet avec un nom d'organisation vide. Si ce n'est pas le cas, vous devez ajouter le nom de votre organisation à tous les noms de classe.

2. Dans Main.storyboard, ajoutez les composants suivants de la bibliothèque d'objets :
	+ un champ UITextField avec comme texte d'espace réservé **Username** ;
	+ un champ UITextField avec comme texte d'espace réservé **Password** et l'option **Secure** activée dans Attribute Inspector, sous Textfield Return Key ;
	+ un bouton UIButton intitulé **1. Log in** et l'option **Enabled** désactivée dans Attributes Inspector, sous Control et Content ;
	+ un bouton UIButton intitulé **2. Send Push** et l'option **Enabled** désactivée.

	Votre storyboard doit ressembler à ce qui suit :

    ![][IOS1]

3. Créez des outlets pour les champs UITextField et les boutons UIButton dans la partie interface de votre ViewController.h.

	    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
		@property (weak, nonatomic) IBOutlet UITextField *PasswordField;
		@property (weak, nonatomic) IBOutlet UIButton *LogInButton;
		@property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

		- (IBAction)LogInAction:(id)sender;
		- (IBAction)SendPushAction:(id)sender;

4. Tout d'abord, nous allons créer une classe RegisterClient pour assurer l'interface avec notre serveur principal. Créez une classe Objective-C appelée RegisterClient, qui hérite de NSObject. Puis ajoutez le code qui suit dans la section interface de RegisterClient.h :

		@property (strong, nonatomic) NSString* authenticationHeader;
		-(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;

5. Dans RegisterClient.m, ajoutez la section d'interface suivante :

		@interface RegisterClient ()

		@property (strong, nonatomic) NSURLSession* session;
		-(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
		-(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
		-(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSString*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;

		@end

6. Ajoutez le code qui suit dans la section d'implémentation RegisterClient.m et remplacez l'espace réservé *{backend endpoint}* par l'URL de destination que vous avez utilisée pour déployer votre serveur principal d'application dans la section précédente.

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

	Le code ci-dessus implémente la logique expliquée dans l'article d'assistance intitulé [Inscription auprès du serveur principal de votre application],(http://msdn.microsoft.com/fr-fr/library/dn743807.aspx) utilisant NSURLSession pour adresser des appels REST au serveur principal de votre application et NSUserDefaults pour stocker localement le registrationId retourné par le concentrateur de notification.

	Notez que cette classe a besoin que sa propriété **authorizationHeader** soit définie pour pouvoir fonctionner correctement. Cette propriété est définie par la classe **ViewController** après la connexion.

7. Dans ViewController.h, ajoutez la déclaration suivante pour le jeton de l'appareil et la référence à une instance de RegisterClient :

		@property (strong, nonatomic) NSData* deviceToken;
		@property (strong, nonatomic) RegisterClient* registerClient;

8. Dans ViewController.m, transformez la classe ViewController en UITextFieldDelegate. Ajoutez ensuite une déclaration de méthode privée :

		@interface ViewController () <UITextFieldDelegate>

		// create the Authorization header to perform Basic authentication with your app back-end
		-(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;

		@end

> [AZURE.NOTE] L'extrait de code suivant n'étant pas un système d'authentification sécurisé, vous devez remplacer l'implémentation de **createAndSetAuthenticationHeaderWithUsername:AndPassword:** par votre mécanisme d'authentification spécifique qui génère un jeton d'authentification devant être consommé par la classe cliente du registre, par exemple, OAuth, Active Directory.

9. Ensuite, dans la section d'implémentation de ViewController.m, ajoutez le code qui suit :

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

	Remarquez la façon dont le jeton de l'appareil active le bouton de connexion. Ceci est dû au fait que dans le cadre de l'action de connexion, le contrôleur d'affichage s'inscrit aux notifications Push auprès du serveur principal d'application. De ce fait, l'action de connexion ne doit pas être accessible tant que le jeton de l'appareil n'a pas été correctement configuré. Vous pouvez découpler la connexion de l'inscription aux notifications Push dans la mesure où la connexion se produit avant l'inscription.

10. Dans ViewController.m, ajoutez une constante pour le point de terminaison de votre serveur principal et utilisez les extraits de code suivants pour implémenter les méthodes d'action de vos boutons UIButton. Remplacez l'espace réservé du point de terminaison du serveur principal par l'URL de destination que vous avez utilisée pour votre serveur principal.

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

11. Dans la fonction **ViewDidLoad**, ajoutez ce qui suit pour instancier l'instance de RegisterClient et définir le délégué pour vos champs de texte.

		self.UsernameField.delegate = self;
		self.PasswordField.delegate = self;
		self.registerClient = [[RegisterClient alloc] init];

12. À présent, dans **AppDelegate.m**, supprimez tout le contenu de la méthode **application:didRegisterForPushNotificationWithDeviceToken:** et remplacez-le par ce qui suit pour faire en sorte que le contrôleur d'affichage contienne le dernier jeton de l'appareil extrait des APN :

	    ViewController* rvc = (ViewController*) self.window.rootViewController;
	    rvc.deviceToken = deviceToken;

13. Enfin, assurez-vous que **AppDelegate.m** contient la méthode suivante :

		- (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		                          [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
		                          @"OK" otherButtonTitles:nil, nil];
		    [alert show];
		}

## Exécution de l'application

1. Dans XCode, exécutez l'application sur un appareil iOS physique (les notifications Push ne fonctionnent pas dans le simulateur).

2. Dans l'interface utilisateur de l'application iOS, entrez un nom d'utilisateur et un mot de passe. La valeur peut être une chaîne quelconque, mais elle doit être identique pour les deux. Cliquez ensuite sur **Log In**.

3. Une fenêtre contextuelle doit s'afficher pour vous informer que l'inscription a abouti. Cliquez sur **OK**.

4. Cliquez sur **Send push** et appuyez sur le bouton d'accueil. Une notification Push s'affiche peu de temps après.


[IOS1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users1.png

<!--HONumber=35_1-->
