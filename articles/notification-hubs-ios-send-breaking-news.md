<properties 
	pageTitle="Didacticiel sur l'utilisation de Notification Hubs pour envoyer les dernières nouvelles - iOS" 
	description="Découvrez comment utiliser Azure Service Bus Notification Hubs pour envoyer des notifications de dernières nouvelles aux appareils iOS." 
	services="notification-hubs" 
	documentationCenter="ios" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="wesmc"/>

# Utilisation de Notification Hubs pour diffuser les dernières nouvelles
<div class="dev-center-tutorial-selector sublanding">
	<a href="/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/" title="Windows Universal" >Windows Universal</a><a href="/documentation/articles/notification-hubs-windows-phone-send-breaking-news/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/notification-hubs-ios-send-breaking-news/" title="iOS" class="current">iOS</a>
	<a href="/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/" title="Android">Android</a>
</div>

##Vue d'ensemble

Cette rubrique montre comment utiliser Azure Notification Hubs pour diffuser des notifications relatives aux dernières nouvelles vers une application iOS. Lorsque vous aurez terminé, vous pourrez vous inscrire aux catégories de dernières nouvelles qui vous intéressent et recevoir uniquement des notifications Push pour ces catégories. Ce scénario est un modèle courant pour de nombreuses applications pour lesquelles des notifications doivent être envoyées à des groupes d'utilisateurs qui ont signalé antérieurement un intérêt, par exemple, lecteur RSS, applications pour fans de musique, etc.

Les scénarios de diffusion sont activés en incluant une ou plusieurs _balises_ lors de la création d'une inscription dans le concentrateur de notification. Lorsque des notifications sont envoyées à une balise, tous les appareils pour lesquels cette balise est inscrite reçoivent la notification. Les balises étant de simples chaînes, il n'est pas nécessaire de les mettre en service à l'avance. Pour plus d'informations sur les balises, consultez la page [Recommandations relatives à Notification Hubs].


##Conditions préalables

Cette rubrique s'appuie sur l'application que vous avez créée dans [Prise en main de Notification Hubs][get-started]. Avant de commencer ce didacticiel, vous devez suivre celui intitulé [Prise en main de Notification Hubs][get-started].

##Ajout d'une sélection de catégories à l'application

La première étape consiste à ajouter à votre storyboard existant les éléments d'interface utilisateur qui permettent à l'utilisateur de sélectionner les catégories à inscrire. Les catégories sélectionnées par un utilisateur sont stockées sur l'appareil. Lorsque l'application démarre, une inscription d'appareil est créée dans votre concentrateur de notification avec les catégories sélectionnées sous forme de balises.

2. Dans MainStoryboard_iPhone.storyboard, ajoutez les composants suivants de la bibliothèque d'objets :
	+ une étiquette intitulée "Breaking News" ;
	+ des étiquettes portant les intitulés de catégories "World", "Politics", "Business", "Technology", "Science", "Sports" ;
	+ six commutateurs, un par catégorie ;
	+ un bouton intitulé "Subscribe".

	Votre storyboard doit ressembler à ce qui suit :

	![][3]

3. Dans l'éditeur de l'Assistant, créez des outlets pour tous les commutateurs et appelez-les "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch".

	![][4]

4. Créez une action pour le bouton intitulé "Subscribe". Le fichier BreakingNewsViewController.h doit désormais contenir le code suivant :

		@property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

		- (IBAction)subscribe:(id)sender;

5. Créez une classe nommée `Notifications`. Copiez le code suivant dans la section de l'interface du fichier Notifications.h :

		@property NSData* deviceToken;

		- (void)storeCategoriesAndSubscribeWithCategories:(NSArray*) categories completion: (void (^)(NSError* error))completion;
		- (void)subscribeWithCategories:(NSSet*) categories completion:(void (^)(NSError *))completion;

6. Ajoutez la directive import suivante au fichier Notifications.m :

		#import <WindowsAzureMessaging/WindowsAzureMessaging.h>

7. Copiez le code suivant dans la section d'implémentation du fichier Notifications.m :

		- (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

		    [self subscribeWithCategories:categories completion:completion];
		}

		- (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion{
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string with listen access>" notificationHubPath:@"<hub name>"];

		    [hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];
		}

	Cette classe utilise le stockage local pour stocker les catégories de nouvelles que cet appareil doit recevoir. Elle contient également les méthodes pour inscrire ces catégories.

4. Dans le code ci-dessus, remplacez les espaces réservés `<hub name>` et `<connection string with listen access>` par le nom de votre concentrateur de notification et par la chaîne de connexion de la signature *DefaultListenSharedAccessSignature* obtenue précédemment.

	> [AZURE.NOTE] Les informations d'identification distribuées avec une application cliente n'étant généralement pas sécurisées, vous ne devez distribuer que la clé d'accès d'écoute avec votre application cliente. L'accès d'écoute permet à votre application de s'inscrire à des notifications, mais les inscriptions existantes ne peuvent pas être modifiées et les notifications ne peuvent pas être envoyées. La clé d'accès complet est utilisée dans un service de serveur principal sécurisé pour l'envoi de notifications et la modification d'inscriptions existantes.

8. Dans le fichier BreakingNewsAppDelegate.h, ajoutez la propriété suivante :

		@property (nonatomic) Notifications* notifications;

	Cela permet de créer une instance singleton de la classe Notification dans AppDelegate.

9. Dans la méthode **didFinishLaunchingWithOptions** du fichier BreakingNewsAppDelegate.m, ajoutez le code suivant avant **registerForRemoteNotificationTypes** :

		self.notifications = [[Notifications alloc] init];

	The initializes the Notification singleton.

10. Dans la méthode **didRegisterForRemoteNotificationsWithDeviceToken** du fichier BreakingNewsAppDelegate.m, retirez l'appel à **registerNativeWithDeviceToken** et ajoutez le code suivant :

		self.notifications.deviceToken = deviceToken;

	À ce stade, il convient de noter qu'il ne doit pas y avoir d'autre code dans la méthode **didRegisterForRemoteNotificationsWithDeviceToken**.

11.	Ajoutez la méthode suivante dans le fichier BreakingNewsAppDelegate.m :

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:
			(NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		    [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
		    [alert show];
	    }

	Cette méthode gère les notifications reçues lorsque l'application s'exécute en affichant une simple **UIAlert**.

9. Dans le fichier BreakingNewsViewController.m, copiez le code suivant dans la méthode **subscribe** générée par XCode :

		NSMutableArray* categories = [[NSMutableArray alloc] init];

	    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
	    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
	    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
	    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
	    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
	    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

	    Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
	        if (!error) {
	            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
	                                  @"Subscribed!" delegate:nil cancelButtonTitle:
	                                  @"OK" otherButtonTitles:nil, nil];
	            [alert show];
	        } else {
	            NSLog(@"Error subscribing: %@", error);
	        }
	    }];

	Cette méthode crée une liste **NSMutableArray** de catégories et utilise la classe **Notifications** pour stocker la liste dans le stockage local et inscrire les balises correspondantes auprès du concentrateur de notification. Lorsque des catégories sont modifiées, l'inscription est à nouveau créée avec les nouvelles catégories.

Votre application peut désormais stocker un ensemble de catégories dans le stockage local sur l'appareil et s'inscrire auprès du concentrateur de notification lorsque l'utilisateur modifie la sélection des catégories.

##Inscription à des notifications

Les étapes suivantes permettent l'inscription auprès du concentrateur de notification au démarrage en utilisant les catégories qui ont été stockées dans le stockage local.

> [AZURE.NOTE] Étant donné que le jeton d'appareil attribué par le service de notification Push Apple (APN, Apple Push Notification) peut être modifié à tout moment, vous devez vous inscrire aux notifications à intervalles réguliers pour éviter les défaillances de notification. Cet exemple s'inscrit aux notifications chaque fois que l'application démarre. Pour les applications exécutées fréquemment, plus d'une fois par jour, vous pouvez probablement ignorer l'inscription afin de préserver la bande passante si moins d'un jour s'est écoulé depuis l'inscription précédente.

1. Ajoutez la méthode suivante dans la section de l'interface du fichier Notifications.h :

		- (NSSet*)retrieveCategories;

	Ce code permet d'extraire les catégories de la classe Notifications.

2. Ajoutez l'implémentation correspondante dans le fichier Notifications.m :

		- (NSSet*)retrieveCategories {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

		    NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

		    if (!categories) return [[NSSet alloc] init];
		    return [[NSSet alloc] initWithArray:categories];
		}

2. Ajoutez le code suivant à la méthode **didRegisterForRemoteNotificationsWithDeviceToken** :

		Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    NSSet* categories = [notifications retrieveCategories];
	    [notifications subscribeWithCategories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];

	Cette opération garantit que chaque fois que l'application démarre, elle récupère les catégories du stockage local et demande une inscription pour ces catégories.

3. Dans le fichier BreakingNewsViewController.h, ajoutez le code suivant à la méthode **viewDidLoad** :

		Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    NSSet* categories = [notifications retrieveCategories];

	    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
	    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
	    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
	    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
	    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
	    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;

	Ce code permet de mettre à jour l'interface utilisateur au démarrage en fonction de l'état des catégories précédemment enregistrées.

L'application est désormais terminée et peut stocker un ensemble de catégories dans le stockage local de l'appareil utilisé pour s'inscrire auprès du concentrateur de notification lorsque l'utilisateur modifie la sélection des catégories. Vous allez ensuite définir un serveur principal qui pourra envoyer des notifications de catégories à cette application.

##Envoi de notifications à partir de votre serveur principal

[AZURE.INCLUDE [notification-hubs-back-end](../includes/notification-hubs-back-end.md)]

##Exécution de l'application et génération de notifications

1. Appuyez sur le bouton Exécuter pour générer le projet et démarrer l'application.

	![][2]

	Notez que l'interface utilisateur de l'application fournit un ensemble de bascules qui vous permet de choisir les catégories auxquelles vous abonner.

2. Activez un ou plusieurs boutons bascules de catégories, puis cliquez sur **S'abonner**.

	Lorsque vous sélectionnez **S'abonner**, l'application convertit les catégories sélectionnées en balises et demande une nouvelle inscription de l'appareil aux balises sélectionnées depuis le concentrateur de notification.

4. Envoyez une nouvelle notification depuis le serveur principal de l'une des manières suivantes :

	+ **Application console :** démarrez l'application console.

	+ **Java/PHP:** exécutez votre application/script.

5. Les notifications pour les catégories sélectionnées apparaissent comme notifications toast.

## Étapes suivantes

Dans ce didacticiel, nous avons appris à diffuser les dernières nouvelles par catégorie. Envisagez de suivre un des didacticiels suivants qui soulignent d'autres scénarios avancés Notification Hubs :

+ **[Utilisation de Notification Hubs pour diffuser les dernières nouvelles localisées]**

	Apprenez à développer l'application relative aux dernières nouvelles pour permettre l'envoi de notifications localisées.

+ **[Notification des utilisateurs avec Notification Hubs]**

	Apprenez comment transmettre des notifications à des utilisateurs authentifiés spécifiques. Il s'agit d'une solution appropriée pour l'envoi de notifications uniquement vers des utilisateurs spécifiques.



<!-- Images. -->
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png
[4]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios3.png







<!-- URLs. -->
[Procédure : Service Bus Notification Hubs (applications iOS)]: http://msdn.microsoft.com/library/jj927168.aspx
[Utilisation de Notification Hubs pour diffuser les dernières nouvelles localisées]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification des utilisateurs avec Notification Hubs]: /manage/services/notification-hubs/notify-users/

[Portail de gestion Azure]: https://manage.windowsazure.com/
[Recommandations relatives à Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Procédures Notification Hubs pour iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/

<!--HONumber=49-->