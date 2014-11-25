## Création du projet WebAPI

La première étape consiste à créer un projet WebAPI ASP.NET. Il s'agit du serveur principal utilisé pour authentifier les clients et générer les notifications.

> [AZURE.NOTE] **Important** : avant d'entamer ce didacticiel, vérifiez que la version la plus récente du Gestionnaire de package NuGet est installé. Pour ce faire, lancez Visual Studio. Dans le menu **Outils**, cliquez sur **Extensions et mises à jour**. Recherchez **Gestionnaire de package NuGet pour Visual Studio 2013** et vérifiez que vous disposez de la version 2.8.50313.46 (ou ultérieure). Si ce n'est pas le cas, désinstallez puis réinstallez le Gestionnaire de package NuGet.
>
> ![][4]

1.  Démarrez Visual Studio avec des privilèges élevés (administrateur).
2.  Dans Visual Studio ou Visual Studio Express, cliquez sur **Fichier**, **Nouveau** et **Projet**, développez **Modèles**, **Visual C\#**, cliquez sur **Web** et **Application web ASP.NET**, entrez le nom **AppBackend**, puis cliquez sur **OK**.

    ![][1]

3.  Dans la boîte de dialogue **New ASP.NET Project**, cliquez sur **API web**, puis sur **OK**.

    ![][2]

4.  Dans la boîte de dialogue **Configure Azure Site**, choisissez l'abonnement, la région et la base de données à utiliser pour ce projet. Cliquez sur **OK** pour créer le projet.

    ![][3]

5.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet **AppBackend**, puis cliquez sur **Gérer les packages NuGet**.

6.  Dans la partie gauche, cliquez sur **En ligne**.

7.  Dans la zone **Rechercher**, entrez **servicebus**.

8.  Dans la liste de résultats, cliquez sur **Windows Azure Service Bus**, puis sur **Installer**. Procédez à l'installation, puis fermez la fenêtre du Gestionnaire de package NuGet.

    ![][4]

9.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le dossier **Modèles**, puis cliquez sur **Ajouter** et sur **Classe**. Nommez la nouvelle classe **Notifications.cs**. Cliquez sur **Ajouter** pour générer la classe. Ce module représente les différentes notifications sécurisées qui seront envoyées. Dans les implémentations complètes, les notifications sont stockées dans une base de données. Par souci de simplification, nous allons ici les stocker en mémoire.

    ![][5]

10. Ajoutez le code au fichier Notifications.cs en remplaçant la définition de classe `Notifications` par ce qui suit :

        public class Notifications
        {
            public static Notifications Instance = new Notifications();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", "{hub name}");
            }
        }

11. Ajoutez l'instruction `using` suivante au début du fichier :

        using Microsoft.ServiceBus.Notifications;

12. Dans la méthode `Notifications()`, remplacez les deux espaces réservés de la ligne de code suivante par la chaîne de connexion (avec accès complet) de votre concentrateur de notification et par le nom de celui-ci. Ces valeurs sont disponibles sur le [portail de gestion Azure][portail de gestion Azure] :

        Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", "{hub name}");

13. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet **AppBackend**, puis cliquez sur **Ajouter** et sur **Classe**. Nommez la nouvelle classe **AuthenticationTestHandler.cs**. Cliquez sur **Ajouter** pour générer la classe. Cette classe permet d'authentifier les utilisateurs via l'*Authentification de base*. Notez que votre application peut utiliser n'importe quel schéma d'authentification.

14. Ajoutez le code au fichier AuthenticationTestHandler.cs en remplaçant la définition de classe `AuthenticationTestHandler` par ce qui suit :

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

    > [AZURE.NOTE] **Remarque relative à la sécurité** : La classe `AuthenticationTestHandler` ne fournit pas de véritable authentification. Elle sert uniquement à simuler l'authentification de base et à renvoyer un principe. Le nom d'utilisateur est requis pour créer des inscriptions au Notification Hub. L'implémentation précédente n'est pas sécurisée. Vous devez mettre en œuvre un mécanisme d'authentification sécurisé dans vos applications de production et vos services.

15. Ajoutez l'instruction `using` suivante au début du fichier AuthenticationTestHandler.cs :

        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Threading;
        using System.Text;
        using System.Security.Principal;
        using System.Net;               

16. Ajoutez le code suivant à la fin de la méthode `Register` dans la classe **App\_Start/WebApiConfig.cs** :

        config.MessageHandlers.Add(new AuthenticationTestHandler());

17. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le dossier **Contrôleurs**, puis cliquez sur **Ajouter** et sur **Contrôleur**. Cliquez sur l'élément **Web API 2 Controller -- Empty**, puis sur **Ajouter**.

    ![][6]

18. Nommez la nouvelle classe **RegisterController**, puis cliquez de nouveau sur **Ajouter** pour générer le contrôleur.

    ![][7]

19. Ajoutez le code suivant dans la définition de classe `RegisterController` :

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

20. Ajoutez l'instruction `using` suivante au début du fichier RegisterController.cs :

        using Microsoft.ServiceBus.Notifications;
        using SecurePush.Models;
        using System.Threading.Tasks;
        using Microsoft.ServiceBus.Messaging;
        using System.Web;

21. Notez que nous ajoutons à ce code la balise de l'utilisateur qui a été authentifié par le gestionnaire. Vous pouvez également ajouter des contrôles facultatifs afin de vérifier que l'utilisateur dispose des droits d'inscription nécessaires pour les balises requises.

22. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le dossier **Contrôleurs**, puis cliquez sur **Ajouter** et sur **Contrôleur**. Cliquez sur l'élément **Web API 2 Controller -- Empty**, puis sur **Ajouter**. Nommez la nouvelle classe **NotificationsController**, puis cliquez de nouveau sur **Ajouter** pour générer le contrôleur. Ce composant permet à l'appareil de récupérer la notification en toute sécurité. Dans l'exemple de ce didacticiel, il permet également à l'utilisateur de déclencher une notification Push sécurisée sur ses appareils. Notez que la notification envoyée ici au concentrateur de notification est brute puisqu'elle comporte uniquement son ID (sans message).

23. Ajoutez le code suivant dans la définition de classe **NotificationsController** :

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:"+user;
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello, " + user + "</text></binding></visual></toast>";
            await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

            return Request.CreateResponse(HttpStatusCode.OK);

        }

24. Ajoutez l'instruction `using` suivante au début du fichier NotificationsController.cs :

        using SecurePush.Models;
        using System.Threading.Tasks;
        using System.Web;

25. Appuyez sur la touche **F5** pour exécuter l'application et vérifier le travail que vous avez accompli jusqu'à présent. L'application doit lancer un navigateur web et afficher la page d'accueil d'ASP.NET.

26. Nous allons maintenant déployer cette application sur un site web Azure afin de la rendre accessible à tous les appareils. Cliquez avec le bouton droit sur le projet **AppBackend** et sélectionnez **Publier**.

27. Sélectionnez Site web Azure comme cible de publication.

    ![][8]

28. Connectez-vous avec votre compte Azure et sélectionnez un site web (nouveau ou existant).

    ![][9]

29. Notez la propriété de l'**URL de destination** sous l'onglet **Connexion**. Plus loin dans ce didacticiel, nous utiliserons cette URL comme *point de terminaison principal*. Cliquez sur **Publier**.

    ![][10]

  
  [1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
  [2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
  [3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
  [4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
  [5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
  [portail de gestion Azure]: http://manage.windowsazure.com
  [6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
  [7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
  [8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
  [9]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
  [10]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG
