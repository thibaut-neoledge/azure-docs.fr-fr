## Création du projet WebAPI

Un nouveau serveur principal ASP.NET WebAPI est créé dans les sections qui suivent, et a trois objectifs principaux :

1. **Authentification des clients** : un gestionnaire de messages sera ajouté ultérieurement pour authentifier les demandes des clients et associer l’utilisateur à la demande.
2. **Inscriptions aux notifications des clients** : ensuite, vous ajouterez un contrôleur pour gérer les nouvelles inscriptions afin qu’un appareil client puisse recevoir des notifications. Le nom d’utilisateur authentifié est automatiquement ajouté à l’inscription en tant que [balise](https://msdn.microsoft.com/library/azure/dn530749.aspx).
3. **Envoi de notifications aux clients** : plus tard, vous ajouterez également un contrôleur permettant à un utilisateur de déclencher une notification Push sécurisée pour les appareils et les clients associés à la balise.

Les étapes suivantes montrent comment créer le serveur principal ASP.NET WebAPI :


> [AZURE.NOTE] **Important** : avant d’entamer ce didacticiel, vérifiez que la version la plus récente du Gestionnaire de package NuGet est installée. Pour ce faire, lancez Visual Studio. Dans le menu **Outils**, cliquez sur **Extensions et mises à jour**. Recherchez **Gestionnaire de package NuGet pour Visual Studio 2013** et vérifiez que vous disposez de la version 2.8.50313.46 (ou ultérieure). Si ce n’est pas le cas, désinstallez puis réinstallez le Gestionnaire de package NuGet.
> 
> ![][B4]

> [AZURE.NOTE] Assurez-vous que le [Kit de développement logiciel (SDK) Azure](https://azure.microsoft.com/downloads/) Visual Studio est installé, pour le déploiement du site web.

1. Démarrez Visual Studio ou Visual Studio Express. Cliquez sur **Explorateur de serveurs** et connectez-vous à votre compte Azure. Visual Studio vous oblige à vous connectez pour pouvoir créer des ressources de site web sur votre compte.
2. Dans Visual Studio, cliquez sur **Fichier**, sur **Nouveau**, puis sur **Projet**, développez **Modèles**, **Visual C#**, puis cliquez sur **Web** et **Application Web ASP.NET**, tapez le nom **AppBackend**, puis cliquez sur **OK**.
	
	![][B1]

3. Dans la boîte de dialogue **Nouveau projet ASP.NET**, cliquez sur **API web**, puis sur **OK**.

	![][B2]

4. Dans la boîte de dialogue de **configuration de l’application web Microsoft Azure**, choisissez un abonnement et un **plan App Service** que vous avez déjà créé. Vous pouvez également choisir de **créer un nouveau plan App Service** dans cette même boîte de dialogue. Vous n'avez pas besoin de base de données pour ce didacticiel. Une fois que vous avez sélectionné votre plan App Service, cliquez sur **OK** pour créer le projet.

	![][B5]



## Authentification des clients sur le serveur principal WebAPI

Dans cette section, vous allez créer une classe de gestionnaire de messages nommée **AuthenticationTestHandler** pour le nouveau serveur principal. Cette classe est dérivée de [DelegatingHandler](https://msdn.microsoft.com/library/system.net.http.delegatinghandler.aspx) et ajoutée comme gestionnaire de messages afin de pouvoir traiter toutes les demandes entrantes dans le serveur principal.



1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **AppBackend**, puis cliquez sur **Ajouter** et sur **Classe**. Nommez la nouvelle classe **AuthenticationTestHandler.cs**, puis cliquez sur **Ajouter** pour générer la classe. Par souci de simplification, cette classe permettra d’authentifier les utilisateurs via l’*Authentification de base*. Notez que votre application peut utiliser n’importe quel schéma d’authentification.

2. Dans AuthenticationTestHandler.cs, ajoutez les instructions `using` suivantes :

        using System.Net.Http;
        using System.Threading;
        using System.Security.Principal;
        using System.Net;
        using System.Web;

3. Dans AuthenticationTestHandler.cs, remplacez la définition de classe `AuthenticationTestHandler` par le code suivant :

	Ce gestionnaire autorise la demande lorsque les trois conditions suivantes sont vraies :
	* La demande comprenait un en-tête d’*autorisation*.
	* La demande utilise l’authentification *de base*.
	* Les chaînes du nom d’utilisateur et du mot de passe sont identiques.

	Sinon, la demande est rejetée. Cette méthode ne répond pas à une véritable approche d’authentification et d’autorisation. Il s’agit uniquement d’un exemple très simple pour ce didacticiel.

	Si le message de la demande est authentifié et autorisé par le `AuthenticationTestHandler`, l’utilisateur de l’authentification de base est attaché à la demande actuelle sur le [HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.current.aspx). Les informations utilisateur dans le HttpContext sont utilisées ultérieurement par un autre contrôleur (RegisterController) pour ajouter une [balise](https://msdn.microsoft.com/library/azure/dn530749.aspx) à la demande d’inscription aux notifications.

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
	                else return Unauthorized();
	            }
	            else return Unauthorized();
	
	            return base.SendAsync(request, cancellationToken);
	        }
	
	        private bool verifyUserAndPwd(string user, string password)
	        {
	            // This is not a real authentication scheme.
	            return user == password;
	        }
	
	        private Task<HttpResponseMessage> Unauthorized()
	        {
	            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
	            var tsc = new TaskCompletionSource<HttpResponseMessage>();
	            tsc.SetResult(response);
	            return tsc.Task;
	        }
	    }

	> [AZURE.NOTE] **Note de sécurité** : la classe `AuthenticationTestHandler` n’offre pas de véritable authentification. Elle sert uniquement à simuler l’authentification de base et n’est pas sécurisée. Vous devez mettre en œuvre un mécanisme d’authentification sécurisé dans vos applications de production et vos services.

4. Ajoutez le code suivant à la fin de la méthode `Register` dans la classe **App\_Start/WebApiConfig.cs** pour inscrire le gestionnaire de messages :

		config.MessageHandlers.Add(new AuthenticationTestHandler());

5. Enregistrez vos modifications.

## Inscription aux notifications à l’aide du serveur principal WebAPI

Dans cette section, nous allons ajouter un nouveau contrôleur au serveur principal WebAPI pour gérer les demandes d’inscription d’un utilisateur et d’un appareil aux notifications à l’aide de la bibliothèque cliente pour les hubs de notification. Le contrôleur ajoute une balise d’utilisateur pour l’utilisateur qui a été authentifié et attaché au HttpContext par le `AuthenticationTestHandler`. La balise a le format de chaîne `"username:<actual username>"`.


 

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **AppBackend**, puis cliquez sur **Gérer les packages NuGet**.

2. Sur le côté gauche, cliquez sur **En ligne**, puis recherchez **Microsoft.Azure.NotificationHubs** dans la zone **Rechercher**.

3. Dans la liste de résultats, cliquez sur **Microsoft Azure Notification Hubs**, puis sur **Installer**. Procédez à l’installation, puis fermez la fenêtre du Gestionnaire de package NuGet.

	Cette opération ajoute une référence au Kit de développement logiciel (SDK) Azure Notification Hubs à l’aide du <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">package NuGet Microsoft.Azure.Notification Hubs</a>.

4. Nous allons maintenant créer un nouveau fichier de classe qui représente la connexion avec le hub de notification utilisé pour envoyer des notifications. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le dossier **Modèles**, cliquez sur **Ajouter**, puis sur **Classe**. Nommez la nouvelle classe **Notifications.cs**, puis cliquez sur **Ajouter** pour générer la classe.

	![][B6]

5. Dans Notifications.cs, ajoutez l’instruction `using` suivante en haut du fichier :

        using Microsoft.Azure.NotificationHubs;

6. Remplacez la définition de classe `Notifications` par ce qui suit et veillez à remplacer les deux espaces réservés par la chaîne de connexion (avec accès complet) pour votre hub de notification et le nom du hub (disponible dans le [portail Azure Classic](http://manage.windowsazure.com)) :

		public class Notifications
        {
            public static Notifications Instance = new Notifications();
        
            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>", 
																			 "<hub name>");
            }
        }



7. Nous allons ensuite créer un contrôleur nommé **RegisterController**. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le dossier **Contrôleurs**, puis cliquez sur **Ajouter** et sur **Contrôleur**. Cliquez sur l’élément **Web API 2 Controller -- Empty**, puis sur **Ajouter**. Nommez la nouvelle classe **RegisterController**, puis cliquez de nouveau sur **Ajouter** pour générer le contrôleur.

	![][B7]

	![][B8]

8. Dans RegisterController.cs, ajoutez les instructions `using` suivantes :

        using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.NotificationHubs.Messaging;
        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

9. Ajoutez le code suivant dans la définition de classe `RegisterController`. Notez que dans ce code, nous ajoutons une balise d’utilisateur pour l’utilisateur attaché au HttpContext. L’utilisateur a été authentifié et attaché au HttpContext par le filtre de messages que nous avons ajouté, `AuthenticationTestHandler`. Vous pouvez également ajouter des contrôles facultatifs afin de vérifier que l’utilisateur dispose des droits d’inscription nécessaires pour les balises requises.

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
            string newRegistrationId = null;
            
            // make sure there are no existing registrations for this push handle (used for iOS and Android)
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

            if (newRegistrationId == null) 
				newRegistrationId = await hub.CreateRegistrationIdAsync();

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

10. Enregistrez vos modifications.

## Envoi de notifications depuis le serveur principal WebAPI

Dans cette section, vous ajoutez un nouveau contrôleur qui permet aux appareils clients d’envoyer une notification basée sur la balise de nom d’utilisateur à l’aide de la bibliothèque de gestion de services Azure Notification Hubs sur le serveur principal ASP.NET WebAPI.


1. Créez un autre contrôleur nommé **NotificationsController**, en suivant la même procédure que pour la création du contrôleur **RegisterController** dans la section précédente.

2. Dans NotificationsController.cs, ajoutez les instructions `using` suivantes :

        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

3. Ajoutez la méthode suivante à la classe **NotificationsController** :

	Ce code envoie un type de notification basé sur le paramètre `pns` du service de notification de plateforme. La valeur de `to_tag` permet de définir la balise *username* sur le message. Cette balise doit correspondre à une balise de nom d’utilisateur d’une inscription de hub de notification active. Le message de notification est extrait du corps de la demande POST et mis en forme pour le PNS cible.

	Selon le service Platform Notification Service (PNS) que vos appareils pris en charge utilisent pour recevoir des notifications, différentes notifications sont prises en charge dans différents formats. Par exemple sur des appareils Windows, vous pouvez utiliser une [notification toast avec WNS](https://msdn.microsoft.com/library/windows/apps/br230849.aspx) qui n'est pas directement prise en charge par un autre service PNS. Par conséquent, votre serveur principal doit formater la notification dans un format pris en charge pour le service PNS des appareils que vous envisagez de prendre en charge. Utilisez ensuite l’API d'envoi appropriée sur la [classe NotificationHubClient](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.notificationhubclient_methods.aspx)

        public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
        {
            var user = HttpContext.Current.User.Identity.Name;
            string[] userTag = new string[2];
            userTag[0] = "username:" + to_tag;
            userTag[1] = "from:" + user;

            Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
            HttpStatusCode ret = HttpStatusCode.InternalServerError;

            switch (pns.ToLower())
            {
                case "wns":
                    // Windows 8.1 / Windows Phone 8.1
                    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                                "From " + user + ": " + message + "</text></binding></visual></toast>";
                    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                    break;
                case "apns":
                    // iOS
                    var alert = "{"aps":{"alert":"" + "From " + user + ": " + message + ""}}";
                    outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                    break;
                case "gcm":
                    // Android
                    var notif = "{ "data" : {"message":"" + "From " + user + ": " + message + ""}}";
                    outcome = await Notifications.Instance.Hub.SendGcmNativeNotificationAsync(notif, userTag);
                    break;
            }

            if (outcome != null)
            {
                if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                    (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
                {
                    ret = HttpStatusCode.OK;
                }
            }

            return Request.CreateResponse(ret);
        }


4. Appuyez sur la touche **F5** pour exécuter l’application et vérifier le travail que vous avez accompli jusqu’à présent. L’application doit lancer un navigateur web et afficher la page d’accueil d’ASP.NET.

##Publication du nouveau serveur principal WebAPI

1. Nous allons maintenant déployer cette application sur un site web Azure afin de la rendre accessible à tous les appareils. Cliquez avec le bouton droit sur le projet **AppBackend** et sélectionnez **Publier**.

2. Sélectionnez **Microsoft Azure Web Apps** comme cible de publication.

    ![][B15]

3. Connectez-vous avec votre compte Azure et sélectionnez une application web (nouvelle ou existante).

    ![][B16]

4. Notez la propriété de l’**URL de destination** sous l’onglet **Connexion**. Plus loin dans ce didacticiel, nous utiliserons cette URL comme *point de terminaison principal*. Cliquez sur **Publier**.

    ![][B18]


[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG

<!---HONumber=AcomDC_0706_2016-->