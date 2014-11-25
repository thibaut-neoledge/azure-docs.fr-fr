<properties linkid="develop-mobile-tutorials-dotnet-rbac-with-aad" urlDisplayName="Role Based Access Control with Azure Active Directory" pageTitle="Role Based Access Control in Mobile Services and Azure Active Directory (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to control access based on Azure Active Directory roles in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Role Based Access Control in Mobile Services and Azure Active Directory" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/21/2014" ms.author="wesmc" />

# Contrôle d'accès en fonction du rôle dans Mobile Services et Azure Active Directory

<div class="dev-center-tutorial-selector sublanding">
    <a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/" title="Windows Store&nbsp;C#" class="current">Windows Store&nbsp;C#</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/" title=".NET backend" class="current">Serveur principal .NET</a> | 
    <a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/" title="JavaScript backend">JavaScript backend</a>
</div>

Le contrôle d'accès en fonction du rôle (RBAC) consiste à affecter des autorisations aux différents rôles assumés par vos utilisateurs, afin de définir des limites en fonction des appartenances. Ce didacticiel vous familiarise avec la procédure d'ajout du contrôle RBAC de base pour Azure Mobile Services.

Ce didacticiel présente le contrôle d'accès en fonction du rôle, qui contrôle l'appartenance de chaque utilisateur à un groupe Sales (Ventes) défini dans Azure Active Directory (AAD). Le contrôle sera effectué avec le projet de service mobile .NET principal à l'aide de la [bibliothèque cliente Graph][bibliothèque cliente Graph] pour Azure Active Directory. Seuls les utilisateurs appartenant au groupe Sales seront autorisés à accéder aux données.

> [WACOM.NOTE] L'objectif de ce didacticiel est d'approfondir vos connaissances sur l'authentification pour inclure des pratiques d'autorisation. Vous devez donc au préalable avoir suivi le didacticiel [Prise en main de l'authentification][Prise en main de l'authentification] avec le fournisseur d'authentification Azure Active Directory. Ce didacticiel poursuit la mise à jour de l'application TodoItem utilisée dans le didacticiel [Prise en main de l'authentification][Prise en main de l'authentification].

Ce didacticiel vous familiarise avec la procédure suivante :

1.  [Création d'un groupe Sales avec appartenance][Création d'un groupe Sales avec appartenance]
2.  [Génération d'une clé pour l'application intégrée][Génération d'une clé pour l'application intégrée]
3.  [Création d'un attribut d'autorisation personnalisé][Création d'un attribut d'autorisation personnalisé]
4.  [Ajout du contrôle d'accès en fonction du rôle pour les opérations de base de données][Ajout du contrôle d'accès en fonction du rôle pour les opérations de base de données]
5.  [Test de l'accès client][Test de l'accès client]

Ce didacticiel requiert les éléments suivants :

-   Visual Studio 2013 s'exécutant sous Windows 8.1.
-   Achèvement du didacticiel [Prise en main de l'authentification][Prise en main de l'authentification] à l'aide du fournisseur d'authentification Azure Active Directory
-   Achèvement du didacticiel [Stockage des scripts serveur][Stockage des scripts serveur] afin de maîtriser l'utilisation d'un référentiel Git pour stocker des scripts serveur.

## <a name="create-group"></a>Création d'un groupe Sales avec appartenance

[WACOM.INCLUDE [mobile-services-aad-rbac-create-sales-group](../includes/mobile-services-aad-rbac-create-sales-group.md)]

## <a name="generate-key"></a>Génération d'une clé pour l'application intégrée

Avec le didacticiel [Prise en main de l'authentification][Prise en main de l'authentification], vous avez créé une inscription pour l'application intégrée lors de l'étape [Inscription à des fins d'utilisation d'une connexion Azure Active Directory][Inscription à des fins d'utilisation d'une connexion Azure Active Directory]. Dans cette section, vous allez générer la clé devant être utilisée pour lire les informations d'annuaire avec l'ID client de cette application intégrée.

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]

## <a name="create-custom-authorization-attribute"></a>Création d'un attribut d'autorisation personnalisé pour le service mobile

Dans cette section, vous allez créer un attribut d'autorisation personnalisé pouvant être utilisé pour exécuter des contrôles d'accès sur les opérations de service mobile. L'attribut recherchera un groupe Active Directory en fonction du nom de rôle transmis, puis procèdera aux contrôles d'accès d'après l'appartenance à ce groupe.

1.  Dans Visual Studio, cliquez avec le bouton droit sur le projet de service mobile .NET principal, puis sélectionnez **Gérer les packages NuGet**.

2.  Dans la boîte de dialogue Gestionnaire de package NuGet, entrez **ADAL** dans les critères de recherche afin de localiser et d'installer la **bibliothèque d'authentification Active Directory** pour votre service mobile.

3.  Dans le gestionnaire de package NuGet, installez également la **bibliothèque cliente Microsoft Azure Active Directory Graph** pour votre service mobile.

4.  Dans Visual Studio, cliquez avec le bouton droit sur le projet de service mobile, puis sélectionnez **Ajouter** et **Nouveau dossier**. Nommez ce nouveau dossier **Utilities**.

5.  Dans Visual Studio, cliquez avec le bouton droit sur ce dossier **Utilities**, puis ajoutez un nouveau fichier de classe nommé **AuthorizeAadRole.cs**.

    ![][0]

6.  Dans le fichier AuthorizeAadRole.cs, ajoutez les instructions `using` suivantes au début du code.

        using System.Net;
        using System.Net.Http;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.Filters;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;
        using System.Linq.Expressions;

7.  Dans le fichier AuthorizeAadRole.cs, ajoutez le type énuméré suivant à l'espace de noms Utilities. Cet exemple utilise uniquement le rôle **Sales**. Les autres rôles sont des exemples de groupes pouvant être utilisés.

        public enum AadRoles
        {
            Sales,
            Management,
            Development
        }

8.  Dans le fichier AuthorizeAadRole.cs, ajoutez ensuite la définition de classe `AuthorizeAadRole` à l'espace de noms Utilities.

        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false, Inherited = true)]
        public class AuthorizeAadRole : AuthorizationFilterAttribute
        {
            private bool isInitialized;
            private bool isHosted;

            public AuthorizeAadRole(AadRoles role)
            {
                this.Role = role;
            }

            public AadRoles Role { get; private set; }

            public override void OnAuthorization(HttpActionContext actionContext)
            {
            }
        }

9.  Toujours dans le fichier AuthorizeAadRole.cs, ajoutez la méthode `GetAADToken` suivante à la classe `AuthorizeAadRole`.

    > [WACOM.NOTE] Vous devez mettre le jeton en cache plutôt qu'en créer un pour chaque contrôle d'accès. Actualisez ensuite le cache lorsque les tentatives d'utilisation du jeton lèvent une exception AccessTokenExpiredException, comme indiqué dans la [bibliothèque cliente Graph][bibliothèque cliente Graph]. Par souci de simplicité, le code ci-après ne reflète pas cette possibilité, mais cela permet de réduire le trafic réseau supplémentaire vers Active Directory.

        private string GetAADToken(ApiServices services)
        {
            const string AadInstance = "https://login.windows.net/{0}";
            const string GraphResourceId = "https://graph.windows.net";

            string tenantdomain;
            string clientid;
            string clientkey;
            string token = null;

            if (services == null)
                return null;

            // Try to get the AAD app settings from the mobile service.  
            if (!(services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                services.Log.Error("GetAADToken: Could not retrieve all AAD app settings from the mobile service configuration.");
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

10. Dans le fichier AuthorizeAadRole.cs, mettez à jour la méthode `OnAuthorization` dans la classe `AuthorizeAadRole` à l'aide du code suivant. Ce code utilise la [bibliothèque cliente Graph][bibliothèque cliente Graph] pour rechercher le groupe Active Directory correspondant au rôle. Il contrôle ensuite l'appartenance de l'utilisateur à ce groupe à des fins d'autorisation.

    > [WACOM.NOTE] Ce code recherche le groupe Active Directory par nom. Il est souvent judicieux d'enregistrer l'ID du groupe en tant que paramètre d'application de service mobile. Le nom du groupe peut en effet changer, mais l'ID reste toujours le même. Cependant, un changement de nom de groupe implique au minimum un changement de la portée du rôle qui nécessite de mettre à jour le code du service mobile.

        public override void OnAuthorization(HttpActionContext actionContext)
        {
            if (actionContext == null)
            {
                throw new ArgumentNullException("actionContext");
            }

            ApiServices services = new ApiServices(actionContext.ControllerContext.Configuration);

            // Check whether we are running in a mode where local host access is allowed 
            // through without authentication.
            if (!this.isInitialized)
            {
                HttpConfiguration config = actionContext.ControllerContext.Configuration;
                this.isHosted = config.GetIsHosted();
                this.isInitialized = true;
            }

            // No security when hosted locally
            if (!this.isHosted && actionContext.RequestContext.IsLocal)
            {
                services.Log.Warn("AuthorizeAadRole: Local Hosting.");
                return;
            }

            ApiController controller = actionContext.ControllerContext.Controller as ApiController;
            if (controller == null)
            {
                services.Log.Error("AuthorizeAadRole: No ApiController.");
                return;
            }

            string accessToken = GetAADToken(services);
            if (accessToken == null)
            {
                services.Log.Error("AuthorizeAadRole: Failed to get an AAD access token.");
                return;
            }

            GraphConnection graphConnection = new GraphConnection(accessToken);
            if (graphConnection == null)
            {
                services.Log.Error("AuthorizeAadRole: Failed to get a graph connection.");
                return;
            }

            bool isAuthorized = false;

            // Find the group using a filter on the name
            FilterGenerator groupFilter = new FilterGenerator();
            groupFilter.QueryFilter = ExpressionHelper
                .CreateConditionalExpression(typeof(Group), GraphProperty.DisplayName, Role.ToString(), 
                    ExpressionType.Equal);

            // Get the group id from the filtered results
            PagedResults<Group> groupPages = graphConnection.List<Group>(null, groupFilter);
            string groupId = groupPages.Results.FirstOrDefault().ObjectId;

            // Check group membership to see if the user is part of the group that corresponds to the role
            if (!string.IsNullOrEmpty(groupId))
            {
                ServiceUser serviceUser = controller.User as ServiceUser;
                if (serviceUser != null && serviceUser.Level == AuthorizationLevel.User)
                {
                    var idents = serviceUser.GetIdentitiesAsync().Result;
                    var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
                    if (clientAadCredentials != null)
                    {
                        bool isMember = graphConnection.IsMemberOf(groupId, clientAadCredentials.ObjectId);
                        if (isMember)
                        {
                            isAuthorized = true;
                        }
                    }
                }
            }

            if (!isAuthorized)
            {
                actionContext.Response = actionContext.Request
                    .CreateErrorResponse(HttpStatusCode.Forbidden, 
                        "User is not logged in or not a member of the required group");
            }
        }

11. Enregistrez les modifications apportées au fichier AuthorizeAadRole.cs.

## <a name="add-access-checking"></a>Ajout du contrôle d'accès en fonction du rôle pour les opérations de base de données

1.  Dans Visual Studio, développez le dossier **Controllers** du projet de service mobile. Ouvrez TodoItemController.cs.

2.  Dans le fichier TodoItemController.cs, ajoutez une instruction `using` pour l'espace de noms Utilities contenant l'attribut d'autorisation personnalisé.

        using todolistService.Utilities;

3.  Toujours dans le fichier TodoItemController.cs, ajoutez l'attribut à la classe de contrôleur ou plusieurs méthodes selon le contrôle d'accès souhaité. Si vous souhaitez que toutes les opérations de contrôleur procèdent au contrôle en fonction d'un même rôle, ajoutez simplement l'attribut à la classe. Procédez comme suit pour ajouter l'attribut à la classe à des fins de test.

        [AuthorizeAadRole(AadGroups.Sales)]
        public class TodoItemController : TableController<TodoItem>

    Si vous souhaitez contrôler les opérations d'insertion, de mise à jour et de suppression uniquement, définissez l'attribut pour les méthodes comme suit.

        // PATCH tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task<TodoItem> PatchTodoItem(string id, Delta<TodoItem> patch)
        {
            return UpdateAsync(id, patch);
        }

        // POST tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

        // DELETE tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task DeleteTodoItem(string id)
        {
            return DeleteAsync(id);
        }

4.  Enregistrez le fichier TodoItemController.cs, puis créez le service mobile afin de vérifier l'absence d'erreurs de syntaxe.
5.  Publiez le projet de service mobile sur votre compte Azure.

## <a name="test-client"></a>Test de l'accès client

[WACOM.INCLUDE [mobile-services-aad-rbac-test-app](../includes/mobile-services-aad-rbac-test-app.md)]



  [bibliothèque cliente Graph]: http://go.microsoft.com/fwlink/?LinkId=510536
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [Création d'un groupe Sales avec appartenance]: #create-group
  [Génération d'une clé pour l'application intégrée]: #generate-key
  [Création d'un attribut d'autorisation personnalisé]: #create-custom-authorization-attribute
  [Ajout du contrôle d'accès en fonction du rôle pour les opérations de base de données]: #add-access-checking
  [Test de l'accès client]: #test-client
  [Stockage des scripts serveur]: /fr-fr/documentation/articles/mobile-services-store-scripts-source-control/
  [Inscription à des fins d'utilisation d'une connexion Azure Active Directory]: /fr-fr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/add-authorize-aad-role-class.png
