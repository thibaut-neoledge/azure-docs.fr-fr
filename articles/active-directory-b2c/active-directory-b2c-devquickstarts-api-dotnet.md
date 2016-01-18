<properties
	pageTitle="Version préliminaire d’Azure AD B2C | Microsoft Azure"
	description="Comment créer une API web .NET à l’aide d’Azure AD B2C, la sécuriser à l’aide des jetons d’accès OAuth 2.0 pour l’authentification."
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
	ms.date="12/22/2015"
	ms.author="dastrock"/>
	
# Version préliminaire d’Azure AD B2C : Générer une API de Web .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Avec Azure AD B2C, vous pouvez sécuriser une API web à l’aide de jetons d’accès OAuth 2.0, ce qui permet aux applications clientes qui utilisent Azure AD B2C de s’authentifier auprès de l’API. Cet article explique comment créer une application WPF .NET de type « liste des tâches » incluant l’inscription d’utilisateur, la connexion et la gestion de profil. La liste de tâches de chaque utilisateur est stockée par un service de tâche, une API web qui permet aux utilisateurs authentifiés de créer et de lire les tâches figurant dans leur liste.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1\. Obtention d'un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou un client. Un répertoire est un conteneur destiné à recevoir tous vos utilisateurs, applications, groupes et autres. Si vous n’en avez pas encore, reportez-vous à [Créer un répertoire B2C](active-directory-b2c-get-started.md) avant d’aller plus loin.

## 2\. Création d'une application

Vous devez maintenant créer dans votre répertoire B2C une application fournissant à Azure AD certaines informations nécessaires pour communiquer de manière sécurisée avec votre application. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md). Assurez-vous de

- Inclure une **application web/API web** dans l’application
- Pour l’application web, utilisez l’**Uri de redirection** `https://localhost:44316/`. Il s’agit de l’emplacement par défaut du client d’application web pour cet exemple de code.
- Notez également l’**ID d’application** affecté à votre application. Vous en aurez besoin rapidement.

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\. Création de vos stratégies

Dans Azure AD B2C, chaque expérience utilisateur est définie par une [**stratégie**](active-directory-b2c-reference-policies.md). Dans ce code, le client contient trois expériences liées à l’identité : l’inscription, la connexion et la modification du profil. Vous devez créer une stratégie de chaque type, comme décrit dans l’[article de référence de stratégie](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Lors de la création de vos trois stratégies, assurez-vous de :

- Choisir **Inscription par le biais d’un ID utilisateur** ou **Inscription par le biais d’une adresse e-mail** dans le panneau des fournisseurs d’identité.
- Choisir le **nom d’affichage** et quelques autres attributs d’inscription dans votre stratégie d’inscription.
- Choisir les revendications **nom d’affichage** et **ID objet** comme revendications d’application pour chaque stratégie. Vous pouvez également choisir d’autres revendications.
- Noter le **nom** de chaque stratégie après sa création. Vous aurez besoin des noms de ces stratégies rapidement. 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Une fois vos trois stratégies créées, vous pouvez générer votre application.

## 4\. Téléchargement du code

Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Pour générer l’exemple à mesure que vous avancez, vous pouvez [télécharger une structure de projet sous la forme d’un fichier zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip) ou cloner la structure :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

L’application terminée est également [disponible en tant que fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) ou sur la branche `complete` du même référentiel.

Une fois l’exemple de code téléchargé, ouvrez le fichier Visual Studio `.sln` pour commencer. Vous remarquerez qu’il existe deux projets dans la solution : un projet `TaskWebApp` et un projet `TaskService`. Le projet `TaskWebApp` est une application de bureau web MVC avec laquelle l’utilisateur interagit. Le projet `TaskService` est l’API web du serveur principal de l’application qui stocke la liste des tâches de chaque utilisateur.

## 5\. Configuration de l’application web de la tâche

Lorsque l’utilisateur interagit avec `TaskWebApp`, le client envoie des demandes à Azure AD et reçoit des jetons de retour pouvant être utilisés pour appeler l’API web `TaskService`. Pour connecter l’utilisateur et recevoir des jetons d’accès, vous devez fournir à `TaskWebApp` des informations relatives à votre application. Dans le projet `TaskWebApp`, ouvrez le fichier `web.config` qui se trouve à la racine et remplacez les valeurs de la section `<appSettings>` :

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application Id assigned to your app by the Azure Portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure Portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Il existe également deux décorateurs `[PolicyAuthorize]` dans lesquels vous devez fournir le nom de votre stratégie de connexion. L’attribut `[PolicyAuthorize]` sert à appeler une stratégie particulière lorsque l’utilisateur tente d’accéder à une page de l’application qui requiert une authentification.

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

Si vous souhaitez apprendre comment une application web comme `TaskWebApp` utilise Azure AD B2C, découvrez notre [article Mise en route de la connexion dans une application web](active-directory-b2c-devquickstarts-web-dotnet.md).

## 6\. Sécuriser l’API

Maintenant que vous avez un client qui appelle l’API pour le compte d’utilisateurs, vous pouvez sécuriser `TaskService` à l’aide de jetons de support OAuth 2.0. Votre API peut accepter et valider les jetons à l’aide de la bibliothèque OWIN de Microsoft.

#### Installer OWIN
Commencez par installer le pipeline d’authentification OAuth OWIN :

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

#### saisissez les informations B2C
À la racine du projet `TaskService`, ouvrez le fichier `web.config` et remplacez les valeurs de la section `<appSettings>` : Ces valeurs seront utilisées dans l’API et dans la bibliothèque OWIN.

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

#### Ajouter une classe de démarrage OWIN
Ajoutez une classe de démarrage OWIN au projet `TaskService` appelé `Startup.cs`. Cliquez avec le bouton droit sur le projet --> **Ajouter** --> **Nouvel élément** --> Recherchez « OWIN ».
  

```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
	// The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

#### Configurer l’authentification OAuth 2.0
Ouvrez le fichier `App_Start\Startup.Auth.cs` et implémentez la méthode `ConfigureAuth(...)`.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
	// These values are pulled from web.config
	public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
	public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
	public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
	public static string commonPolicy = ConfigurationManager.AppSettings["ida:PolicyId"];
	private const string discoverySuffix = ".well-known/openid-configuration";

	public void ConfigureAuth(IAppBuilder app)
	{   
		TokenValidationParameters tvps = new TokenValidationParameters
		{
			// This is where you specify that your API only accepts tokens from its own clients
			ValidAudience = clientId,
		};

		app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
		{   
			// This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
			AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, "v2.0", discoverySuffix, commonPolicy)))
		});
	}
}
```

#### Sécuriser le contrôleur de tâche
Maintenant que l’application est configurée pour utiliser l’authentification OAuth 2.0, tout ce que vous avez à faire pour sécuriser votre API web est d’ajouter une balise `[Authorize]` au contrôleur de tâche. Il s’agit du contrôleur sur lequel toutes les manipulations de liste des tâches ont lieu. Donc, nous allons sécuriser l’ensemble du contrôleur au niveau de la classe. Vous pouvez également ajouter la balise `[Authorize]` pour les actions individuelles et obtenir ainsi un contrôle plus précis.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
	...
}
```

#### Obtenir des informations utilisateur à partir du jeton
Le `TaskController` stocke des tâches dans une base de données où chaque tâche a un utilisateur associé « propriétaire » de la tâche. Le propriétaire est identifié par l’**ID d’objet** de l’utilisateur(c’est pourquoi vous devez ajouter l’ID d’objet en tant que revendication d’application dans toutes vous stratégies) :

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
	string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
	return userTasks;
}
```

## 7\. Exécuter l’application exemple

Pour terminer, générez et exécutez `TaskWebApp` et `TaskService`. Inscrivez-vous à l’application à l’aide d’une adresse de messagerie ou d’un nom d’utilisateur. Créez des tâches sur la liste des tâches de l’utilisateur et notez la façon dont elles restent dans l’API, même une fois que vous avez arrêté et redémarré le client.

## 8\. Modifier vos stratégies

Maintenant que vous avez une API sécurisée avec Azure AD B2C, vous pouvez jouer avec les stratégies de votre application et afficher le résultat (ou l’absence de résultat) sur l’API. Vous pouvez 
<!--add **identity providers**
to the policies, allowing you users to sign into the Task Client using social accounts.  You can also manipuler les **revendications d’application** dans les stratégies et modifier les informations utilisateur qui sont disponibles dans l’API web. Toutes les revendications que vous ajoutez seront disponibles pour votre API web MVC .NET dans l’objet `ClaimsPrincipal`, comme décrit ci-dessus.
-->

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=AcomDC_0107_2016-->