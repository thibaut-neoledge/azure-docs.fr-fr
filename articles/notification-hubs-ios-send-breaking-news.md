<properties linkid="develop-notificationhubs-tutorials-send-breaking-news-ios" urlDisplayName="Breaking News" pageTitle="Notification Hubs Breaking News Tutorial - iOS" metaKeywords="" description="Learn how to use Azure Service Bus Notification Hubs to send breaking news notifications to iOS devices." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send breaking news" authors="ricksal" solutions="" manager="" editor="" />

Utilisation de Notification Hubs pour envoyer les dernières nouvelles
=====================================================================

[Windows Store C\#](/en-us/manage/services/notification-hubs/breaking-news-dotnet "Windows Store C#")[Windows Phone](/en-us/manage/services/notification-hubs/breaking-news-wp8 "Windows Phone")[iOS](/en-us/manage/services/notification-hubs/breaking-news-ios "iOS")

Cette rubrique montre comment utiliser Azure Notification Hubs pour diffuser des notifications relatives aux dernières nouvelles vers une application iOS. Lorsque vous aurez terminé, vous pourrez vous inscrire aux catégories de dernières nouvelles qui vous intéressent et recevoir uniquement des notifications Push pour ces catégories. Ce scénario est un modèle courant pour de nombreuses applications pour lesquelles des notifications doivent être envoyées à des groupes d'utilisateurs qui ont signalé antérieurement un intérêt, par exemple, lecteur RSS, applications pour fans de musique, etc.

Les scénarios de diffusion sont activés en incluant une ou plusieurs *balises* lors de la création d'une inscription dans le concentrateur de notification. Lorsque des notifications sont envoyées à une balise, tous les appareils pour lesquels cette balise est inscrite reçoivent la notification. Les balises étant de simples chaînes, il n'est pas nécessaire de les approvisionner à l'avance. Pour plus d'informations sur les balises, consultez la page [Recommandations relatives à Notification Hubs](http://msdn.microsoft.com/en-us/library/jj927170.aspx).

Ce didacticiel vous familiarise avec les étapes de base pour activer ce scénario :

1.  [Ajout d'une sélection de catégories à l'application](#adding-categories)
2.  [Inscription à des notifications](#register)
3.  [Envoi de notifications à partir de votre serveur principal](#send)
4.  [Exécution de l'application et génération de notifications](#test-app)

Cette rubrique s'appuie sur l'application que vous avez créée dans [Prise en main de Notification Hubs](/en-us/manage/services/notification-hubs/get-started-notification-hubs-ios/). Avant de commencer ce didacticiel, vous devez suivre celui intitulé [Prise en main de Notification Hubs](/en-us/manage/services/notification-hubs/get-started-notification-hubs-ios/).

Ajout d'une sélection de catégories à l'application
---------------------------------------------------

La première étape consiste à ajouter à votre storyboard existant les éléments d'interface utilisateur qui permettent à l'utilisateur de sélectionner les catégories à inscrire. Les catégories sélectionnées par un utilisateur sont stockées sur l'appareil. Lorsque l'application démarre, une inscription d'appareil est créée dans votre concentrateur de notification avec les catégories sélectionnées sous forme de balises.

1.  Dans MainStoryboard\_iPhone.storyboard, ajoutez les composants suivants de la bibliothèque d'objets :
	+ une étiquette intitulée "Breaking News" ;
	+ des étiquettes portant les intitulés de catégories "World", "Politics", "Business", "Technology", "Science", "Sports" ;
	+ six commutateurs, un par catégorie ;
	+ un bouton intitulé "Subscribe".

	Votre storyboard doit ressembler à ce qui suit :

	![](./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png)

2.  Dans l'éditeur de l'Assistant, créez des outlets pour tous les commutateurs et appelez-les "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch".

    ![](./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios3.png)

3.  Créez une action pour le bouton intitulé "Subscribe". Le fichier BreakingNewsViewController.h doit désormais contenir le code suivant :

         @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
         @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
         @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
         @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
         @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
         @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

         - (IBAction)subscribe:(id)sender;

4.  Créez une classe nommée `Notifications`. Copiez le code suivant dans la section de l'interface du fichier Notifications.h :

         - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*) categories completion: (void (^)(NSError* error))completion;
         - (void)subscribeWithCategories:(NSSet*) categories completion:(void (^)(NSError *))completion;

5.  Ajoutez la directive import suivante au fichier Notifications.m :

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6.  Copiez le code suivant dans la section d'implémentation du fichier Notifications.m :

         - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
             NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
             [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
                
             [self subscribeWithCategories:categories completion:completion];
         }

         - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion{
             SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string with listen access>" notificationHubPath:@"<hub name>"];
                
             [hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];
         }

    Cette classe utilise le stockage local pour stocker les catégories de nouvelles que cet appareil doit recevoir. Elle contient également les méthodes pour inscrire ces catégories.

7.  Dans le code ci-dessus, remplacez les espaces réservés `<hub name>` et `<connection string with listen access>` par le nom du concentrateur de notification et la chaîne de connexion pour *DefaultListenSharedAccessSignature* obtenue précédemment.

    **Remarque**

    Les informations d'identification distribuées avec une application cliente n'étant généralement pas sécurisées, vous ne devez distribuer que la clé d'accès d'écoute avec votre application cliente. L'accès d'écoute permet à votre application de s'inscrire à des notifications, mais les inscriptions existantes ne peuvent pas être modifiées et les notifications ne peuvent pas être envoyées. La clé d'accès complet est utilisée dans un service de serveur principal sécurisé pour l'envoi de notifications et la modification d'inscriptions existantes.

8.  Dans le fichier BreakingNewsAppDelegate.h, ajoutez la propriété suivante :

         @property (nonatomic) Notifications* notifications;

    Cela permet de créer une instance singleton de la classe Notification dans AppDelegate.

9.  Dans la méthode **didFinishLaunchingWithOptions** du fichier BreakingNewsAppDelegate.m, ajoutez le code suivant avant **registerForRemoteNotificationTypes** :

         self.notifications = [[Notifications alloc] init];

    Ce dernier initialise le singleton Notification.

10. Dans la méthode **didRegisterForRemoteNotificationsWithDeviceToken** du fichier BreakingNewsAppDelegate.m, retirez l'appel à **registerNativeWithDeviceToken** et ajoutez le code suivant :

        self.notifications.deviceToken = deviceToken;

    À ce stade, il est à noter qu'il ne doit pas y avoir d'autre code dans la méthode **didRegisterForRemoteNotificationsWithDeviceToken**.

11. Ajoutez la méthode suivante dans le fichier BreakingNewsAppDelegate.m :

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
            (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

    Cette méthode gère les notifications reçues lorsque l'application est en cours d'exécution en affichant tout simplement une **UIAlert**.

12. Dans le fichier BreakingNewsViewController.m, copiez le code suivant dans la méthode **subscribe** générée par XCode :

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

Inscription à des notifications
-------------------------------

Les étapes suivantes permettent l'inscription auprès du concentrateur de notification au démarrage en utilisant les catégories qui ont été stockées dans le stockage local.

**Remarque**

Étant donné que le jeton d'appareil attribué par le service de notification Push Apple (APN, Apple Push Notification) peut être modifié à tout moment, vous devez vous inscrire aux notifications à intervalles réguliers pour éviter les défaillances de notification. Cet exemple s'inscrit aux notifications chaque fois que l'application démarre. Pour les applications exécutées fréquemment, plus d'une fois par jour, vous pouvez probablement ignorer l'inscription afin de préserver la bande passante si moins d'un jour s'est écoulé depuis l'inscription précédente.

1.  Ajoutez la méthode suivante dans la section de l'interface du fichier Notifications.h :

         - (NSSet*)retrieveCategories;

    Ce code permet d'extraire les catégories de la classe Notifications.

2.  Ajoutez l'implémentation correspondante dans le fichier Notifications.m :

         - (NSSet*)retrieveCategories {
             NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
                
             NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];
                
             if (!categories) return [[NSSet alloc] init];
             return [[NSSet alloc] initWithArray:categories];
         }

3.  Ajoutez le code suivant à la méthode **didRegisterForRemoteNotificationsWithDeviceToken** :

         Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

         NSSet* categories = [notifications retrieveCategories];
         [notifications subscribeWithCategories:categories completion:^(NSError* error) {
             if (error != nil) {
                 NSLog(@"Error registering for notifications: %@", error);
             }
         }];

    Cette opération garantit que chaque fois que l'application démarre, elle récupère les catégories du stockage local et demande une inscription pour ces catégories.

4.  Dans le fichier BreakingNewsViewController.h, ajoutez le code suivant à la méthode **viewDidLoad** :

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

Envoi de notificationsEnvoi de notifications à partir de votre serveur principal
--------------------------------------------------------------------------------

[WACOM.INCLUDE [notification-hubs-back-end](../includes/notification-hubs-back-end.md)]

Exécution de l'application et génération de notifications
---------------------------------------------------------

1.  Appuyez sur le bouton Exécuter pour générer le projet et démarrer l'application.

    ![](./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png)

    Notez que l'interface utilisateur de l'application fournit un ensemble de bascules qui vous permet de choisir les catégories auxquelles vous abonner.

2.  Activez une ou plusieurs bascules de catégories, puis cliquez sur **S'abonner**.

    Lorsque vous sélectionnez **Subscribe**, l'application convertit les catégories sélectionnées en balises et demande une nouvelle inscription de l'appareil aux balises sélectionnées depuis le concentrateur de notification.

3.  Envoyez une nouvelle notification depuis le serveur principal de l'une des manières suivantes :

    -   **Application console :** démarrez l'application console.

    -   **Mobile Services :** cliquez sur l'onglet **Scheduler**, sur la tâche, puis sur **Exécuter une fois**.

4.  Les notifications pour les catégories sélectionnées apparaissent comme notifications toast.

Étapes suivantes
----------------

Dans ce didacticiel, nous avons appris à diffuser les dernières nouvelles par catégorie. Envisagez de suivre un des didacticiels suivants qui soulignent d'autres scénarios avancés Notification Hubs :

-   **[Utilisation de Notification Hubs pour diffuser les dernières nouvelles localisées](/en-us/manage/services/notification-hubs/breaking-news-localized-dotnet/)**

    Apprenez à développer l'application relative aux dernières nouvelles pour permettre l'envoi de notifications localisées.

-   **[Notification des utilisateurs avec Notification Hubs](/en-us/manage/services/notification-hubs/notify-users/)**

    Apprenez comment transmettre des notifications à des utilisateurs authentifiés spécifiques. Il s'agit d'une solution appropriée pour l'envoi de notifications uniquement vers des utilisateurs spécifiques.


