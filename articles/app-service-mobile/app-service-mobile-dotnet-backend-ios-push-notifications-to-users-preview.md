<properties 
	pageTitle="Envoi de notifications interplateformes à un utilisateur spécifique dans iOS" 
	description="Découvrez comment envoyer des notifications push à tous les appareils d'un utilisateur spécifique."
	services="app-service\mobile" 
	documentationCenter="ios" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-dotnet" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="09/25/2015"
	ms.author="yuaxu"/>

# Envoi de notifications interplateformes à un utilisateur spécifique

[AZURE.INCLUDE [app-service-mobile-selector-push-users-preview](../../includes/app-service-mobile-selector-push-users-preview.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Cette rubrique montre comment envoyer des notifications à tous les appareils inscrits d'un utilisateur spécifique à partir de votre backend mobile. Elle introduit le concept de [modèles], qui donne aux applications clientes la liberté de spécifier des formats de charge utile et divers emplacements réservés lors de l’inscription. L'envoi atteint alors chaque plateforme dotée de ces espaces réservés, générant ainsi des notifications interplateformes.

> [AZURE.NOTE]Pour que les notifications push fonctionnent avec des clients interplateformes, vous devez suivre ce didacticiel pour chaque plateforme que vous souhaitez utiliser. Il vous suffit d’effectuer une fois la [mise à jour du serveur principal mobile](#backend) pour les clients qui partagent le serveur principal.
 
##Configuration requise 

Avant de commencer ce didacticiel, vous devez avoir déjà effectué les didacticiels App Service pour chaque plateforme cliente que vous souhaitez utiliser :

+ [Prise en main de l'authentification]<br/>Ajoute une exigence de connexion à l'exemple d'application TodoList.

+ [Prise en main des notifications Push]<br/>Configure l’exemple d’application TodoList pour les notifications Push.

##<a name="client"></a>Mise à jour de votre client afin de vous inscrire à des modèles pour gérer des opérations push interplateformes

1. Déplacez les extraits de code d’inscription au service de notification push Apple figurant dans **QSAppDelegate.m**, dans **application:didFinishLaunchingWithOptions**, vers l’appel à **loginWithProvider** dans **QSTodoListViewController.m**, afin que l’inscription intervienne une fois l’authentification terminée :

        [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
            [self refresh];
            
            // register iOS8 or previous devices for notifications
            if ([[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)]) {
                [[UIApplication sharedApplication] registerForRemoteNotifications];
            }
            else {
                // Register for remote notifications
                [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
                 UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            }
        }];

2. Remplacez votre appel **registerDeviceToken** dans **application:didRegisterForRemoteNotificationsWithDeviceToken** par ce qui suit pour utiliser des modèles :

        NSDictionary *templates = @{
                               @"testNotificationTemplate": @{ @"body" : @{ @"aps" : @{ @"alert": @"$(message)" } } }
                               };
    
        // register with templates
        [client.push registerDeviceToken:deviceToken template:templates completion:^(NSError *error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

##<a name="backend"></a>Mise à jour du serveur principal de votre service pour envoyer des notifications à un utilisateur spécifique

1. Dans Visual Studio, mettez à jour la définition de la méthode `PostTodoItem` avec le code suivant :  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            // get notification hubs credentials associated with this mobile app
            string notificationHubName = this.Services.Settings.NotificationHubName;
            string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

            // connect to notification hub
            NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName)

            // get the current user id and create tag to identify user
            ServiceUser authenticatedUser = this.User as ServiceUser;
            string userTag = "_UserId:" + authenticatedUser.Id;

            // build dictionary for template
            var notification = new Dictionary<string, string>{{"message", item.Text}};

            try
            {
            	await Hub.Push.SendTemplateNotificationAsync(notification, userTag);
            }
            catch (System.Exception ex)
            {
                throw;
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

##<a name="test"></a>Test de l’application

Publiez à nouveau votre projet de backend mobile et exécutez les applications clientes que vous avez configurées. Lors de l'insertion d'un élément, le backend envoie des notifications à toutes les applications clientes auxquelles l'utilisateur est connecté.

<!-- URLs. -->
[Prise en main de l'authentification]: app-service-mobile-dotnet-backend-ios-get-started-push-preview.md
[Prise en main des notifications Push]: app-service-mobile-dotnet-backend-ios-get-started-push-preview.md
[modèles]: https://msdn.microsoft.com/fr-FR/library/dn530748.aspx
 

<!---HONumber=Oct15_HO3-->