<properties linkid="develop-mobile-tutorials-dotnet-aad-graph-info" urlDisplayName="Accessing Azure Active Directory Graph Information" pageTitle="Accessing Azure Active Directory Graph Information (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to access Azure Active Directory information using the Graph API in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Accessing Azure Active Directory Graph Information" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/20/2014" ms.author="wesmc"></tags>

# Accès aux informations Azure Active Directory Graph

<div class="dev-center-tutorial-selector sublanding">
    <a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-aad-graph-info/" title="Windows Store&nbsp;C#" class="current">Windows Store&nbsp;C#</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/" title=".NET backend" class="current">Serveur principal .NET</a> |
    <a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/" title="JavaScript backend">JavaScript backend</a>
</div>

Comme tous les autres fournisseurs d'identité proposés avec Mobile Services, le fournisseur Azure Active Directory (AAD) prend en charge une [bibliothèque cliente Graph][bibliothèque cliente Graph] enrichie pouvant être utilisée pour l'accès par programme à l'annuaire. Dans ce didacticiel, vous allez mettre à jour l'application ToDoList pour personnaliser l'expérience des utilisateurs authentifiés d'après les informations utilisateur supplémentaires collectées à partir de l'annuaire à l'aide de la [bibliothèque cliente Graph][bibliothèque cliente Graph].

> [WACOM.NOTE] L'objectif de ce didacticiel est d'approfondir vos connaissances sur l'authentification à l'aide d'Azure Active Directory. Vous devez donc au préalable avoir suivi le didacticiel [Prise en main de l'authentification][Prise en main de l'authentification] avec le fournisseur d'authentification Azure Active Directory. Ce didacticiel poursuit la mise à jour de l'application TodoItem utilisée dans le didacticiel [Prise en main de l'authentification][Prise en main de l'authentification].

Ce didacticiel vous familiarise avec la procédure suivante :

1.  [Génération d'une clé d'accès pour l'inscription de l'application dans AAD][Génération d'une clé d'accès pour l'inscription de l'application dans AAD]
2.  [Création d'une API personnalisée GetUserInfo][Création d'une API personnalisée GetUserInfo]
3.  [Mise à jour de l'application pour utiliser l'API personnalisée][Mise à jour de l'application pour utiliser l'API personnalisée]
4.  [Test de l'application][Test de l'application]

## Configuration requise

Avant de commencer ce didacticiel, vous devez suivre les didacticiels Mobile Services suivants :

-   [Prise en main de l'authentification][Prise en main de l'authentification]
    Ajoute une exigence de connexion à l'exemple d'application TodoList.

-   [Didacticiel relatif aux API personnalisées][Didacticiel relatif aux API personnalisées]
    Explique comment appeler une API personnalisée.

## <a name="generate-key"></a>Génération d'une clé d'accès pour l'inscription de l'application dans AAD

Avec le didacticiel [Prise en main de l'authentification][Prise en main de l'authentification], vous avez créé une inscription pour l'application intégrée lors de l'étape [Inscription à des fins d'utilisation d'une connexion Azure Active Directory][Inscription à des fins d'utilisation d'une connexion Azure Active Directory]. Dans cette section, vous allez générer la clé devant être utilisée pour lire les informations d'annuaire avec l'ID client de cette application intégrée.

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key][mobile-services-generate-aad-app-registration-access-key]]

## <a name="create-api"></a>Création d'une API personnalisée GetUserInfo

Dans cette section, vous allez créer l'API personnalisée GetUserInfo devant utiliser la [bibliothèque cliente Graph][bibliothèque cliente Graph] pour extraire des informations utilisateur supplémentaires à partir d'AAD.

Si vous n'avez encore jamais utilisé d'API personnalisées avec Mobile Services, suivez le [didacticiel relatif aux API personnalisées][Didacticiel relatif aux API personnalisées] avant de poursuivre.

1.  Dans Visual Studio, cliquez avec le bouton droit sur le projet de service mobile .NET principal, puis sélectionnez **Gérer les packages NuGet**.
2.  Dans la boîte de dialogue Gestionnaire de package NuGet, entrez **ADAL** dans les critères de recherche afin de localiser et d'installer la **bibliothèque d'authentification Active Directory** pour votre service mobile.
3.  Dans le gestionnaire de package NuGet, installez également la **bibliothèque cliente Microsoft Azure Active Directory Graph** pour votre service mobile.

4.  Dans Visual Studio, cliquez avec le bouton droit sur le dossier **Controllers** du projet de service mobile, puis cliquez sur **Ajouter** pour ajouter un nouveau **contrôleur personnalisé Microsoft Azure Mobile Services** nommé `GetUserInfoController`. Le client appellera cette API pour obtenir des informations utilisateur d'Active Directory.

5.  Dans le nouveau fichier GetUserInfoController.cs, ajoutez les instructions `using` suivantes.

        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;

6.  Dans ce même fichier GetUserInfoController.cs, ajoutez la méthode `GetAADToken` suivante à la classe.

        private string GetAADToken()
        {
            const string AadInstance = "https://login.windows.net/{0}";
            const string GraphResourceId = "https://graph.windows.net";

            string tenantdomain;
            string clientid;
            string clientkey;
            string token = null;

            // Try to get the AAD app settings from the mobile service.  
            if (!(Services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  Services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  Services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                Services.Log.Error("GetUserInfo API: Could not retrieve AAD app settings from the mobile service configuration.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = authContext.AcquireToken(GraphResourceId, clientCred);

            if (result != null)            
                token = result.AccessToken;

            return token;
        }

    Cette méthode utilise les paramètres d'application configurés pour le service mobile dans le [portail de gestion Azure][portail de gestion Azure] afin d'obtenir un jeton d'accès à Active Directory.

7.  Dans ce même fichier GetUserInfoController.cs, ajoutez la méthode `GetAADUser` suivante à la classe.

        private User GetAADUser()
        {
            ServiceUser serviceUser = (ServiceUser)this.User;

            // Need a user
            if (serviceUser == null || serviceUser.Level != AuthorizationLevel.User)
            {
                Services.Log.Error("GetUserInfo API: No Service or wrong Authorizationlevel");
                return null;
            }

            // Get the user's AAD object id
            var idents = serviceUser.GetIdentitiesAsync().Result;
            var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            if (clientAadCredentials == null)
            {
                Services.Log.Error("GetUserInfo API: Could not get AAD credientials for the logged in user.");
                return null;
            }

            string accesstoken = GetAADToken();
            if (accesstoken == null)
            {
                Services.Log.Error("GetUserInfo API: Failed to get an AAD access token.");
                return null;
            }

            GraphConnection graphConnection = new GraphConnection(accesstoken);
            var user = graphConnection.Get<User>(clientAadCredentials.ObjectId);

            return user;
        }

    Cette méthode obtient l'ID objet Active Directory pour l'utilisateur autorisé, puis utilise la bibliothèque cliente Graph pour obtenir les informations relatives à l'utilisateur à partir d'Active Directory.

8.  Dans le fichier GetUserInfoController.cs, remplacez ensuite la méthode `Get` par la méthode suivante, qui renvoie l'objet `User` de la bibliothèque cliente Graph et requiert qu'un utilisateur autorisé appelle l'API.

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public User Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return GetAADUser();
        }

9.  Enregistrez vos modifications, puis configurez le service de manière à vérifier l'absence d'erreurs de syntaxe.
10. Publiez le projet de service mobile sur votre compte Azure.

## <a name="update-app"></a>Mise à jour de l'application pour utiliser l'API personnalisée GetUserInfo

Dans cette section, vous allez mettre à jour la méthode `AuthenticateAsync` implémentée dans le didacticiel [Prise en main de l'authentification][Prise en main de l'authentification] pour appeler l'API personnalisée et renvoyer des informations supplémentaires sur l'utilisateur à partir d'AAD.

[WACOM.INCLUDE [mobile-services-aad-graph-info-update-app][mobile-services-aad-graph-info-update-app]]

## <a name="test-app"></a>Test de l'application

[WACOM.INCLUDE [mobile-services-aad-graph-info-test-app][mobile-services-aad-graph-info-test-app]]

## <a name="next-steps"></a>Étapes suivantes

Dans le prochain didacticiel, [Contrôle d'accès en fonction du rôle avec AAD dans Mobile Services][Contrôle d'accès en fonction du rôle avec AAD dans Mobile Services], vous utiliserez le contrôle d'accès en fonction du rôle avec Azure Active Directory (AAD) pour contrôler l'appartenance aux groupes avant d'autoriser l'accès.



  [Windows Store C#]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-aad-graph-info/ "Windows Store C#"
  [Serveur principal .NET]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/ ".NET backend"
  [JavaScript backend]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/ "JavaScript backend"
  [bibliothèque cliente Graph]: http://go.microsoft.com/fwlink/?LinkId=510536
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
  [Génération d'une clé d'accès pour l'inscription de l'application dans AAD]: #generate-key
  [Création d'une API personnalisée GetUserInfo]: #create-api
  [Mise à jour de l'application pour utiliser l'API personnalisée]: #update-app
  [Test de l'application]: #test-app
  [Didacticiel relatif aux API personnalisées]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api/
  [Inscription à des fins d'utilisation d'une connexion Azure Active Directory]: /fr-fr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [mobile-services-generate-aad-app-registration-access-key]: ../includes/mobile-services-generate-aad-app-registration-access-key.md
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [mobile-services-aad-graph-info-update-app]: ../includes/mobile-services-aad-graph-info-update-app.md
  [mobile-services-aad-graph-info-test-app]: ../includes/mobile-services-aad-graph-info-test-app.md
  [Contrôle d'accès en fonction du rôle avec AAD dans Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/
