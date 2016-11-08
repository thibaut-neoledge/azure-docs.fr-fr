---
title: Contrôle d’accès en fonction du rôle dans Mobile Services à l’aide de .NET et d’Azure Active Directory (Windows Store) | Microsoft Docs
description: Découvrez comment contrôler l’accès en fonction des rôles Azure Active Directory dans votre application Windows Store à l’aide d’un service mobile avec un backend .NET.
documentationcenter: windows
author: wesmc7777
manager: dwrede
editor: ''
services: mobile-services

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 07/21/2016
ms.author: wesmc

---
# Contrôle d’accès en fonction du rôle dans Mobile Services à l’aide de JavaScript et d’Azure Active Directory
[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[!INCLUDE [mobile-services-selector-rbac](../../includes/mobile-services-selector-rbac.md)]

## Vue d'ensemble
Le contrôle d'accès en fonction du rôle (RBAC) consiste à affecter des autorisations aux différents rôles assumés par vos utilisateurs. Il permet de définir des limites en fonction des appartenances des utilisateurs. Ce didacticiel vous montre comment ajouter le contrôle RBAC de base à Azure Mobile Services.

Ce didacticiel présente le contrôle d'accès en fonction du rôle, qui contrôle l'appartenance de chaque utilisateur à un groupe Sales (Ventes) défini dans Azure Active Directory (AAD). Le contrôle sera effectué avec le backend .NET Mobile Service à l'aide de l'[API REST Graph] pour Azure Active Directory. Seuls les utilisateurs appartenant au groupe Sales seront autorisés à accéder aux données.

> [!NOTE]
> L'objectif de ce didacticiel est d'approfondir vos connaissances sur l'authentification pour inclure des pratiques d'autorisation. Vous devez donc au préalable avoir suivi le didacticiel [Ajout de l'authentification à votre application] avec le fournisseur d'authentification Azure Active Directory. Ce didacticiel poursuit la mise à jour de l'application TodoItem utilisée dans le didacticiel [Ajout de l'authentification à votre application].
> 
> 

## Configuration requise
Ce didacticiel requiert les éléments suivants :

* Visual Studio 2013 s'exécutant sous Windows 8.1.
* Achèvement du didacticiel [Ajout de l'authentification à votre application] à l'aide du fournisseur d'authentification Azure Active Directory.

## Génération d'une clé pour l'application intégrée
Avec le didacticiel [Ajout de l'authentification à votre application], vous avez créé une inscription pour l'application intégrée pendant l'étape [Inscription pour utiliser une connexion Azure Active Directory]. Dans cette section, vous allez générer la clé devant être utilisée pour lire les informations d'annuaire avec l'ID client de cette application intégrée.

[!INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]

## Création d'un groupe Sales avec appartenance
[!INCLUDE [mobile-services-aad-rbac-create-sales-group](../../includes/mobile-services-aad-rbac-create-sales-group.md)]

## Création d'un attribut d'autorisation personnalisé pour le service mobile
Dans cette section, vous allez créer un attribut d'autorisation personnalisé pouvant être utilisé pour exécuter des contrôles d'accès sur les opérations de service mobile. L'attribut recherchera un groupe Active Directory en fonction du nom de rôle transmis, puis procèdera aux contrôles d'accès d'après l'appartenance à ce groupe.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet de backend .NET du service mobile, puis sélectionnez **Gérer les packages NuGet**.
2. Dans la boîte de dialogue Gestionnaire de package NuGet, entrez **ADAL** dans les critères de recherche afin de localiser et d'installer la **bibliothèque d'authentification Active Directory** pour votre service mobile. Ce didacticiel a été récemment testé avec la version 3.3.205061641-alpha (version préliminaire) du package de la bibliothèque ADAL.
3. Dans Visual Studio, cliquez avec le bouton droit sur le projet de service mobile, puis sélectionnez **Ajouter** et **Nouveau dossier**. Nommez ce nouveau dossier **Utilities**.
4. Dans Visual Studio, cliquez avec le bouton droit sur ce dossier **Utilities**, puis ajoutez un nouveau fichier de classe nommé **AuthorizeAadRole.cs**.
   
    ![][0]
5. Dans le fichier AuthorizeAadRole.cs, ajoutez les instructions `using` suivantes au début du code.
   
        using System.Net;
        using System.Net.Http;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.Filters;
        using Newtonsoft.Json;
        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;
        using System.IO;
6. Dans le fichier AuthorizeAadRole.cs, ajoutez le type énuméré suivant à l'espace de noms Utilities. Cet exemple utilise uniquement le rôle **Sales**. Les autres rôles sont des exemples de groupes pouvant être utilisés.
   
        public enum AadRoles
        {
            Sales,
            Management,
            Development
        }
7. Dans le fichier AuthorizeAadRole.cs, ajoutez ensuite la définition de classe `AuthorizeAadRole` suivante à l'espace de noms Utilities.
   
        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false, Inherited = true)]
        public class AuthorizeAadRole : AuthorizationFilterAttribute
        {
            private bool isInitialized;
            private bool isHosted;
            private ApiServices services = null;
   
            // Constants used with ADAL and the Graph REST API for AAD
            private const string AadInstance = "https://login.windows.net/{0}";
            private const string GraphResourceId = "https://graph.windows.net/";
            private const string APIVersion = "?api-version=2013-04-05";
   
            // App settings pulled from the Mobile Service
            private string tenantdomain;
            private string clientid;
            private string clientkey;
            private Dictionary<int, string> groupIds = new Dictionary<int, string>();
   
            private string token = null;
   
            public AuthorizeAadRole(AadRoles role)
            {
                this.Role = role;
            }
   
            // private class used to serialize the Graph REST API web response
            private class MembershipResponse
            {
                public bool value;
            }
   
            public AadRoles Role { get; private set; }
   
            // Generate a local dictionary for the role group ids configured as
            // Mobile Service app settings
            private void InitGroupIds()
            {
            }
   
            // Use ADAL and the authentication app settings from the Mobile Service to
            // get an AAD access token
            private string GetAADToken()
            {
            }
   
            // Given an AAD user id, check membership against the group associated with the role.
            private bool CheckMembership(string memberId)
            {
            }
   
            // Called when the user is attempting authorization
            public override void OnAuthorization(HttpActionContext actionContext)
            {
            }
        }
8. Dans le fichier AuthorizeAadRole.cs, mettez à jour la méthode `InitGroupIds` sur la classe `AuthorizeAadRole` comme suit. Cette méthode crée un mappage de dictionnaire des ID de groupe pour chaque rôle.
   
        private void InitGroupIds()
        {
            string groupId;
   
            if (services == null)
                return;
   
            if (!groupIds.ContainsKey((int)AadRoles.Sales))
            {
                if (services.Settings.TryGetValue("AAD_SALES_GROUP_ID", out groupId))
                {
                    groupIds.Add((int)AadRoles.Sales, groupId);
                }
                else
                    services.Log.Error("AAD_SALES_GROUP_ID app setting not found.");
            }
        }
9. Dans le fichier AuthorizeAadRole.cs, mettez à jour la méthode `GetAADToken` sur la classe `AuthorizeAadRole`. Cette méthode utilise les paramètres d'application stockés dans le service mobile pour obtenir un jeton d'accès à AAD à partir de la bibliothèque ADAL.
   
   > [!NOTE]
   > La bibliothèque ADAL pour .NET inclut un cache de jeton en mémoire par défaut afin de réduire le trafic réseau supplémentaire par rapport à Active Directory. Toutefois, vous pouvez écrire votre propre implémentation du cache ou désactiver complètement la mise en cache. Pour plus d'informations, consultez [Bibliothèque d'authentification Azure AD pour .NET].
   > 
   > 
   
        // Use ADAL and the authentication app settings from the Mobile Service to get an AAD access token
        private async Task<string> GetAADToken()
        {
            // Try to get the required AAD authentication app settings from the mobile service.
            if (!(services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                services.Log.Error("GetAADToken() : Could not retrieve mobile service app settings.");
                return null;
            }
   
            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
   
            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientCred);
            if (result != null)
                token = result.AccessToken;
            else
                services.Log.Error("GetAADToken() : Failed to return a token.");
   
            return token;
        }
10. Dans le fichier AuthorizeAadRole.cs, mettez à jour la méthode `CheckMembership` sur la classe `AuthorizeAadRole`. Cette méthode reçoit l'ID d'objet d'un utilisateur. Elle utilise ensuite l'API REST Graph AAD pour déterminer si cet ID d'objet est un ID de membre du groupe associé au rôle sélectionné sur la classe `AuthorizeAadRole`.
    
        private bool CheckMembership(string memberId)
        {
            bool membership = false;
            string url = GraphResourceId + tenantdomain + "/isMemberOf" + APIVersion;
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
    
            // Use the Graph REST API to check group membership in the AAD
            try
            {
                request.Method = "POST";
                request.ContentType = "application/json";
                request.Headers.Add("Authorization", token);
                using (var sw = new StreamWriter(request.GetRequestStream()))
                {
                    // Request body must have the group id and a member id to check for membership
                    string body = String.Format(""groupId":"{0}","memberId":"{1}"",
                        groupIds[(int)Role], memberId);
                    sw.Write("{" + body + "}");
                }
    
                WebResponse response = request.GetResponse();
                StreamReader sr = new StreamReader(response.GetResponseStream());
                string json = sr.ReadToEnd();
                MembershipResponse membershipResponse = JsonConvert.DeserializeObject<MembershipResponse>(json);
                membership = membershipResponse.value;
            }
            catch (Exception e)
            {
                services.Log.Error("OnAuthorization() exception : " + e.Message);
            }
    
            return membership;
        }
11. Dans le fichier AuthorizeAadRole.cs, mettez à jour la méthode `OnAuthorization` dans la classe `AuthorizeAadRole` à l'aide du code suivant. Ce code suppose que l'utilisateur appelant le service mobile s'est authentifié auprès d'AAD. Il obtient ensuite l'ID d'objet AAD de l'utilisateur et vérifie s'il appartient au groupe Active Directory qui correspond au rôle.
    
    > [!NOTE]
    > Vous pourriez rechercher le groupe Active Directory par nom. Toutefois, il est souvent judicieux d'enregistrer l'ID du groupe en tant que paramètre d'application de service mobile. Le nom du groupe est en effet susceptible de changer, mais l'ID reste toujours le même.
    > 
    > 
    
        public override void OnAuthorization(HttpActionContext actionContext)
        {
            if (actionContext == null)
            {
                throw new ArgumentNullException("actionContext");
            }
    
            services = new ApiServices(actionContext.ControllerContext.Configuration);
    
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
            }
    
            bool isAuthorized = false;
            try
            {
                // Initialize a mapping for the group id to our enumerated type
                InitGroupIds();
    
                // Retrieve a AAD token from ADAL
                GetAADToken();
                if (token == null)
                {
                    services.Log.Error("AuthorizeAadRole: Failed to get an AAD access token.");
                }
                else
                {
                    // Check group membership to see if the user is part of the group that corresponds to the role
                    if (!string.IsNullOrEmpty(groupIds[(int)Role]))
                    {
                        ServiceUser serviceUser = controller.User as ServiceUser;
                        if (serviceUser != null && serviceUser.Level == AuthorizationLevel.User)
                        {
                            var idents = serviceUser.GetIdentitiesAsync().Result;
                            AzureActiveDirectoryCredentials clientAadCredentials =
                                idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
                            if (clientAadCredentials != null)
                            {
                                isAuthorized = CheckMembership(clientAadCredentials.ObjectId);
                            }
                        }
                    }
                }
            }
            catch (Exception e)
            {
                services.Log.Error(e.Message);
            }
            finally
            {
                if (isAuthorized == false)
                {
                    services.Log.Error("Denying access");
    
                    actionContext.Response = actionContext.Request
                        .CreateErrorResponse(HttpStatusCode.Forbidden,
                            "User is not logged in or not a member of the required group");
                }
            }
        }
12. Enregistrez les modifications apportées au fichier AuthorizeAadRole.cs.

## Ajout du contrôle d'accès en fonction du rôle pour les opérations de base de données
1. Dans Visual Studio, développez le dossier **Controllers** du projet de service mobile. Ouvrez TodoItemController.cs.
2. Dans le fichier TodoItemController.cs, ajoutez une instruction `using` pour l'espace de noms Utilities contenant l'attribut d'autorisation personnalisé.
   
        using todolistService.Utilities;
3. Toujours dans le fichier TodoItemController.cs, ajoutez l'attribut à la classe de contrôleur ou plusieurs méthodes selon le contrôle d'accès souhaité. Si vous souhaitez que toutes les opérations de contrôleur procèdent au contrôle en fonction d'un même rôle, ajoutez simplement l'attribut à la classe. Procédez comme suit pour ajouter l'attribut à la classe à des fins de test.
   
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
4. Enregistrez le fichier TodoItemController.cs, puis créez le service mobile afin de vérifier l'absence d'erreurs de syntaxe.
5. Publiez le projet de service mobile sur votre compte Azure.

## Test de l'accès client
[!INCLUDE [mobile-services-aad-rbac-test-app](../../includes/mobile-services-aad-rbac-test-app.md)]

<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/add-authorize-aad-role-class.png

<!-- URLs. -->
[Ajout de l'authentification à votre application]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Directory Sync Scenarios]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[Inscription pour utiliser une connexion Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[API REST Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[IsMemberOf]: http://msdn.microsoft.com/library/azure/dn151601.aspx
[Bibliothèque d'authentification Azure AD pour .NET]: https://msdn.microsoft.com/library/azure/jj573266.aspx

<!---HONumber=AcomDC_0727_2016-->