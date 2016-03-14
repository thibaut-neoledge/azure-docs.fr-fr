<properties
	pageTitle="Version préliminaire d’Azure Active Directory B2C | Microsoft Azure"
	description="Comment créer une application web qui appelle une API web à l’aide d’Azure Active Directory B2C."
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Version préliminaire d’Azure AD B2C : appeler une API web à partir d’une application web .NET


<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Avec Azure Active Directory (Azure AD) B2C, vous pouvez ajouter de puissantes fonctionnalités de gestion des identités en libre-service à vos applications web et API web, en seulement quelques étapes. Cet article explique comment créer une application web de type « liste des tâches » MVC (Model-View-Controller) .NET, qui appelle une API web .NET en utilisant des jetons du porteur OAuth 2.0. L’application web et l’API web utilisent toutes les deux Azure AD B2C pour gérer les identités des utilisateurs et authentifier les utilisateurs.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Cet article ne couvre pas l'implémentation de la connexion, de l'inscription et de la gestion de profil avec Azure AD B2C. Il s'intéresse principalement à l'appel d'API web après que l'utilisateur s'est authentifié. Si ce n’est encore fait, vous devez commencer par consulter le [didacticiel sur la prise en main de l’application web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) pour en savoir plus sur les principes fondamentaux d’Azure AD B2C.

## Obtention d'un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou un client. Un répertoire est un conteneur destiné à recevoir tous vos utilisateurs, applications, groupes et autres. Si vous n’en possédez pas déjà un, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant d’aller plus loin dans ce guide.

## Création d'une application

Vous devez maintenant créer dans votre répertoire B2C une application fournissant à Azure AD certaines informations nécessaires pour communiquer de manière sécurisée avec votre application. Dans ce cas, l’application web et l’API web sont toutes les deux représentées par un seul **ID d’application**, car elles constituent une application logique. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md). Veillez à effectuer les opérations suivantes :

- Inclure une **application web/API web** dans l’application.
- Entrer `https://localhost:44316/` comme **URL de réponse**. Il s’agit de l’URL par défaut pour cet exemple de code.
- Créer une **clé secrète d’application** pour votre application et la copier. Vous en aurez besoin ultérieurement. Cette valeur doit être [placée dans une séquence d’échappement XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) avant son utilisation.
- Copiez l’**ID d’application** affecté à votre application. Vous en aurez besoin ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Création de vos stratégies

Dans Azure AD B2C, chaque expérience utilisateur est définie par une [stratégie](active-directory-b2c-reference-policies.md). Cette application web contient trois expériences liées à l’identité : l’inscription, la connexion et la modification du profil. Vous devez créer une stratégie de chaque type, comme décrit dans l’[article de référence de stratégie](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Lors de la création des trois stratégies, assurez-vous de :

- Choisir le **nom d’affichage** et d’autres attributs d’inscription dans votre stratégie d’inscription.
- Choisir les revendications **Nom d’affichage** et **ID objet** comme revendications d’application pour chaque stratégie. Vous pouvez aussi choisir d'autres revendications.
- Copier le **nom** de chaque stratégie après sa création. Il doit porter le préfixe `b2c_1_`. Vous aurez besoin des noms de ces stratégies ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Une fois vos 3 stratégies créées, vous pouvez générer votre application.

Remarque : cet article n’explique pas comment utiliser les stratégies que vous venez de créer. Pour en savoir plus sur la façon dont les stratégies fonctionnent dans Azure AD B2C, commencez par le [didacticiel sur la prise en main de l’application web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Téléchargement du code

Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet). Pour générer l’exemple à mesure que vous avancez, vous pouvez [télécharger la structure de projet sous la forme d’un fichier zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip). Vous pouvez également cloner la structure :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

L’application terminée est également [disponible en tant que fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip) ou sur la branche `complete` du même dépôt.

Une fois l’exemple de code téléchargé, ouvrez le fichier .sln Visual Studio pour commencer. Il existe deux projets dans la solution : un projet `TaskWebApp` et un projet `TaskService`. `TaskWebApp` est le serveur frontal d’application web Windows Presentation Foundation (WPF) avec lequel l’utilisateur interagit. `TaskService` est l’API web du serveur principal de l’application qui stocke la liste des tâches de chaque utilisateur.

## Configuration du service de tâches

Quand `TaskService` reçoit une demande de `TaskWebApp`, il recherche un jeton d’accès valide pour authentifier la demande. Pour valider le jeton d’accès, vous devez fournir à `TaskService` des informations relatives à votre application. Dans le projet `TaskService`, ouvrez le fichier `web.config` qui se trouve à la racine du projet et remplacez les valeurs de la section `<appSettings>` :

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/{1}/{2}?p={3}" />
    <add key="ida:Tenant" value="{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal}" />
    <add key="ida:PolicyId" value="{Enter the name of one of the policies you created, like `b2c_1_my_sign_in_policy`}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


Cet article n’explique pas comment sécuriser `TaskService`. Pour apprendre comment une API web authentifie en toute sécurité les demandes à l’aide d’Azure AD B2C, découvrez l’[article sur la prise en main de l’API web](active-directory-b2c-devquickstarts-api-dotnet.md).

## Configuration de l’application web de la tâche

Pour que `TaskWebApp` communique avec Azure AD B2C, vous devez renseigner certains paramètres courants. Dans le projet `TaskWebApp`, ouvrez le fichier `web.config` qui se trouve à la racine du projet et remplacez les valeurs de la section `<appSettings>`. Ces valeurs seront utilisées dans l’application web.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g.g b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Il existe également deux décorateurs `[PolicyAuthorize]` qui vous demandent de fournir le nom de votre stratégie de connexion. L’attribut `[PolicyAuthorize]` sert à appeler une stratégie particulière quand un utilisateur tente d’accéder à une page de l’application qui requiert une authentification.

```C#
// Controllers\HomeController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public ActionResult Claims()
{
```

```C#
// Controllers\TasksController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public class TasksController : Controller
{
```

## Obtention des jetons d'accès et appel de l'API de tâche

Cette section explique comment procéder à un échange de jeton OAuth 2.0 dans une application web à l’aide des bibliothèques et des infrastructures Microsoft. Si vous n’êtes pas familiarisé avec les codes d’autorisation et les jetons d’accès, nous vous conseillons de consulter les [informations de référence sur le protocole OpenID Connect](active-directory-b2c-reference-protocols.md).

### Obtention d'un code d'autorisation

La première étape d’un appel de l’API web `TaskService` consiste à authentifier l’utilisateur et à recevoir un code d’autorisation d’Azure AD. Vous pouvez recevoir un code d’autorisation d’Azure AD après l’exécution réussie de n’importe quelle stratégie. Il peut s’agir de stratégies de connexion, d’inscription et de modification de profil.

Pour commencer, installez l’intergiciel OWIN OpenID Connect à l’aide de la console du gestionnaire de package Visual Studio. Utilisez OWIN pour envoyer les requêtes d’authentification à Azure AD et gérer leurs réponses :

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskWebApp
```

Ouvrez le fichier `App_Start\Startup.Auth.cs`. Il s’agit de l’emplacement où vous allez configurer le pipeline de l’authentification OWIN en entrant les détails de votre répertoire B2C et de l’application que vous avez créée :

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
	public const string AcrClaimType = "http://schemas.microsoft.com/claims/authnclassreference";
	public const string PolicyKey = "b2cpolicy";
	public const string OIDCMetadataSuffix = "/.well-known/openid-configuration";

	// App config settings
	public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
	public static string clientSecret = ConfigurationManager.AppSettings["ida:ClientSecret"];
	public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
	public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
	public static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

	// B2C policy identifiers
	public static string SignUpPolicyId = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
	public static string SignInPolicyId = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
	public static string ProfilePolicyId = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

	public void ConfigureAuth(IAppBuilder app)
	{
		app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

		app.UseCookieAuthentication(new CookieAuthenticationOptions());

		OpenIdConnectAuthenticationOptions options = new OpenIdConnectAuthenticationOptions
		{
			// These are standard OpenID Connect parameters, with values pulled from web.config
			ClientId = clientId,
			RedirectUri = redirectUri,
			PostLogoutRedirectUri = redirectUri,
			Notifications = new OpenIdConnectAuthenticationNotifications
			{
				AuthenticationFailed = OnAuthenticationFailed,
				RedirectToIdentityProvider = OnRedirectToIdentityProvider,
				AuthorizationCodeReceived = OnAuthorizationCodeReceived,
			},
			Scope = "openid offline_access",

			// The PolicyConfigurationManager takes care of getting the correct Azure AD authentication
			// endpoints from the OpenID Connect metadata endpoint. It is included in the PolicyAuthHelpers folder.
			ConfigurationManager = new PolicyConfigurationManager(
				String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, "/v2.0", OIDCMetadataSuffix),
				new string[] { SignUpPolicyId, SignInPolicyId, ProfilePolicyId }),

			// This piece is optional - it is used for displaying the user's name in the navigation bar.
			TokenValidationParameters = new System.IdentityModel.Tokens.TokenValidationParameters
			{
				NameClaimType = "name",
			},
		};

		app.UseOpenIdConnectAuthentication(options);
	}
	...
}
```

### Obtention d’un jeton d’accès à l’aide du code d’autorisation

Votre application web est désormais configurée pour authentifier les utilisateurs à l’aide de votre répertoire B2C et recevoir des codes d’autorisation d’Azure AD. L’étape suivante consiste à échanger les codes d’autorisation contre des jetons d’accès d’Azure AD.

Quand vos applications web .NET ont besoin d’obtenir des jetons d’accès d’Azure AD, vous pouvez utiliser la bibliothèque d’authentification Active Directory (ADAL). Vous n’êtes pas obligé d’utiliser la bibliothèque ADAL pour cette procédure, mais elle vous facilite le travail en gérant de nombreux détails. Ceux-ci incluent l’envoi de messages d’authentification OAuth 2.0, la mise en cache et l’actualisation des jetons.

Tout d’abord, installez la bibliothèque ADAL dans le projet `TaskWebApp` à l’aide de la console du gestionnaire de package :

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskWebApp -IncludePrerelease
```

Ensuite, vous devez transmettre le code d’autorisation à la bibliothèque ADAL afin que celle-ci obtienne des jetons pour vous. L’intergiciel OWIN OpenID Connect fournit une notification pour vous permettre d’utiliser ce code d’autorisation. La notification est envoyée chaque fois que votre application reçoit un code d’autorisation d’Azure AD. Dans `App_Start\Startup.Auth.cs`, implémentez le gestionnaire de notification `OnAuthorizationCodeReceived` à l’aide de la bibliothèque ADAL :

```C#
// App_Start\Startup.Auth.cs

private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
	// The user's objectId is extracted from the claims provided in the id_token, and used to cache tokens in ADAL
	// The authority is constructed by appending your B2C directory's name to "https://login.microsoftonline.com/"
	// The client credential is where you provide your application secret, and it is used to authenticate the application to Azure AD
	string userObjectID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, string.Empty, string.Empty);
	ClientCredential credential = new ClientCredential(clientId, clientSecret);

	// We don't care which policy is used to access the TaskService, so let's use the most recent policy as indicated in the sign-in token
	string mostRecentPolicy = notification.AuthenticationTicket.Identity.FindFirst(Startup.AcrClaimType).Value;

	// The Authentication Context is ADAL's primary class, which represents your connection to your B2C directory
	// ADAL uses an in-memory token cache by default. In this case, we've extended the default cache to use a simple per-user session cache
	AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));

	// Here you ask for a token by using the web app's clientId as the scope, because the web app and service share the same clientId.
	// The token will be stored in the ADAL token cache for use in our controllers
	AuthenticationResult result = await authContext.AcquireTokenByAuthorizationCodeAsync(notification.Code, new Uri(redirectUri), credential, new string[] { clientId }, mostRecentPolicy);
}
```

### Obtention d'un jeton d'accès dans les contrôleurs

Après avoir obtenu un jeton d’accès pour le serveur principal `TaskService` et l’avoir stocké dans le cache de jetons de la bibliothèque ADAL, vous devez utiliser. `TasksController` est responsable de la communication avec l’API `TaskService`, et envoie des requêtes HTTP à celle-ci pour lire, créer et supprimer des tâches. Avant que la requête HTTP ne soit envoyée, obtenez un jeton d’accès de la bibliothèque ADAL :

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
	AuthenticationResult result = null;
	try
	{
		string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, Startup.tenant, string.Empty, string.Empty);
		ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

		// We don't care which policy is used to access the TaskService, so let's use the most recent policy
		string mostRecentPolicy = ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value;

		// Here you ask for a token by using the web app's clientId as the scope, because the web app and service share the same clientId.
		// AcquireTokenSilentAsync will return a token from the token cache and throw an exception if it cannot do so.
		AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
		result = await authContext.AcquireTokenSilentAsync(new string[] { Startup.clientId }, credential, UserIdentifier.AnyUser, mostRecentPolicy);

		...
	}
	catch (AdalException ee)
	{
		// If ADAL could not get a token silently, show the user an error indicating they might need to sign in again.
		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
	}
	...
}
```

La bibliothèque ADAL met en cache les jetons, les actualise quand ils arrivent à expiration et vous informe quand l’utilisateur doit se connecter à nouveau en levant des exceptions. Il vous suffit d’appeler `AuthenticationContext.AcquireTokenSilentAsync(...)` chaque fois que vous avez besoin d’un jeton dans votre application.

### Lecture de tâches à partir de l'API Web

Quand vous avez un jeton, vous pouvez le joindre à la requête HTTP `GET` dans l’en-tête `Authorization` pour appeler le `TaskService` en toute sécurité :

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
	...

	try
	{
		HttpClient client = new HttpClient();
		HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

		// Add the token acquired from ADAL to the request headers
		request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);
		HttpResponseMessage response = await client.SendAsync(request);

		if (response.IsSuccessStatusCode)
		{
			String responseString = await response.Content.ReadAsStringAsync();
			JArray tasks = JArray.Parse(responseString);
			ViewBag.Tasks = tasks;
			return View();
		}
		else
		{
			// If the call failed with access denied, then drop the current access token from the cache,
			// and show the user an error that indicates that they might need to sign in again.
			if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
			{
				var todoTokens = authContext.TokenCache.ReadItems().Where(a => a.Scope.Contains(Startup.clientId));
				foreach (TokenCacheItem tci in todoTokens)
					authContext.TokenCache.DeleteItem(tci);

				return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
			}
		}

		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + response.StatusCode);
	}
	catch (Exception ex)
	{
		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ex.Message);
	}
}

```

### Création et suppression de tâches dans l’API web

Suivez le même modèle quand vous envoyez des requêtes `POST` et `DELETE` à `TaskService`. Appelez `AuthenticationContext.AcquireTokenSilentAsync(...)` et joignez le jeton résultant à la requête dans l’en-tête `Authorization`. Nous avons implémenté l’action de création pour vous. Vous pouvez essayer de terminer l’action de suppression dans `TasksController.cs`.

## Déconnexion de l’utilisateur

Quand l’utilisateur se déconnecte de l’application web, vous devez effacer le cache de jetons de la bibliothèque ADAL pour supprimer les restes de la session authentifiée de l’utilisateur :

```C#
// Controllers/AccountController.cs

public void SignOut()
{
	if (Request.IsAuthenticated)
	{
		// When the user signs out, clear their token cache in the process
		string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, Startup.tenant, string.Empty, string.Empty);
		AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
		authContext.TokenCache.Clear();

		HttpContext.GetOwinContext().Authentication.SignOut(
		new AuthenticationProperties(
			new Dictionary<string, string>
			{
				{Startup.PolicyKey, ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value}
			}), OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
	}
}
```

## Exécution de l'exemple d'application

Pour terminer, générez et exécutez `TaskClient` et `TaskService`. Inscrivez-vous et connectez-vous à l’application. Créez des tâches pour l’utilisateur connecté. Déconnectez-vous et connectez-vous en tant qu’autre utilisateur. Créer des tâches pour cet utilisateur. Notez la façon dont les tâches sont stockées par utilisateur sur l’API, car l’API extrait l’identité de l’utilisateur à partir du jeton d’accès qu’il reçoit.

Pour référence, l’exemple terminé [est fourni en tant que fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->

<!---HONumber=AcomDC_0302_2016-->