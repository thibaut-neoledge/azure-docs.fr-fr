<properties
	pageTitle="Modèle d’application v2.0 | Microsoft Azure"
	description="Génération d’une API web MVC .NET qui accepte les jetons des comptes Microsoft personnels et des comptes professionnels ou scolaires."
	services="active-directory"
	documentationCenter=".net"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# Version préliminaire du modèle d’application v2.0 : Sécuriser une API web MVC

Le modèle d’application v2.0 vous permet de protéger une API web à l’aide des jetons d’accès [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), ce qui permet aux utilisateurs disposant d’un compte Microsoft personnel et de comptes professionnel ou scolaire d’accéder en toute sécurité à votre API web.

> [AZURE.NOTE]Ces informations s’appliquent à la version préliminaire publique du modèle d’application v2.0. Pour bénéficier d’instructions sur l’intégration avec le service Azure AD généralement disponible, consultez le [Guide du développeur Azure AD](active-directory-developers-guide.md).

Dans les API web ASP.NET, vous pouvez y parvenir en utilisant l’intergiciel OWIN de Microsoft inclus dans .NET Framework 4.5. Ici, nous allons utiliser OWIN pour créer une API web MVC « To Do List » qui : - Permet aux clients de créer et de lire des tâches d’une liste de tâches d’un utilisateur. - Désigne les API qui sont protégées. - Valide le fait que les appels d’API web contiennent un jeton d’accès valide.

Pour ce faire, vous devez :

1. inscrire une application auprès d’Azure AD ;
2. configurer votre application pour utiliser le pipeline d’authentification OWIN ;
3. configurer une application cliente pour appeler l’API web To Do List.

Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet). Pour suivre la procédure, vous pouvez [télécharger la structure de l’application au format .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) ou la cloner :

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

The completed app is provided at the end of this tutorial as well.


## 1. Register an App
Create a new app at [apps.dev.microsoft.com](https://apps.dev.microsoft.com), or follow these [detailed steps](active-directory-v2-app-registration.md).  Make sure to:

- Copy down the **Application Id** assigned to your app, you'll need it soon.

This visual studio solution also contains a "TodoListClient", which is a simple WPF app.  The TodoListClient is used to demonstrate how a user signs-in and how a client can issue requests to your Web API.  In this case, both the TodoListClient and the TodoListService are represented by the same app.  To configure the TodoListClient, you should also:

- Add the **Mobile** platform for your app.
- Copy down the **Redirect URI** from the portal. You must use the default value of `urn:ietf:wg:oauth:2.0:oob`.


## 2. Set up your app to use the OWIN authentication pipeline

Now that you’ve registered an app, you need to set up your app to communicate with the v2.0 endpoint in order to validate incoming requests & tokens.

-	To begin, open the solution and add the OWIN middleware NuGet packages to the TodoListService project using the Package Manager Console.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService ```

-	Ajoutez une classe de démarrage OWIN au projet TodoListService appelé `Startup.cs`. Cliquez avec le bouton droit sur le projet --> **Ajouter** --> **Nouvel élément** --> Recherchez « OWIN ». L’intergiciel OWIN appelle la méthode `Configuration(…)` lorsque votre application démarre.
-	Modifiez la déclaration de classe en `public partial class Startup`. Nous avons déjà mis en œuvre une partie de cette classe pour vous, dans un autre fichier. Dans la méthode `Configuration(…)`, appelez ConfgureAuth(...) pour configurer l’authentification pour votre application web.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-	Ouvrez le fichier `App_Start\Startup.Auth.cs` et implémentez la méthode `ConfigureAuth(…)`, qui configure l’API web pour accepter les jetons du point de terminaison v2.0.

```C#
public void ConfigureAuth(IAppBuilder app)
{
		var tvps = new TokenValidationParameters
		{
				// In this app, the TodoListClient and TodoListService
				// are represented using the same Application Id - we use
				// the Application Id to represent the audience, or the
				// intended recipient of tokens.

				ValidAudience = clientId,

				// In a real applicaiton, you might use issuer validation to
				// verify that the user's organization (if applicable) has
				// signed up for the app.  Here, we'll just turn it off.

				ValidateIssuer = false,
		};

		// Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
		// The options provided here tell the middleware about the type of tokens
		// that will be recieved, which are JWTs for the v2.0 endpoint.

		// NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
		// metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
		// OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
		// metadata document.

		app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
		{
				AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
		});
}
```

-	Vous pouvez désormais utiliser les attributs `[Authorize]` pour protéger vos contrôleurs et vos actions avec l’authentification de porteur OAuth 2.0. Décorez la classe `Controllers\TodoListController.cs` avec une balise authorize. Cela force l’utilisateur à se connecter avant d’accéder à cette page.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- Lorsqu’un appelant autorisé appelle correctement l’une des API `TodoListController`, l’action peut avoir besoin d’accéder aux informations sur l’appelant. OWIN fournit l’accès aux revendications dans le jeton porteur via l’objet `ClaimsPrincpal`.  

```C#
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

-	Enfin, ouvrez le fichier `web.config` dans la racine du projet ToDoListService, puis entrez les valeurs de configuration dans la section `<appSettings>`.
  -	`ida:Audience` est l’**ID de l’application** que vous avez entré dans le portail.

## 3\. Configurer une application cliente et exécuter le service
Avant de pouvoir voir le service Todo List en action, vous devez configurer le client Todo List afin qu’il puisse obtenir des jetons du point de terminaison v2.0 et effectuer des appels au service.

- Dans le projet TodoListClient, ouvrez `App.config` et entrez les valeurs de votre configuration dans la section `<appSettings>`.
  -	`ida:ClientId` est l’ID d’application que vous avez copié à partir du portail.
	- L’élément `ida:RedirectUri` est l’**URI de redirection** à partir du portail.

Enfin, nettoyez, générez et exécutez chaque projet. Vous disposez maintenant d’une API web MVC .NET qui accepte les jetons des comptes Microsoft personnels et des comptes professionnels ou scolaires. Connectez-vous au projet TodoListClient, puis appelez votre API web pour ajouter des tâches à la liste des tâches de l’utilisateur.

Pour référence, l’exemple terminé (sans vos valeurs de configuration) [est fourni au format .zip ici](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## Étapes suivantes
Vous pouvez à présent aborder d’autres rubriques. Par exemple :

[Appel d’une API web à partir d’une application web avec le modèle d’application v2.0 >>](active-directory-devquickstarts-webapp-webapi-dotnet.md)

Pour obtenir des ressources supplémentaires, consultez : - [Version préliminaire du modèle d’application v2.0 >>](active-directory-appmodel-v2-overview.md) - [Balise azure-active-directory StackOverflow >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

<!---HONumber=August15_HO7-->