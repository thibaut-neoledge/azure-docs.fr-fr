<properties 
	pageTitle="Didacticiel sur l’utilisation de Notification Hubs pour envoyer les dernières nouvelles - iOS" 
	description="Découvrez comment utiliser Azure Service Bus Notification Hubs pour envoyer des notifications de dernières nouvelles aux appareils iOS." 
	services="notification-hubs" 
	documentationCenter="ios" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="wesmc"/>

# Utilisation de Notification Hubs pour diffuser les dernières nouvelles

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##Vue d'ensemble

Cette rubrique montre comment utiliser Azure Notification Hubs pour diffuser des notifications relatives aux dernières nouvelles vers une application iOS. Lorsque vous aurez terminé, vous pourrez vous inscrire aux catégories de dernières nouvelles qui vous intéressent et recevoir uniquement des notifications Push pour ces catégories. Ce scénario est un modèle courant pour de nombreuses applications pour lesquelles des notifications doivent être envoyées à des groupes d'utilisateurs qui ont signalé antérieurement un intérêt, par exemple, lecteur RSS, applications pour fans de musique, etc.

Les scénarios de diffusion sont activés en incluant une ou plusieurs _balises_ lors de la création d'une inscription dans le Notification Hub. Lorsque des notifications sont envoyées à une balise, tous les appareils pour lesquels cette balise est inscrite reçoivent la notification. Les balises étant de simples chaînes, il n’est pas nécessaire de les mettre en service à l’avance. Pour plus d'informations sur les balises, consultez la page [Recommandations relatives à Notification Hubs].


##Composants requis

Cette rubrique s'appuie sur l'application que vous avez créée dans [Prise en main de Notification Hubs][get-started]. Avant de commencer ce didacticiel, vous devez suivre celui intitulé [Prise en main de Notification Hubs][get-started].

##Ajout d’une sélection de catégories à l’application

La première étape consiste à ajouter à votre storyboard existant les éléments d’interface utilisateur qui permettent à l’utilisateur de sélectionner les catégories à inscrire. Les catégories sélectionnées par un utilisateur sont stockées sur l'appareil. Lorsque l'application démarre, une inscription d'appareil est créée dans votre concentrateur de notification avec les catégories sélectionnées sous forme de balises.

1. Dans MainStoryboard_iPhone.storyboard, ajoutez les composants suivants de la bibliothèque d'objets :
	+ une étiquette intitulée « Dernières nouvelles » ;
	+ des étiquettes portant les intitulés de catégories « Monde », « Politiques », « Entreprise », « Technologies », « Science », « Sports » ;
	+ six commutateurs, un par catégorie, chacun défini sur l’**État** **Désactivé** par défaut ;
	+ un bouton intitulé « S’abonner ».

	Votre storyboard doit ressembler à ce qui suit :

	![][3]

2. Dans l’éditeur de l’Assistant, créez des outlets pour tous les commutateurs et appelez-les "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch".


3. Créez une action pour le bouton intitulé « S’abonner ». Le fichier ViewController.h doit désormais contenir le code suivant :

		@property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

		- (IBAction)subscribe:(id)sender;

4. Créez une **classe Cocoa Touch** appelée `Notifications`. Copiez le code suivant dans la section de l’interface du fichier Notifications.h :

		@property NSData* deviceToken;

		- (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories 
					completion:(void (^)(NSError* error))completion;

		- (NSSet*)retrieveCategories;

		- (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;

5. Ajoutez la directive import suivante au fichier Notifications.m :

		#import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6. Copiez le code suivant dans la section d’implémentation du fichier Notifications.m et remplacez les espaces réservés `<hub name>` et `<connection string with listen access>` par le nom du concentrateur de notification et la chaîne de connexion pour *DefaultListenSharedAccessSignature* obtenue précédemment.

		- (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

		    [self subscribeWithCategories:categories completion:completion];
		}


		- (NSSet*)retrieveCategories {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

		    NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

		    if (!categories) return [[NSSet alloc] init];
		    return [[NSSet alloc] initWithArray:categories];
		}


		- (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
		{
		    SBNotificationHub* hub = [[SBNotificationHub alloc] 
										initWithConnectionString:@"<connection string with listen access>" 
										notificationHubPath:@"<hub name>"];

		    [hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];
		}



	Cette classe utilise le stockage local pour stocker et récupérer les catégories de nouvelles que cet appareil doit recevoir. Elle comporte également une méthode pour s’inscrire à ces catégories.

	> [AZURE.NOTE]Les informations d’identification distribuées avec une application cliente n’étant généralement pas sécurisées, vous ne devez distribuer que la clé d’accès d’écoute avec votre application cliente. L'accès d'écoute permet à votre application de s'inscrire à des notifications, mais les inscriptions existantes ne peuvent pas être modifiées et les notifications ne peuvent pas être envoyées. La clé d'accès complet est utilisée dans un service de serveur principal sécurisé pour l'envoi de notifications et la modification d'inscriptions existantes.

7. Dans le fichier AppDelegate.h, ajoutez une instruction import pour Notifications.h et une propriété pour une instance de la classe Notifications :

		#import "Notifications.h"

		@property (nonatomic) Notifications* notifications;

	Cela permet de créer une instance singleton de la classe Notification dans AppDelegate.

8. Dans la méthode **didFinishLaunchingWithOptions** du fichier AppDelegate.m, ajoutez ce code pour initialiser l’instance de notifications au début de la méthode :

		self.notifications = [[Notifications alloc] init];

	Ce dernier initialise le singleton Notification.


9. Dans la méthode **didRegisterForRemoteNotificationsWithDeviceToken** du fichier AppDelegate.m, remplacez le code de la méthode par le code suivant pour transmettre le jeton d’appareil à la classe Notifications. La classe Notifications effectue l’enregistrement pour les notifications avec les catégories. Si l’utilisateur modifie les sélections de catégorie, nous appelons la méthode `subscribeWithCategories` en réponse au bouton **S’abonner** pour mettre à jour les sections.

	> [AZURE.NOTE]Étant donné que le jeton d’appareil attribué par le service de notification Push Apple (APN, Apple Push Notification) peut être modifié à tout moment, vous devez vous inscrire aux notifications à intervalles réguliers pour éviter les défaillances de notification. Cet exemple s'inscrit aux notifications chaque fois que l'application démarre. Pour les applications exécutées fréquemment, plus d'une fois par jour, vous pouvez probablement ignorer l'inscription afin de préserver la bande passante si moins d'un jour s'est écoulé depuis l'inscription précédente.

		self.notifications.deviceToken = deviceToken;

		// Retrieves the categories from local storage and requests a registration for these categories
		// each time the app starts and performs a registration.

	    NSSet* categories = [self.notifications retrieveCategories];
	    [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];


	À ce stade, il est à noter qu'il ne doit pas y avoir d'autre code dans la méthode **didRegisterForRemoteNotificationsWithDeviceToken**.

10.	Les méthodes suivantes doivent déjà être présentes dans AppDelegate.m depuis la fin du didacticiel [Prise en main de Notification Hubs][get-started]. Si ce n’est pas le cas, veuillez les ajouter.

		-(void)MessageBox:(NSString *)title message:(NSString *)messageText
		{
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self 
				cancelButtonTitle:@"OK" otherButtonTitles: nil];
			[alert show];
		}

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:
			(NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
	    }

	Cette méthode gère les notifications reçues lorsque l'application est en cours d'exécution en affichant tout simplement une **UIAlert**.

11. Dans le fichier ViewController.m, ajoutez une instruction import pour AppDelegate.h et copiez le code suivant dans la méthode **subscribe** générée par le Xcode. Ce code met à jour l’inscription aux notifications pour utiliser les nouvelles balises de catégories choisies par l’utilisateur dans l’interface utilisateur.

		NSMutableArray* categories = [[NSMutableArray alloc] init];

	    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
	    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
	    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
	    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
	    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
	    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

	    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
	        if (!error) {
	            [self MessageBox:@"Notification" message:@"Subscribed!"];
	        } else {
	            NSLog(@"Error subscribing: %@", error);
	        }
	    }];

	Cette méthode crée une liste **NSMutableArray** de catégories et utilise la classe **Notifications** pour stocker la liste dans le stockage local et inscrire les balises correspondantes auprès du concentrateur de notification. Lorsque des catégories sont modifiées, l'inscription est à nouveau créée avec les nouvelles catégories.

12. Dans ViewController.m, ajoutez le code suivant dans la méthode **viewDidLoad** pour définir l’interface utilisateur en fonction des catégories précédemment enregistrées.


		// This updates the UI on startup based on the status of previously saved categories.

		Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    NSSet* categories = [notifications retrieveCategories];

	    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
	    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
	    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
	    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
	    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
	    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;



L’application peut désormais stocker un ensemble de catégories dans le stockage local de l’appareil utilisé pour s’inscrire auprès du Notification Hub au démarrage de l’application. L’utilisateur peut modifier la sélection des catégories au démarrage et cliquer sur la méthode **subscribe** pour mettre à jour l’inscription de l’appareil. Ensuite, vous allez mettre à jour l’application pour envoyer les notifications de dernières nouvelles directement dans l’application elle-même.


##Envoi de notifications

Normalement, les notifications doivent être envoyées par un service principal mais, pour ce didacticiel, nous allons mettre à jour notre code d’envoi de notifications pour pouvoir envoyer des notifications de dernières nouvelles directement à partir de l’application. Pour cela, nous allons mettre à jour la méthode `SendNotificationRESTAPI` que nous avons définie dans le didacticiel [Prise en main de Notification Hubs][get-started].


1. Dans ViewController.m, mettez à jour la méthode `SendNotificationRESTAPI` comme suit, pour qu’elle récupère un paramètre `pns` du service de notification de la plateforme et un paramètre pour la balise de catégorie.

		- (void)SendNotificationRESTAPI:(NSString*)pns Category:(NSString*)categoryTag
		{
		    NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
									 defaultSessionConfiguration] delegate:nil delegateQueue:nil];
		    
		    NSString *json;
		    
		    // Construct the messages REST endpoint
		    NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
		                                       HUBNAME, API_VERSION]];
		    
		    // Generated the token to be used in the authorization header.
		    NSString* authorizationToken = [self generateSasToken:[url absoluteString]];
		    
		    //Create the request to add the APNS notification message to the hub
		    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
		    [request setHTTPMethod:@"POST"];
		    
		    // Add the category as a tag
		    [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];
		
		    // Windows Notification format of the notification message
		    if ([pns isEqualToString:@"wns"])
		    {
		        json = [NSString stringWithFormat:@"<?xml version="1.0" encoding="utf-8"?>"
		                                           "<toast>"
		                                           "<visual><binding template="ToastText01">"
		                                           "<text id="1">Breaking %@ News : %@</text>"
		                                           "</binding>"
		                                           "</visual>"
		                                           "</toast>",
		                categoryTag, self.notificationMessage.text];
		        
		        // Signify windows notification format
		        [request setValue:@"windows" forHTTPHeaderField:@"ServiceBusNotification-Format"];
		        
		        // XML Content-Type
		        [request setValue:@"application/xml" forHTTPHeaderField:@"Content-Type"];
		        
		        // Set X-WNS-TYPE header
		        [request setValue:@"wns/toast" forHTTPHeaderField:@"X-WNS-Type"];
		    }
		    
		    // Google Cloud Messaging Notification format of the notification message
		    if ([pns isEqualToString:@"gcm"])
		    {
		        json = [NSString stringWithFormat:@"{"data":{"message":"Breaking %@ News : %@"}}",
		                categoryTag, self.notificationMessage.text];
		        // Signify gcm notification format
		        [request setValue:@"gcm" forHTTPHeaderField:@"ServiceBusNotification-Format"];

				// JSON Content-Type
				[request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
		    }
		    
		    // Apple Notification format of the notification message
		    if ([pns isEqualToString:@"apns"])
		    {
		        json = [NSString stringWithFormat:@"{"aps":{"alert":"Breaking %@ News : %@"}}",
		                categoryTag, self.notificationMessage.text];
		        // Signify apple notification format
		        [request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];

				// JSON Content-Type
				[request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
		    }
		    
		    //Authenticate the notification message POST request with the SaS token
		    [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];
		    
		    //Add the notification message body
		    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
		    
		    // Send the REST request
		    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
		               completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
		               {
		               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		                   if (error || httpResponse.statusCode != 200)
		                   {
		                       NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
		                   }
		                   if (data != NULL)
		                   {
		                       //xmlParser = [[NSXMLParser alloc] initWithData:data];
		                       //[xmlParser setDelegate:self];
		                       //[xmlParser parse];
		                   }
		               }];
		    [dataTask resume];
		}
		


2. Dans la mise à jour ViewController.m, mettez à jour l’action **Envoyer des notifications** comme indiqué dans le code suivant. Ainsi, les notifications sont envoyées à l’aide de chaque balise de manière individuelle vers plusieurs plateformes.



		- (IBAction)SendNotificationMessage:(id)sender
		{
		    self.sendResults.text = @"";
		    
		    NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business", 
									@"Technology", @"Science", @"Sports", nil];
		
		    // Lets send the message as breaking news for each category to WNS, GCM, and APNS
		    for(NSString* category in categories)
		    {
		        [self SendNotificationRESTAPI:@"wns" Category:category];
		        [self SendNotificationRESTAPI:@"gcm" Category:category];
		        [self SendNotificationRESTAPI:@"apns" Category:category];
		    }
		}



3. Régénérez votre projet et vérifiez qu’il n’existe aucune erreur de génération.


##Exécution de l’application et génération de notifications

1. Cliquez sur le bouton Exécuter pour générer le projet et démarrer l’application. Sélectionnez certaines options de dernières nouvelles pour vous y abonner, puis appuyez sur le bouton **S’abonner**. Vous devez voir une boîte de dialogue indiquant les notifications auxquelles vous êtes abonné.

	![][1]

	Lorsque vous sélectionnez **S’abonner**, l'application convertit les catégories sélectionnées en balises et demande une nouvelle inscription de l'appareil aux balises sélectionnées depuis le concentrateur de notification.

2. Entrez un message à envoyer comme dernières nouvelles, puis appuyez sur le bouton **Envoyer des notifications**

	![][2]


3. Chaque appareil abonné aux dernières nouvelles reçoit les notifications de dernières nouvelles que vous venez d’envoyer.

	![][4] ![][5]


## Étapes suivantes

Dans ce didacticiel, nous avons appris à diffuser les dernières nouvelles par catégorie. Envisagez de suivre un des didacticiels suivants qui soulignent d’autres scénarios avancés Notification Hubs :

+ **[Utilisation de Notification Hubs pour diffuser les dernières nouvelles localisées]**

	Apprenez à développer l’application relative aux dernières nouvelles pour permettre l’envoi de notifications localisées.

+ **[Notification des utilisateurs avec Notification Hubs]**

	Apprenez à transmettre des notifications à des utilisateurs authentifiés spécifiques. Il s’agit d’une solution appropriée pour l’envoi de notifications uniquement vers des utilisateurs spécifiques.



<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png
[4]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews1.png
[5]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews2.png







<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Utilisation de Notification Hubs pour diffuser les dernières nouvelles localisées]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification des utilisateurs avec Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Azure Management Portal]: https://manage.windowsazure.com/
[Recommandations relatives à Notification Hubs]: http://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/
 

<!---HONumber=July15_HO4-->