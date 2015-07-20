<properties 
	pageTitle="Accès aux informations de graphique Azure Active Directory (Windows Store) | Centre de développement mobile" 
	description="Découvrez comment accéder aux informations Azure Active Directory à l'aide de l'API Graph dans votre application Windows Store." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/09/2015" 
	ms.author="wesmc"/>

# Accès aux informations Azure Active Directory Graph



[AZURE.INCLUDE [mobile-services-selector-aad-graph](../../includes/mobile-services-selector-aad-graph.md)]

##Vue d'ensemble

Comme tous les autres fournisseurs d'identité proposés avec Mobile Services, le fournisseur Azure Active Directory (AAD) prend en charge une API Graph enrichie pouvant être utilisée pour l'accès par programme à l'annuaire. Dans ce didacticiel, vous allez mettre à jour l'application ToDoList pour personnaliser l'expérience des utilisateurs authentifiés en retournant les informations utilisateur supplémentaires collectées à partir de l'annuaire à l'aide de l'[API REST Graph].

Pour plus d'informations sur l'API Graph Azure AD, consultez le [blog de l'équipe Azure Active Directory Graph].


>[AZURE.NOTE]L'objectif de ce didacticiel est d'approfondir vos connaissances sur l'authentification à l'aide d'Azure Active Directory. Vous devez donc au préalable avoir suivi le didacticiel [Ajout de l'authentification à votre application] avec le fournisseur d'authentification Azure Active Directory. Ce didacticiel poursuit la mise à jour de l'application TodoItem utilisée dans le didacticiel [Ajout de l'authentification à votre application].




##Configuration requise 

Avant de commencer ce didacticiel, vous devez suivre les didacticiels Mobile Services suivants :

+ [Ajout de l'authentification à votre application]<br/>Ajoute une exigence de connexion à l'exemple d'application TodoList.

+ [Didacticiel relatif aux API personnalisées]<br/>Explique comment appeler une API personnalisée.



## <a name="generate-key"></a>Génération d'une clé d'accès pour l'inscription de l'application dans AAD


Avec le didacticiel [Ajout de l'authentification à votre application], vous avez créé une inscription pour l'application intégrée pendant l'étape [Inscription pour utiliser une connexion Azure Active Directory]. Dans cette section, vous allez générer la clé devant être utilisée pour lire les informations d'annuaire avec l'ID client de cette application intégrée.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]


## <a name="create-api"></a>Création d'une API personnalisée GetUserInfo

Dans cette section, vous allez créer l'API personnalisée GetUserInfo devant utiliser l'API Graph Azure AD pour extraire des informations utilisateur supplémentaires à partir d'AAD.

Si vous n'avez encore jamais utilisé d'API personnalisées avec Mobile Services, suivez le [didacticiel relatif aux API personnalisées] avant de poursuivre.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet de backend .NET du service mobile, puis sélectionnez **Gérer les packages NuGet**.
2. Dans la boîte de dialogue Gestionnaire de package NuGet, entrez **ADAL** dans les critères de recherche afin de localiser et d'installer la **bibliothèque d'authentification Active Directory** pour votre service mobile. Ce didacticiel a été récemment testé avec la version 3.3.205061641-alpha (version préliminaire) du package de la bibliothèque ADAL.


3. Dans Visual Studio, cliquez avec le bouton droit sur le dossier **Contrôleurs** du projet de service mobile, puis cliquez sur **Ajouter** pour ajouter un nouveau **contrôleur personnalisé Microsoft Azure Mobile Services** nommé `GetUserInfoController`. Le client appellera cette API pour obtenir des informations utilisateur d'Active Directory.

4. Dans le nouveau fichier GetUserInfoController.cs, ajoutez les instructions `using` suivantes.

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using System.Globalization;
		using System.Threading.Tasks;
		using Newtonsoft.Json;
		using System.IO;

5. Dans le nouveau fichier GetUserInfoController.cs, ajoutez la classe `UserInfo` suivante pour le stockage des informations que vous souhaitez collecter à partir d'AAD.

	    public class UserInfo
	    {
	        public String displayName { get; set; }
	        public String streetAddress { get; set; }
	        public String city { get; set; }
	        public String state { get; set; }
	        public String postalCode { get; set; }
	        public String mail { get; set; }
	        public String[] otherMails { get; set; }
	
	        public override string ToString()
	        {
	            return "displayName : " + displayName + "\n" +
	                   "streetAddress : " + streetAddress + "\n" +
	                   "city : " + city + "\n" +
	                   "state : " + state + "\n" +
	                   "postalCode : " + postalCode + "\n" +
	                   "mail : " + mail + "\n" +
	                   "otherMails : " + string.Join(", ",otherMails);
	        }
	    }

6. Dans ce même fichier GetUserInfoController.cs, ajoutez les variables de membre suivantes à la classe méthode `GetUserInfoController`.

        private const string AadInstance = "https://login.windows.net/{0}";
        private const string GraphResourceId = "https://graph.windows.net/";
        private const string APIVersion = "?api-version=2013-04-05";

        private string tenantdomain;
        private string clientid;
        private string clientkey;
        private string token = null;


7. Dans ce même fichier GetUserInfoController.cs, ajoutez la méthode `GetAADToken` suivante à la classe.

        private async Task<string> GetAADToken()
        {
            // Try to get the AAD app settings from the mobile service.  
            if (!(Services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  Services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  Services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                Services.Log.Error("GetAADToken() : Could not retrieve mobile service app settings.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientCred);

            if (result != null)
                token = result.AccessToken;
            else
                Services.Log.Error("GetAADToken() : Failed to return a token.");

            return token;
        }

    Cette méthode utilise les paramètres d'application configurés pour le service mobile dans le [portail de gestion Azure] afin d'obtenir un jeton d'accès à Active Directory.

7. Dans ce même fichier GetUserInfoController.cs, ajoutez la méthode `GetAADUser` suivante à la classe.

        private async Task<UserInfo> GetAADUser()
        {
            ServiceUser serviceUser = (ServiceUser)this.User;

            // Need a user
            if (serviceUser == null || serviceUser.Level != AuthorizationLevel.User)
            {
                Services.Log.Error("GetAADUser() : No ServiceUser or wrong Authorizationlevel");
                return null;
            }

            // Get the user's AAD object id
            var idents = serviceUser.GetIdentitiesAsync().Result;
            var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            if (clientAadCredentials == null)
            {
                Services.Log.Error("GetAADUser() : Could not get AAD credientials for the logged in user.");
                return null;
            }

            if (token == null)
                await GetAADToken();

            if (token == null)
            {
                Services.Log.Error("GetAADUser() : No token.");
                return null;
            }

            // User the AAD Graph REST API to get the user's information
            string url = GraphResourceId + tenantdomain + "/users/" + clientAadCredentials.ObjectId + APIVersion;
            Services.Log.Info("GetAADUser() : Request URL : " + url);
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
            request.Method = "GET";
            request.ContentType = "application/json";
            request.Headers.Add("Authorization", token);
            UserInfo userinfo = null;
            try
            {
                WebResponse response = await request.GetResponseAsync();
                StreamReader sr = new StreamReader(response.GetResponseStream());
                string userjson = sr.ReadToEnd();
                userinfo = JsonConvert.DeserializeObject<UserInfo>(userjson);
                Services.Log.Info("GetAADUser user : " + userinfo.ToString());
            }
            catch(Exception e)
            {
                Services.Log.Error("GetAADUser exception : " + e.Message);
            }

            return userinfo;
        }

    Cette méthode obtient l'ID objet Active Directory pour l'utilisateur autorisé, puis utilise l'API REST Graph pour obtenir les informations relatives à l'utilisateur à partir d'Active Directory.


8. Dans le fichier GetUserInfoController.cs, remplacez ensuite la méthode `Get` par la méthode suivante. Cette méthode retourne l'entité utilisateur à partir d'Azure Active Directory à l'aide de l'API REST Graph et nécessite qu'un utilisateur autorisé appelle l'API.

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public async Task<UserInfo> Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return await GetAADUser();
        }

9. Enregistrez vos modifications, puis configurez le service de manière à vérifier l'absence d'erreurs de syntaxe.
10. Publiez le projet de service mobile sur votre compte Azure. 


## <a name="update-app"></a>Mise à jour de l'application pour utiliser l'API personnalisée GetUserInfo

Dans cette section, vous allez mettre à jour la méthode `AuthenticateAsync` implémentée dans le didacticiel [Ajout de l'authentification à votre application] pour appeler l'API personnalisée et renvoyer des informations supplémentaires sur l'utilisateur à partir d'AAD.

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../../includes/mobile-services-aad-graph-info-update-app.md)]
  


## <a name="test-app"></a>Test de l'application

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../../includes/mobile-services-aad-graph-info-test-app.md)]





##<a name="next-steps"></a>Étapes suivantes

Dans le prochain didacticiel, [Contrôle d'accès en fonction du rôle avec AAD dans Mobile Services], vous utiliserez le contrôle d'accès en fonction du rôle avec Azure Active Directory (AAD) pour contrôler l'appartenance aux groupes avant d'autoriser l'accès.



<!-- Anchors. -->
[Generate an access key for the App registration in AAD]: #generate-key
[Create a GetUserInfo custom API]: #create-api
[Update the app to use the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images -->


<!-- URLs. -->
[Ajout de l'authentification à votre application]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[portail de gestion Azure]: https://manage.windowsazure.com/
[API REST Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Didacticiel relatif aux API personnalisées]: mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api.md
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[Inscription pour utiliser une connexion Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[blog de l'équipe Azure Active Directory Graph]: http://go.microsoft.com/fwlink/?LinkId=510536
[Get User]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[Contrôle d'accès en fonction du rôle avec AAD dans Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac.md
 

<!---HONumber=July15_HO2-->