---
title: Azure AD B2C | Microsoft Docs
description: Comment créer une API web .NET à l’aide d’Azure Active Directory B2C, la sécuriser à l’aide des jetons d’accès OAuth 2.0 pour l’authentification.
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: msmbaldwin
editor: ''

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/22/2016
ms.author: dastrock

---
# Azure Active Directory B2C : générer une API web .NET
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Avec Azure Active Directory (Azure AD) B2C, vous pouvez sécuriser une API web à l’aide de jetons d’accès OAuth 2.0, ce qui permet aux applications clientes qui utilisent Azure AD B2C de s’authentifier auprès de l’API. Cet article vous indique comment créer une API .NET Model-View-Controller (MVC) « liste de tâches » permettant aux utilisateurs d’exécuter des actions de création, lecture, mise à jour et suppression (CRUD) sur les tâches. L’API web est sécurisée à l’aide d’Azure AD B2C et autorise uniquement les utilisateurs authentifiés à gérer leur liste de tâches.

## Créer un répertoire Azure AD B2C
Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou un client. Un répertoire est un conteneur destiné à recevoir tous vos utilisateurs, applications, groupes, etc. Si vous n’en possédez pas déjà un, [créez un annuaire B2C](active-directory-b2c-get-started.md) avant d’aller plus loin dans ce guide.

## Création d'une application
Vous devez maintenant créer dans votre répertoire B2C une application fournissant à Azure AD certaines informations nécessaires pour communiquer de manière sécurisée avec votre application. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md). Veillez à effectuer les opérations suivantes :

* Incluez une **application web** ou une **API web** dans l’application.
* Utilisez **l’URI de redirection** `https://localhost:44316/` pour l’application web. Il s’agit de l’emplacement par défaut du client d’application web pour cet exemple de code.
* Copiez **l’ID de l’application** affecté à votre application. Vous en aurez besoin ultérieurement.
  
  [!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Création de vos stratégies
Dans Azure AD B2C, chaque expérience utilisateur est définie par une [stratégie](active-directory-b2c-reference-policies.md). Dans cet exemple de code, le client contient trois expériences liées à l’identité : l’inscription, la connexion et la modification du profil. Vous devez créer une stratégie pour chaque type, comme décrit dans [l’article de référence sur les stratégies](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Lors de la création de vos trois stratégies, assurez-vous de :

* Choisir **Inscription par le biais d’un ID utilisateur** ou **Inscription par le biais d’une adresse e-mail** dans le panneau des fournisseurs d’identité.
* Choisir **Nom d’affichage** et d’autres attributs d’inscription dans votre stratégie d’inscription.
* Choisir les revendications **Nom d’affichage** et **ID d’objet** comme revendications d’application pour chaque stratégie. Vous pouvez aussi choisir d'autres revendications.
* Copiez le **nom** de chaque stratégie après sa création. Vous aurez besoin des noms de ces stratégies ultérieurement.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Une fois vos trois stratégies créées, vous pouvez générer votre application.

## Téléchargement du code
Le code associé à ce didacticiel [est stocké sur GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Pour générer l’exemple à mesure que vous avancez, vous pouvez [télécharger une structure de projet sous la forme d’un fichier zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip). Vous pouvez également cloner la structure :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

L’application terminée est également [disponible en tant que fichier zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) ou sur la branche `complete` du même référentiel.

Une fois l’exemple de code téléchargé, ouvrez le fichier .sln Visual Studio pour commencer. Le fichier solution contient deux projets : `TaskWebApp` et `TaskService`. `TaskWebApp` est une application web MVC avec laquelle l’utilisateur interagit. `TaskService` est l’API web du serveur principal de l’application qui stocke la liste de tâches de chaque utilisateur.

## Configuration de l’application web de la tâche
Quand un utilisateur interagit avec `TaskWebApp`, le client envoie des demandes à Azure AD et reçoit en retour des jetons pouvant être utilisés pour appeler l’API web `TaskService`. Pour connecter l’utilisateur et obtenir des jetons, vous devez fournir à `TaskWebApp` des informations concernant votre application. Dans le projet `TaskWebApp`, ouvrez le fichier `web.config` qui se trouve à la racine du projet et remplacez les valeurs de la section `<appSettings>`. Vous pouvez conserver les valeurs `AadInstance`, `RedirectUri` et `TaskServiceUrl` en l’état.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
  </appSettings>
```

Cet article ne couvre pas la génération du client `TaskWebApp`. Pour savoir comment générer une application web à l’aide d’Azure AD B2C, consultez [notre didacticiel sur les applications web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Sécuriser l’API
Lorsque l’un de vos clients appelle l’API pour le compte d’utilisateurs, vous pouvez sécuriser `TaskService` à l’aide de jetons du porteur OAuth 2.0. Votre API peut accepter et valider les jetons à l’aide de la bibliothèque OWIN (Open Web Interface for .NET) de Microsoft.

### Installer OWIN
Commencez par installer le pipeline d’authentification OAuth OWIN :

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### saisissez les informations B2C
À la racine du projet `TaskService`, ouvrez le fichier `web.config` et remplacez les valeurs de la section `<appSettings>` : Ces valeurs seront utilisées dans l’API et dans la bibliothèque OWIN. Vous pouvez conserver la valeur `AadInstance` en l’état.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
```

### Ajouter une classe de démarrage OWIN
Ajoutez une classe de démarrage OWIN au projet `TaskService` appelé `Startup.cs`. Cliquez avec le bouton droit sur le projet, sélectionnez **Ajouter** et **Nouvel élément**, puis recherchez OWIN.

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

### Configurer l’authentification OAuth 2.0
Ouvrez le fichier `App_Start\Startup.Auth.cs` et implémentez la méthode `ConfigureAuth(...)`.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // These values are pulled from web.config
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string signUpPolicy = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string signInPolicy = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string editProfilePolicy = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {   
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signUpPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signInPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(editProfilePolicy));
    }

    public OAuthBearerAuthenticationOptions CreateBearerOptionsFromPolicy(string policy)
    {
        TokenValidationParameters tvps = new TokenValidationParameters
        {
            // This is where you specify that your API only accepts tokens from its own clients
            ValidAudience = clientId,
            AuthenticationType = policy,
        };

        return new OAuthBearerAuthenticationOptions
        {
            // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
            AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, policy))),
        };
    }
}
```

### Sécuriser le contrôleur de tâche
Une fois que l’application est configurée pour utiliser l’authentification OAuth 2.0, vous pouvez sécuriser votre API web en ajoutant une balise `[Authorize]` au contrôleur de tâches. Comme il s’agit du contrôleur sur lequel toutes les manipulations de liste des tâches ont lieu, vous devez sécuriser l’ensemble du contrôleur au niveau de la classe. Vous pouvez également ajouter la balise `[Authorize]` à des actions individuelles afin d’obtenir un contrôle plus précis.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### Obtenir des informations utilisateur à partir du jeton
Le `TasksController` stocke des tâches dans une base de données où chaque tâche est associée à un utilisateur « propriétaire » de la tâche. Le propriétaire est identifié par **l’ID objet** de l’utilisateur (c’est pourquoi vous devez ajouter l’ID d’objet en tant que revendication d’application dans toutes vous stratégies).

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

## Exécution de l'exemple d'application
Pour finir, générez et exécutez `TaskWebApp` et `TaskService`. Inscrivez-vous à l’application à l’aide d’une adresse de messagerie ou d’un nom d’utilisateur. Créez des tâches sur la liste des tâches de l’utilisateur et notez la façon dont elles restent dans l’API, même une fois que vous avez arrêté et redémarré le client.

## Modifier vos stratégies
Une fois que vous avez sécurisé une API avec Azure AD B2C, vous pouvez tester les stratégies de votre application et afficher le résultat (ou l’absence de résultat) sur l’API. Vous pouvez manipuler les revendications d’application dans les stratégies et modifier les informations utilisateur qui sont disponibles dans l’API web. Toutes les revendications que vous ajoutez seront accessibles par votre API web .NET MVC dans l’objet `ClaimsPrincipal`, comme décrit plus haut dans cet article.

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->

<!---HONumber=AcomDC_0727_2016-->