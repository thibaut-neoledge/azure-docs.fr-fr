<!-- saved from url=(0022)http://internet.e-mail -->

Notification des utilisateurs avec Notification Hubs
====================================================

[Mobile Services](/fr-fr/manage/services/notification-hubs/notify-users "Mobile Services")[ASP.NET](/fr-fr/manage/services/notification-hubs/notify-users-aspnet "ASP.NET")

Ce didacticiel explique comment utiliser Azure Notification Hubs pour envoyer des notifications Push à un utilisateur particulier d'une application sur un appareil spécifique. Un serveur principal d'API Web ASP.NET est utilisé pour authentifier les clients et pour générer les notifications. Ce didacticiel fait intervenir le Notification Hub que vous avez créé dans le didacticiel précédent intitulé **Prise en main de Notification Hubs**. Le code d'inscription aux notifications est transféré du client vers le service principal. Ainsi, l'inscription n'est pas terminée tant qu'un client n'a pas été authentifié correctement par le service. Cela signifie également que les informations d'identification du Notification Hub ne sont pas distribuées avec l'application cliente. Le service contrôle également les balises demandées pendant l'inscription.

Ce didacticiel vous familiarise avec les étapes de base suivantes :

-   [Création d'une application ASP.NET avec authentification](#create-application)
-   [Mise à jour de votre application ASP.NET afin de vous inscrire aux notifications](#register-notification)
-   [Mise à jour de l'application pour se connecter et demander l'inscription](#update-app)

Configuration requise
---------------------

-   Visual Studio 2012. Vous pouvez également utiliser Visual Studio Express 2012 pour le Web et Visual Studio Express 2012 pour Windows 8 afin de créer l'application ASP.NET et l'application Windows Store, respectivement.
-   Ce didacticiel fait intervenir l'application et le Notification Hub que vous avez créés dans le didacticiel précédent intitulé **Prise en main de Notification Hubs**. Avant de commencer, vous devez suivre le didacticiel **Prise en main de Notification Hubs** ([Windows Store C\#](/fr-fr/manage/services/notification-hubs/getting-started-windows-dotnet)/[iOS](/fr-fr/manage/services/notification-hubs/get-started-notification-hubs-ios)/[Android](/fr-fr/manage/services/notification-hubs/get-started-notification-hubs-android)).

**Remarque**

Le projet d'API Web ASP.NET créé dans ce didacticiel s'exécute sur votre ordinateur local. Vous pouvez également publier un projet d'API Web ASP.NET sur Azure. Pour plus d'informations, consultez la page [Création d'un service REST propice à la mobilité avec l'API Web ASP.NET et la base de données SQL](/fr-fr/develop/net/tutorials/rest-service-using-web-api/).

Création de l'application ASP.NETCréation d'une application ASP.NET avec authentification
-----------------------------------------------------------------------------------------

Vous allez d'abord créer une application d'API Web ASP.NET. Ce service principal authentifiera les clients, effectuera l'inscription aux notifications Push pour le compte d'un utilisateur authentifié et enverra des notifications.

1.  Dans Visual Studio ou Visual Studio Express 2012 pour le Web, cliquez sur **Fichier**, puis sur **Nouveau projet**, développez **Modèles**, puis **Visual C\#**. Cliquez ensuite sur **Web**, puis sur **Application Web ASP.NET MVC 4**, entrez le nom *NotificationService* et cliquez sur **OK**.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-create-mvc-app.png)

2.  Dans la boîte de dialogue **New ASP.NET MVC Project**, cliquez sur **Empty**, puis sur **OK**.

    Un projet ASP.NET MVC est créé.

3.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet, cliquez sur **Ajouter**, sur **Classe**, puis tapez `AuthenticationTestHandler` et cliquez sur **Ajouter**.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-create-aspnet-class.png)

    Un fichier de code est alors ajouté au projet pour la classe **AuthenticationTestHandler**.

4.  Ouvrez le nouveau fichier de projet AuthenticationTestHandler.cs et remplacez la définition de classe par le code suivant :

         using System;
         using System.Collections.Generic;
         using System.Linq;
         using System.Net;
         using System.Net.Http;
         using System.Threading;
         using System.Threading.Tasks;
         using System.Security.Principal;
         using System.Text;
         using System.Web;
            
         namespace NotificationService
         {
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
                             // Attachez le nouvel objet principal à l'objet HttpContext actuel
                             HttpContext.Current.User = 
                                 new GenericPrincipal(new GenericIdentity(user), new string[0]);
                             System.Threading.Thread.CurrentPrincipal = 
                                 System.Web.HttpContext.Current.User;
                         }
                         else return Unauthorised();
                     } else return Unauthorised();
            
                     return base.SendAsync(request, cancellationToken);
                 }
            
                 private bool verifyUserAndPwd(string user, string password)
                 {
                     // Il ne s'agit pas d'un schéma d'authentification réel.
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
         } 

    **Remarque relative à la sécurité**

    La classe **AuthenticationTestHandler** ne fournit pas une véritable authentification. Elle sert uniquement à simuler l'authentification de base et à renvoyer un principe. Le nom d'utilisateur est requis pour créer des inscriptions au Notification Hub. La mise en œuvre ci-dessus n'est pas sécurisée. Vous devez mettre en œuvre un mécanisme d'authentification sécurisé dans vos applications de production et vos services.

5.  Développez le dossier **App\_Start**, ouvrez le fichier WebApiConfig.cs et ajoutez la ligne de code suivante à la fin de la méthode **Register** :

         config.MessageHandlers.Add(new AuthenticationTestHandler());

    Cela nécessite que les demandes à destination de l'application ASP.NET contiennent l'en-tête Authorization.

Nous avons maintenant créé l'application de base avec un schéma d'authentification fictif pour nous fournir un nom d'utilisateur.

Inscription aux notificationsMise à jour de votre application ASP.NET afin de vous inscrire aux notifications
-------------------------------------------------------------------------------------------------------------

L'étape suivante consiste à ajouter la logique d'inscription pour les Notification Hubs à l'application ASP.NET en créant un contrôleur **Registration**.

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Service Bus**, sur votre espace de noms, **Notification Hubs**, puis choisissez votre Notification Hub et cliquez sur **Informations de connexion**.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-select-hub-connection.png)

2.  Notez le nom de votre Notification Hub et copiez la chaîne de connexion pour **DefaultFullSharedAccessSignature**.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-connection-strings.png)

    Vous allez utiliser cette chaîne de connexion, avec le nom du Notification Hub, à la fois pour vous inscrire et pour envoyer des notifications.

3.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Manage NuGet Packages**.

4.  Dans le volet gauche, sélectionnez la catégorie **Online**, recherchez `WindowsAzure.ServiceBus`, cliquez sur **Installer** au niveau du package **Azure Service Bus**, puis acceptez le contrat de licence.

	![](./media/notification-hubs-aspnet-notify-users/notification-hub-add-nuget-package.png) 

	L'assembly Microsoft.ServiceBus.dll est ajouté à votre projet.

1.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le dossier **Controllers**, cliquez sur **Ajouter**, sur **Contrôleur...**, puis tapez `RegisterController` pour **Controller name**, choisissez **Empty API controller** et cliquez sur **Ajouter**.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-add-register-controller2.png)

    Une classe de contrôleur est ajoutée au projet. Lorsqu'il sera appelé, ce contrôleur procédera à l'inscription d'un appareil aux Notification Hubs.

2.  Ouvrez le nouveau fichier projet RegisterController.cs et ajoutez les instructions **using** suivantes.

         using Microsoft.ServiceBus.Notifications;
         using Newtonsoft.Json.Linq;
         using System.Threading.Tasks;
         using System.Web;

3.  Ajoutez le code suivant dans la nouvelle classe RegisterController :

         // Définissez le client Notification Hubs.
         private NotificationHubClient hubClient;

         // Créez le client dans le constructeur.
         public RegisterController()
         {
             var cn = "<FULL_SAS_CONNECTION_STRING>";
             hubClient = NotificationHubClient
                 .CreateClientFromConnectionString(cn, "<NOTIFICATION_HUB_NAME>");
         }

4.  Mettez à jour le code dans le constructeur en remplaçant *`<NOTIFICATION_HUB_NAME>`* et *`<FULL_SAS_CONNECTION_STRING>`* par les valeurs correspondant à votre Notification Hub, puis cliquez sur **Enregistrer**.

    **Remarque**

    Veillez à utiliser **DefaultFullSharedAccessSignature** pour *`<FULL_SAS_CONNECTION_STRING>`*. Cette revendication permet à votre API Web de créer et de mettre à jour des inscriptions.

5.  Ajoutez le code de la méthode Post suivant dans la classe RegisterController :

         public async Task<RegistrationDescription> Post([FromBody]JObject registrationCall)
         {
             // Obtenez les informations d'inscription dont nous avons besoin à partir de la demande. 
             var platform = registrationCall["platform"].ToString();
             var installationId = registrationCall["instId"].ToString();
             var channelUri = registrationCall["channelUri"] != null 
         
                 registrationCall["channelUri"].ToString() : null;
             var deviceToken = registrationCall["deviceToken"] != null 
         
                 registrationCall["deviceToken"].ToString() : null;       
             var userName = HttpContext.Current.User.Identity.Name;

             // Obtenez les inscriptions pour l'ID d'installation actuel.
             var regsForInstId = await hubClient.GetRegistrationsByTagAsync(installationId, 100);

             bool updated = false;
             bool firstRegistration = true;
             RegistrationDescription registration = null;

             // Recherchez les inscriptions existantes.
             foreach (var registrationDescription in regsForInstId)
             {
                 if (firstRegistration)
                 {

    			// Mettez à jour les balises.

                     registrationDescription.Tags = new HashSet<string>() { installationId, userName };

                     // Nous avons besoin de gérer chaque plateforme séparément.
                     switch (platform)
                     {
                         case "windows":
                             var winReg = registrationDescription as WindowsRegistrationDescription;
                             winReg.ChannelUri = new Uri(channelUri);
                             registration = await hubClient.UpdateRegistrationAsync(winReg);                            
                             break;
                         case "ios":
                             var iosReg = registrationDescription as AppleRegistrationDescription;
                             iosReg.DeviceToken = deviceToken;
                             registration = await hubClient.UpdateRegistrationAsync(iosReg);
                             break;
                     }
                     updated = true;
                     firstRegistration = false;
                 }
                 else
                 {
                     // Nous ne devons pas avoir d'inscriptions supplémentaires ; les supprimer le cas échéant.
                     await hubClient.DeleteRegistrationAsync(registrationDescription);
                 }
             }

             // Créez une nouvelle inscription.
             if (!updated)
             {
                 switch (platform)
                 {
                     case "windows":
                         registration = await hubClient.CreateWindowsNativeRegistrationAsync(channelUri, 
                             new string[] { installationId, userName });
                         break;
                     case "ios":
                         registration = await hubClient.CreateAppleNativeRegistrationAsync(deviceToken, 
                             new string[] { installationId, userName });
                         break;
                 }
             }

             // Envoyez une notification de test.
             sendNotification(string.Format("Test notification for {0}", userName), userName);

             return registration;
         }

    Ce code est appelé par une demande POST et obtient la plateforme et l'information deviceID à partir du corps du message. Ces données, avec l'ID d'installation de l'en-tête de la demande et l'ID utilisateur de l'utilisateur connecté, sont utilisées pour mettre à jour une inscription. S'il n'existe aucune inscription, une nouvelle inscription est créée. Cette inscription est marquée avec l'ID utilisateur et l'ID d'installation.

6.  Ajoutez la méthode sendNotification suivante :

        // Mise en œuvre de base qui envoie une notification aux clients de l'application Windows Store et iOS.
        private async Task sendNotification(string notificationText, string tag)
        {
            try
            {
                // Créez des notifications pour les plateformes Windows Store et iOS.
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                    notificationText + "</text></binding></visual></toast>";
                var alert = "{\"aps\":{\"alert\":\"" + notificationText + 
                    "\"}, \"inAppMessage\":\"" + notificationText + "\"}";

                // Envoyez une notification à l'utilisateur connecté sur les deux plateformes.
                await hubClient.SendWindowsNativeNotificationAsync(toast, tag);
                await hubClient.SendAppleNativeNotificationAsync(alert, tag);
            }
            catch(ArgumentException ex)
            {
                // Attendu lorsqu'il n'existe aucune inscription APNS.
                Console.WriteLine(ex.Message);
            }
        }
        

    Cette méthode est appelée pour envoyer une notification dès qu'une inscription est terminée.

Ensuite, nous allons mettre à jour l'application cliente que vous avez créée dans le didacticiel **Prise en main de Notification Hubs**.

Mise à jour de l'applicationMise à jour de l'application pour se connecter et demander l'inscription
----------------------------------------------------------------------------------------------------

L'application que vous avez créée dans le didacticiel **Prise en main de Notification Hubs** demande l'inscription directement à partir du Notification Hub. Vous allez supprimer ce code d'inscription de l'application cliente et le remplacer par un appel à la nouvelle API Register dans l'application d'API Web ASP.NET.

1.  Appuyez sur F5 pour démarrer l'application ASP.NET et essayer de charger la page par défaut.

    Lorsque le navigateur est affiché, notez le nom d'hôte du site demandé. Vous aurez besoin de cette URL racine lors de la mise à jour de l'application cliente.

    **Remarque**

    Lorsque vous utilisez le serveur Web IIS local ou le serveur Visual Studio Development, vous devez également spécifier le numéro du port. Notez qu'une erreur 404 est renvoyée parce que nous n'avons mis en œuvre aucune page par défaut dans cette application.

2.  Suivez les étapes de l'une des versions suivantes de la procédure d'**Inscription de l'utilisateur actuel pour les notifications Push avec l'API Web ASP.NET**, selon votre plateforme cliente :

    -   [Version Windows Store C\#](/fr-fr/manage/services/notification-hubs/register-users-aspnet-dotnet)
    -   [Version iOS](/fr-fr/manage/services/notification-hubs/howto-register-user-with-aspnet-ios)

3.  Exécutez l'application mise à jour, connectez-vous au service en utilisant la même chaîne pour le nom d'utilisateur et le mot de passe, puis vérifiez que l'ID d'inscription attribué à la notification est affiché.

    Vous recevrez également une notification Push.

    **Remarque**

    Une erreur est générée sur le serveur principal lorsqu'il n'existe pas d'inscription pour une plateforme vers laquelle l'envoi d'une notification a été demandé. Dans ce cas, cette erreur peut être ignorée. Pour voir comment éviter cette situation en utilisant des modèles, consultez la page [Envoi de notifications interplateforme aux utilisateurs avec les Notification Hubs](/fr-fr/manage/services/notification-hubs/notify-users-xplat-aspnet).

4.  (Facultatif) Déployez l'application cliente sur un second appareil, puis exécutez-la et insérez du texte.

    Une notification s'affiche sur chaque appareil.

Étapes suivantes
----------------

Maintenant que vous êtes parvenu à la fin de ce didacticiel, vous pouvez poursuivre avec les didacticiels suivants :

-   **Utilisation des Notification Hubs pour diffuser les dernières nouvelles ([Windows Store C\#](/fr-fr/manage/services/notification-hubs/breaking-news-dotnet) / [iOS](/fr-fr/manage/services/notification-hubs/breaking-news-dotnet))**
    Ce didacticiel propre à la plateforme vous montre comment utiliser des balises pour permettre aux utilisateurs de s'abonner aux types de notifications qui les intéressent.

-   **[Envoi de notifications interplateforme aux utilisateurs avec les Notification Hubs](/fr-fr/manage/services/notification-hubs/notify-users-xplat-aspnet)**
    Ce didacticiel prolonge le présent didacticiel **Notification des utilisateurs avec les Notification Hubs** et explique comment s'inscrire aux notifications en utilisant des modèles propres à la plateforme. Cela vous permet d'envoyer des notifications à partir d'une seule méthode dans votre code côté serveur.

Pour plus d'informations sur Notification Hubs, consultez la page [Azure Notification Hubs](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj927170.aspx).

