## Création du projet WebAPI

Suivez les étapes ci-dessous pour créer un serveur principal WebAPI ASP.NET afin d'authentifier les clients et générer des notifications, ou modifiez un serveur principal existant à partir des projets précédents ou le didacticiel [Envoi de notifications Push aux utilisateurs authentifiés](http://azure.microsoft.com/ documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/).

> [AZURE.NOTE] **Important** : Avant d'entamer ce didacticiel, vérifiez que la version la plus récente du Gestionnaire de package NuGet est installé. Pour ce faire, lancez Visual Studio. Dans le menu **Outils**, cliquez sur **Extensions et mises à jour**. Recherchez **Gestionnaire de package NuGet pour Visual Studio 2013** et vérifiez que vous disposez de la version 2.8.50313.46 (ou ultérieure). Si ce n'est pas le cas, désinstallez puis réinstallez le Gestionnaire de package NuGet.
> 
> ![][4]

> [AZURE.NOTE] Assurez-vous que le [Kit de développement logiciel (SDK) Azure](http://azure.microsoft.com/ downloads/) Visual Studio est installé, pour le déploiement du site web.

1. Démarrez Visual Studio ou Visual Studio Express.
2. Dans Visual Studio, cliquez sur **fichier**, puis cliquez sur **nouveau**, puis **projet**, développez **modèles**, **Visual C#**, puis cliquez sur **Web** et **Application Web ASP.NET**, tapez le nom **AppBackend**, puis cliquez sur **OK**. 
	
	![][1]

3. Dans la boîte de dialogue **Nouveau projet ASP.NET**, cliquez sur **API web**, puis sur **OK**.

	![][2]

4. Dans la boîte de dialogue **Configurer un site Azure** choisissez l'abonnement, la région et la base de données à utiliser pour ce projet. Cliquez ensuite sue **OK** pour créer le projet.

	![][5]

5. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet **AppBackend**, puis cliquez sur **Gérer les packages NuGet**.

6. Sur le côté gauche, cliquez sur **En ligne**, puis recherchez **servicebus** dans la zone **Rechercher**.

7. Dans la liste de résultats, cliquez sur **Windows Azure Service Bus**, puis sur **Installer**. Procédez à l'installation, puis fermez la fenêtre du Gestionnaire de package NuGet.

	![][14]

8. Nous allons maintenant créer une classe **Notifications.cs**. Accédez à l'Explorateur de solutions, cliquez avec le bouton droit de la souris sur le dossier **Modèles**, puis cliquez sur **Ajouter** et sur **Classe**. Après avoir nommé la nouvelle classe **Notifications.cs**, cliquez sur **Ajouter** pour générer la classe. Ce module représente les différentes notifications sécurisées qui seront envoyées. Dans les implémentations complètes, les notifications sont stockées dans une base de données. Par souci de simplicité, ce didacticiel les stocke en mémoire.

	![][6]

9. Dans Notifications.cs, ajoutez l'instruction `using` suivante en haut du fichier :

        using Microsoft.ServiceBus.Notifications;

10. Remplacez ensuite la définition de classe `Notifications` par ce qui suit et veillez à remplacer les deux espaces réservés par la chaîne de connexion (avec accès complet) pour votre concentrateur de notification et le nom du concentrateur (disponible sur le [portail de gestion Azure](http://manage.windowsazure.com)) :

		public class Notifications
        {
            public static Notifications Instance = new Notifications();
        
            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", "{hub name}");
            }
        }

11. Nous allons ensuite créer une classe **AuthenticationTestHandler.cs**. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet **AppBackend**, puis cliquez sur **Ajouter** et sur **Classe**. Nommez la nouvelle classe **AuthenticationTestHandler.cs**, puis cliquez sur **Ajouter** pour générer la classe. Cette classe est utilisée pour authentifier les utilisateurs à l'aide de *Basic Authentication*. Notez que votre application peut utiliser n'importe quel schéma d'authentification.

12. Dans AuthenticationTestHandler.cs, ajoutez les instructions `using` suivantes :

        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Threading;
        using System.Text;
        using System.Security.Principal;
        using System.Net;

13. Dans AuthenticationTestHandler.cs, remplacez la définition de classe `AuthenticationTestHandler` par la suivante :

		public class AuthenticationTestHandler : DelegatingHandler
	    {
	        protected override Task<HttpResponseMessage> SendAsync(
	        HttpRequestMessage request, CancellationToken cancellationToken)
	        {
	            var authorizationHeader = request.Headers.GetValues("Authorization").First();
	
	            if (authorizationHeader != null && authorizationHeader
	                .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
	            {
	                string authorizationUserAndPwdBase64 =
	                    authorizationHeader.Substring("Basic ".Length);
	                string authorizationUserAndPwd = Encoding.Default
	                    .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
	                string user = authorizationUserAndPwd.Split(':')[0];
	                string password = authorizationUserAndPwd.Split(':')[1];
	
	                if (verifyUserAndPwd(user, password))
	                {
	                    // Attach the new principal object to the current HttpContext object
	                    HttpContext.Current.User =
	                        new GenericPrincipal(new GenericIdentity(user), new string[0]);
	                    System.Threading.Thread.CurrentPrincipal =
	                        System.Web.HttpContext.Current.User;
	                }
	                else return Unauthorised();
	            }
	            else return Unauthorised();
	
	            return base.SendAsync(request, cancellationToken);
	        }
	
	        private bool verifyUserAndPwd(string user, string password)
	        {
	            // This is not a real authentication scheme.
	            return user == password;
	        }
	
	        private Task<HttpResponseMessage> Unauthorised()
	        {
	            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
	            var tsc = new TaskCompletionSource<HttpResponseMessage>();
	            tsc.SetResult(response);
	            return tsc.Task;
	        }
	    }

	> [AZURE.NOTE] **Remarque relative à la sécurité** : La classe `AuthenticationTestHandler` n'offre pas de véritable authentification. Elle sert uniquement à simuler l'authentification de base et n'est pas sécurisée. Vous devez mettre en œuvre un mécanisme d'authentification sécurisé dans vos applications de production et vos services.				

14. Ajoutez le code suivant à la fin de la méthode `Register` dans la classe **App_Start/WebApiConfig.cs** :

		config.MessageHandlers.Add(new AuthenticationTestHandler());

15. Nous allons ensuite créer un nouveau contrôleur **RegisterController**. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le dossier **Contrôleurs**, puis cliquez sur **Ajouter** et sur **Contrôleur**. Cliquez sur l'élément **Web API 2 Controller -- Empty**, et cliquez sur **Ajouter**. Nommez la nouvelle classe **RegisterController**, puis cliquez de nouveau sur **Ajouter** pour générer le contrôleur.

	![][7]

	![][8]

16. Dans RegiterController.cs, ajoutez les instructions `using` suivantes :

        using Microsoft.ServiceBus.Notifications;
        using AppBackend.Models;
        using System.Threading.Tasks;
        using Microsoft.ServiceBus.Messaging;
        using System.Web;

17. Ajoutez le code suivant dans la définition de classe `RegisterController`. Notez que nous ajoutons à ce code la balise de l'utilisateur qui a été authentifié par le gestionnaire. Vous pouvez également ajouter des contrôles facultatifs afin de vérifier que l'utilisateur dispose des droits d'inscription nécessaires pour les balises requises.

		private NotificationHubClient hub;

        public RegisterController()
        {
            hub = Notifications.Instance.Hub;
        }

        public class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        // POST api/register
        // This creates a registration id
        public async Task<string> Post(string handle = null)
        {
            // make sure there are no existing registrations for this push handle (used for iOS and Android)
            string newRegistrationId = null;
            
            if (handle != null)
            {
                var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

                foreach (RegistrationDescription registration in registrations)
                {
                    if (newRegistrationId == null)
                    {
                        newRegistrationId = registration.RegistrationId;
                    }
                    else
                    {
                        await hub.DeleteRegistrationAsync(registration);
                    }
                }
            }

            if (newRegistrationId == null) newRegistrationId = await hub.CreateRegistrationIdAsync();

            return newRegistrationId;
        }

        // PUT api/register/5
        // This creates or updates a registration (with provided channelURI) at the specified id
        public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
        {
            RegistrationDescription registration = null;
            switch (deviceUpdate.Platform)
            {
                case "mpns":
                    registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "wns":
                    registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "apns":
                    registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "gcm":
                    registration = new GcmRegistrationDescription(deviceUpdate.Handle);
                    break;
                default:
                    throw new HttpResponseException(HttpStatusCode.BadRequest);
            }

            registration.RegistrationId = id;
            var username = HttpContext.Current.User.Identity.Name;

            // add check if user is allowed to add these tags
            registration.Tags = new HashSet<string>(deviceUpdate.Tags);
            registration.Tags.Add("username:" + username);

            try
            {
                await hub.CreateOrUpdateRegistrationAsync(registration);
            }
            catch (MessagingException e)
            {
                ReturnGoneIfHubResponseIsGone(e);
            }

            return Request.CreateResponse(HttpStatusCode.OK);
        }

        // DELETE api/register/5
        public async Task<HttpResponseMessage> Delete(string id)
        {
            await hub.DeleteRegistrationAsync(id);
            return Request.CreateResponse(HttpStatusCode.OK);
        }

        private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
        {
            var webex = e.InnerException as WebException;
            if (webex.Status == WebExceptionStatus.ProtocolError)
            {
                var response = (HttpWebResponse)webex.Response;
                if (response.StatusCode == HttpStatusCode.Gone)
                    throw new HttpRequestException(HttpStatusCode.Gone.ToString());
            }
        }

18. Créez un nouveau contrôleur **NotificationsController**, de la même façon que nous avons créé **RegisterController**. Ce composant expose une méthode permettant à l'appareil de récupérer la notification en toute sécurité et offre un moyen pour un utilisateur de déclencher une transmission de type push sécurisée vers les appareils. Notez que la notification envoyée ici au concentrateur de notification est brute puisqu'elle comporte uniquement son ID (sans message).

19. Dans NotificationsController.cs, ajoutez les instructions `using` suivantes :

        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

20. Ajoutez le code suivant dans la définition de classe  **NotificationsController** et veillez à placer en commentaires les extraits de code pour les plateformes que vous n'utilisez pas.

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:"+user;


            // windows
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello, " + user + "</text></binding></visual></toast>";
            await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);


            // apns
            var alert = "{\"aps\":{\"alert\":\"Hello\"}}";
            await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);


            // gcm
            var notif = "{ \"data\" : {\"msg\":\"Hello\"}}";
            await Notifications.Instance.Hub.SendGcmNativeNotificationAsync(notif, userTag);


            return Request.CreateResponse(HttpStatusCode.OK);
        }

21. Appuyez sur la touche **F5** pour exécuter l'application et vérifier le travail que vous avez accompli jusqu'à présent. L'application doit lancer un navigateur web et afficher la page d'accueil d'ASP.NET. 

22. Nous allons maintenant déployer cette application sur un site web Azure afin de la rendre accessible à tous les appareils. Cliquez avec le bouton droit sur le projet **AppBackend** et sélectionnez **Publier**.

23. Sélectionnez Site web Azure comme cible de publication.

    ![][B15]

24. Connectez-vous avec votre compte Azure et sélectionnez un site web (nouveau ou existant).

    ![][B16]

25. Notez la propriété **URL de destination** sous l'onglet **Connexion**. Plus loin dans ce didacticiel, nous utiliserons cette URL comme *backend endpoint*. Cliquez sur **Publier**.

    ![][B18]


[1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG

<!--HONumber=45--> 
