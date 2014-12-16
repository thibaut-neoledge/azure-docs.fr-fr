<properties title="Azure Notification Hubs Notify Users" pageTitle="Notification des utilisateurs via Azure Notification Hubs" metaKeywords="notifications push Azure, concentrateurs de notification Azure" description="Learn how to send secure push notifications in Azure. Code samples written in Objective-C using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="yuaxu" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="yuaxu" />

#Notification des utilisateurs via Azure Notification Hubs

<div class="dev-center-tutorial-selector sublanding">
    	<a href="/fr-fr/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal">Windows Universal</a><a href="/fr-fr/documentation/articles/notification-hubs-/" title="iOS" class="current">iOS</a>
		<a href="/fr-fr/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

La prise en charge des notifications Push dans Azure vous permet d'accéder à une infrastructure Push conviviale, multi-plateforme et avec montée en charge qui simplifie fortement l'implémentation des notifications Push pour les applications consommateur et entreprise pour les plateformes mobiles. Ce didacticiel explique comment utiliser Azure Notification Hubs pour envoyer des notifications Push à un utilisateur particulier d'une application sur un appareil spécifique. Un code WebAPI principal ASP.NET est utilisé pour authentifier les clients et pour générer les notifications, comme présenté dans la rubrique de conseils [Inscription auprès du serveur principal de votre application](http://msdn.microsoft.com/fr-fr/library/dn743807.aspx).

> [AZURE.NOTE] Ce didacticiel part du principe que vous avez créé et configuré votre concentrateur de notification comme décrit dans la rubrique [Prise en main de Notification Hubs (iOS)](http://azure.microsoft.com/fr-fr/documentation/articles/notification-hubs-ios-get-started/). Ce didacticiel est également un prérequis pour le didacticiel [sur les notifications Push sécurisées (iOS)](http://azure.microsoft.com/fr-fr/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/).


## Création et configuration du concentrateur de notification

Veuillez suivre les sections 1 à 5 de la rubrique [Prise en main de Notification Hubs (iOS)](http://azure.microsoft.com/fr-fr/documentation/articles/notification-hubs-ios-get-started/). Pour obtenir des ressources supplémentaires sur l'approvisionnement de périphériques iOS, consultez le guide sur le site [Big Nerd Ranch](http://www.bignerdranch.com/we-teach/how-to-prepare/ios-device-provisioning.html)..

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Modification de votre application iOS

1. Ouvrez l'application Affichage page simple que vous avez créée dans les sections 1 à 5 de la rubrique [Prise en main de Notification Hubs (iOS)](http://azure.microsoft.com/fr-fr/documentation/articles/notification-hubs-ios-get-started/).

> [AZURE.NOTE] Dans cette section, nous partons du principe que votre projet a été configuré avec un nom d'organisation vide. Si ce n'est pas le cas, vous devez faire précéder tous les noms de classe du nom de votre organisation.

2. Dans Main.storyboard, ajoutez les composants suivants de la bibliothèque d'objets :
	+ Un champ UITextField avec **Username** pour le texte de l'espace réservé.
	+ Un champ UITextField avec le texte d'espace réservé **Mot de passe**, et activez l'option **Sécurisé** dans l'Inspecteur d'attributs, sous Clé de retour de champ de texte.
	+ Un UIButton intitulé **1. Se connecter** et désactivez l'option **Activé** dans l'inspecteur d'attributs, sous Contrôle puis Contenu.
	+ Un UIButton intitulé **2. Envoyer Push**, et désactivez l'option **Activé**.

	Votre storyboard doit ressembler à ce qui suit :

    ![][IOS1]

3. Créez des outlets pour les deux UITextFields et les boutons UIButtons dans la partie interface de votre ViewController.h

	    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
		@property (weak, nonatomic) IBOutlet UITextField *PasswordField;
		@property (weak, nonatomic) IBOutlet UIButton *LogInButton;
		@property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

		- (IBAction)LogInAction:(id)sender;
		- (IBAction)SendPushAction:(id)sender;

4. Tout d'abord, nous allons créer une classe RegisterClient pour assurer l'interface avec notre serveur principal. Créez une classe Objective-C appelée RegisterClient, qui hérite de NSObject. Ensuite, ajoutez le code qui suit dans la section interface de RegisterClient.h :

		@property (strong, nonatomic) NSString* authenticationHeader;
		-(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;

5. Dans RegisterClient.m, ajoutez la section d'interface suivante :

		@interface RegisterClient ()

		@property (strong, nonatomic) NSURLSession* session;
		-(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
		-(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
		-(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSString*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;

		@end

6. Ajoutez le code qui suit dans la section d'implémentation RegisterClient.m et remplacez l'espace réservé *{backend endpoint}* par le point de terminaison que vous avez utilisé pour déployer votre serveur principal d'application dans la section précédente.

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

	Le code ci-dessus implémente la logique expliquée dans l'article [Inscription auprès du serveur principal de votre application](http://msdn.microsoft.com/fr-fr/library/dn743807.aspx), et utilise NSURLSession pour passer des appels REST à votre serveur principal d'application et NSUserDefaults pour stocker en local la valeur registrationId renvoyée par le concentrateur de notification.

	Notez que cette classe a besoin que sa propriété **authorizationHeader** soit définie afin de fonctionner correctement. Cette propriété est définie par la classe **ViewController** après la connexion.

7. Dans ViewController.h, ajoutez la déclaration suivante pour la référence et le jeton de l'appareil à une instance RegisterClient :

		@property (strong, nonatomic) NSData* deviceToken;
		@property (strong, nonatomic) RegisterClient* registerClient;

8. Dans ViewController.m, convertissez la classe ViewController en UITextFieldDelegate. Ajoutez ensuite une déclaration de méthode privée :

		@interface ViewController () <UITextFieldDelegate>

		// create the Authorization header to perform Basic authentication with your app back-end
		-(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;

		@end

> [AZURE.NOTE] L'extrait de code suivant n'est pas un schéma d'authentification sécurisé. Vous devez remplacer l'implémentation de **createAndSetAuthenticationHeaderWithUsername:AndPassword:** par votre mécanisme d'authentification spécifique qui génère un jeton d'authentification destiné à votre classe de client d'inscription, par exemple OAuth, Active Directory.

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

	Remarquez la façon dont le jeton de l'appareil active le bouton de connexion. Ceci est dû au fait que dans le cadre de l'action de connexion, le contrôleur d'affichage s'inscrit aux notifications Push avec le serveur principal d'application. Par conséquent, nous ne souhaitons pas que l'action de connexion (LogInAction) soit accessible tant que le jeton de l'appareil n'a pas été configuré correctement. Vous pouvez découpler la connexion résultant de l'inscription par émission de données tant que la première se produit avant la seconde.

10. Dans ViewController.m, ajoutez une constante pour le point de terminaison de votre serveur principal et utilisez les extraits de code suivant pour implémenter les méthodes d'action de vos UIButtons. Remplacez l'espace réservé du point de terminaison du serveur principal par l'URL de destination que vous avez utilisée pour votre serveur principal.

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

11. Dans la fonction **ViewDidLoad**, ajoutez le code qui suit pour instancier l'instance RegisterClient et définir le délégué pour vos champs de texte.

		self.UsernameField.delegate = self;
		self.PasswordField.delegate = self;
		self.registerClient = [[RegisterClient alloc] init];

12. Maintenant, dans votre méthode **AppDelegate.m**, supprimez tout le contenu de la méthode **application:didRegisterForPushNotificationWithDeviceToken:** et remplacez-le par le code suivant pour vous assurer que le contrôleur d'affichage contienne le dernier jeton d'appareil extrait des APN :

	    ViewController* rvc = (ViewController*) self.window.rootViewController;
	    rvc.deviceToken = deviceToken;

13. Enfin, dans **AppDelegate.m**, assurez-vous que vous disposez de la méthode suivante :

		- (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		                          [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
		                          @"OK" otherButtonTitles:nil, nil];
		    [alert show];
		}

## Exécution de l'application

1. Dans XCode, exécutez l'application sur un appareil iOS physique (les notifications Push ne fonctionnent pas dans le simulateur).

2. Dans l'interface utilisateur de l'application iOS, entrez un nom d'utilisateur et un mot de passe. La valeur peut être une chaîne quelconque, mais elle doit être identique pour les deux. Ensuite, cliquez sur **Connexion**.

3. Un menu contextuel devrait apparaître pour vous informer de la réussite de l'inscription. Cliquez sur **OK**.

4. Cliquez sur **Send push** et appuyez sur le bouton Accueil. Une notification Push apparaîtra sous peu.


[IOS1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users1.png
