<properties
   pageTitle="Authentification dans les applications mutualisées | Microsoft Azure"
   description="Comment une application mutualisée peut authentifier les utilisateurs à partir d’Azure AD"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Authentification dans les applications mutualisées

Cet article fait [partie d’une série](guidance-multitenant-identity.md), qui s’accompagne d’un [exemple d’application].

Cet article explique comment une application mutualisée peut authentifier les utilisateurs d’Azure Active Directory (Azure AD) qui font appel à l’authentification OpenID Connect (OIDC).

## Vue d'ensemble

Notre [implémentation de référence](guidance-multitenant-identity-tailspin.md) est une application ASP.NET Core 1.0. L’application utilise la solution middleware OpenID Connect intégrée pour exécuter le flux d’authentification OIDC. Le schéma suivant illustre globalement la succession d’événements consécutifs à une connexion de l’utilisateur.

![Flux d’authentification](media/guidance-multitenant-identity/auth-flow.png)

1.	L’utilisateur clique sur le bouton de connexion dans l’application. Cette action est gérée par un contrôleur MVC.
2.	Le contrôleur MVC renvoie une action **ChallengeResult**.
3.	Le middleware intercepte la **ChallengeResult** et génère une réponse 302 qui redirige l’utilisateur vers la page de connexion d’Azure AD.
4.	L’utilisateur s’authentifie auprès d’Azure AD.
5.	Azure AD envoie un jeton d’ID à l’application.
6.	Le middleware valide le jeton d’ID. À ce stade, l’utilisateur est maintenant authentifié au sein de l’application.
7.	Le middleware redirige l’utilisateur vers l’application.

## Inscription de l’application auprès d’Azure AD

Pour activer OpenID Connect, le fournisseur SaaS inscrit l’application à l’intérieur de son propre client Azure AD.

Pour inscrire l’application, suivez les étapes de la section « Enregistrement d’une nouvelle application dans le portail Azure Classic » de l’article [Intégration d’applications dans Azure Active Directory](../active-directory/active-directory-integrating-applications.md).

Sur la page **Configurer** :

-	Notez l’ID client.
-	Sous **Application mutualisée**, sélectionnez **Oui**.
-	Définissez **URL de réponse** sur une URL vers laquelle Azure AD enverra la réponse d’authentification. Vous pouvez utiliser l’URL de base de votre application.
  -	Remarque : vous pouvez utiliser n’importe quel chemin d’URL à condition que le nom d’hôte corresponde à l’application que vous avez déployée.
  -	Vous pouvez définir plusieurs URL de réponse. Pendant le développement, vous pouvez utiliser une adresse `localhost` pour exécuter l’application en local.
-	Générez une clé secrète client : sous **Clés**, cliquez sur la liste déroulante qui indique **Sélectionnez une durée** et choisissez 1 ou 2 ans. La clé sera visible dès que vous cliquez sur **Enregistrer**. Veillez à copier la valeur, car elle n’apparaîtra plus lorsque vous rechargerez la page de configuration.

## Configuration du middleware d’authentification

Cette section décrit comment configurer le middleware d’authentification dans ASP.NET Core 1.0 pour une authentification mutualisée avec OpenID Connect.

Dans votre classe de démarrage, ajoutez le middleware OpenID Connect :

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    options.AutomaticAuthenticate = true;
    options.AutomaticChallenge = true;
    options.ClientId = [client ID];
    options.Authority = "https://login.microsoftonline.com/common/";
    options.CallbackPath = [callback path];
    options.PostLogoutRedirectUri = [application URI];
    options.SignInScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.TokenValidationParameters = new TokenValidationParameters
    {
        ValidateIssuer = false
    };
    options.Events = [event callbacks];
});
```

> [AZURE.NOTE] Voir [Startup.cs](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs).

Pour plus d’informations sur la classe de démarrage, consultez la page [Application Startup](https://docs.asp.net/en/latest/fundamentals/startup.html) (Démarrage d’application) de la documentation d’ASP.NET Core 1.0.

Définissez les options de middleware suivantes :

- **ClientId**. ID client de l’application que vous avez obtenu lors de l’inscription de l’application dans Azure AD.
- **Authority**. Pour une application mutualisée, définissez ce paramètre sur `https://login.microsoftonline.com/common/`. Il s’agit de l’URL du point de terminaison commun d’Azure AD, qui permet aux utilisateurs de n’importe quel client Azure AD de se connecter. Pour plus d’informations sur le point de terminaison commun, consultez [ce billet de blog](http://www.cloudidentity.com/blog/2014/08/26/the-common-endpoint-walks-like-a-tenant-talks-like-a-tenant-but-is-not-a-tenant/).
- Dans **TokenValidationParameters**, définissez **ValidateIssuer** sur false. Cela signifie que l’application est chargée de valider la valeur de l’émetteur dans le jeton d’ID (le middleware valide toujours le jeton lui-même). Pour plus d’informations sur la validation de l’émetteur, consultez la rubrique [Issuer Validation](guidance-multitenant-identity-claims.md#issuer-validation) (Validation de l’émetteur).
- **CallbackPath**. Définissez ce paramètre sur le chemin d’accès de l’URL de réponse que vous avez inscrite dans Azure AD. Par exemple, si l’URL de réponse est `http://contoso.com/aadsignin`, la valeur du paramètre **CallbackPath** doit être `aadsignin`. Si vous ne définissez pas cette option, la valeur par défaut est `signin-oidc`.
- **PostLogoutRedirectUri**. Spécifiez une URL de redirection des utilisateurs après leur déconnexion. Vous devez choisir une page qui autorise les demandes anonymes (en général, la page d’accueil).
- **SignInScheme**. Définissez cette propriété sur `CookieAuthenticationDefaults.AuthenticationScheme`. Ce paramètre signifie qu’une fois que l’utilisateur est authentifié, les revendications d’utilisateur sont stockées localement dans un cookie. Ce cookie représente la façon dont l’utilisateur reste connecté pendant la session de navigateur.
- **Événements :** Rappels d’événement ; consultez la rubrique [Événements d’authentification](#authentication-events).

Ajoutez également le middleware Cookie Authentification au pipeline. Ce middleware est chargé d’écrire les revendications d’utilisateur sur un cookie et de lire ensuite le cookie au cours des prochains chargements de la page.

```csharp
app.UseCookieAuthentication(options =>
{
    options.AutomaticAuthenticate = true;
    options.AutomaticChallenge = true;
    options.AccessDeniedPath = "/Home/Forbidden";
});
```

## Démarrage du flux d’authentification

Pour démarrer le flux d’authentification dans ASP.NET MVC, vous devez renvoyer un **ChallengeResult** à partir du contrôleur :

```csharp
[AllowAnonymous]
public IActionResult SignIn()
{
    return new ChallengeResult(
        OpenIdConnectDefaults.AuthenticationScheme,
        new AuthenticationProperties
        {
            IsPersistent = true,
            RedirectUri = Url.Action("SignInCallback", "Account")
        });
}
```

Le middleware renvoie alors une réponse 302 (trouvé) qui redirige vers le point de terminaison de l’authentification.

## Ouverture de session utilisateur

Comme nous l’avons indiqué plus haut, lors de la première connexion de l’utilisateur, le middleware Cookie Authentication écrit les revendications d’utilisateur dans un cookie. Les requêtes HTTP sont ensuite authentifiées grâce à la lecture du cookie.

Par défaut, ce middleware écrit un [cookie de session][session-cookie] qui est automatiquement supprimé dès lors que l’utilisateur ferme le navigateur. À sa prochaine visite, l’utilisateur devra donc de nouveau se connecter. Cependant, si vous définissez le paramètre **IsPersistent** sur true dans le **ChallengeResult**, le middleware écrit un cookie persistant qui permet à l’utilisateur de rester connecté après la fermeture du navigateur. Vous pouvez configurer le délai d’expiration du cookie ; pour cela, consultez la page [Controlling cookie options][cookie-options]. Les cookies persistants sont plus pratiques pour l’utilisateur, mais ils peuvent être inadaptés à certaines applications (par exemple, les applications bancaires) pour lesquelles il est souhaitable de demander à l’utilisateur de se connecter à chaque fois.

## À propos du middleware OpenID Connect

Le middleware OpenID Connect dans ASP.NET masque la plupart des détails du protocole. Cette section contient des remarques relatives à l’implémentation qui peuvent vous aider à comprendre le flux du protocole.

Commençons par examiner le flux d’authentification du point de vue d’ASP.NET (en ignorant les détails du flux de protocole OIDC entre l’application et Azure AD). Le schéma qui suit présente ce processus.

![Flux de connexion](media/guidance-multitenant-identity/sign-in-flow.png)

Dans ce schéma, deux contrôleurs MVC sont référencés. Le contrôleur de compte gère les demandes de connexion et le contrôleur d’accueil fournit la page d’accueil.

Voici comment se déroule le processus d’authentification :

1. L’utilisateur clique sur le bouton de connexion et le navigateur envoie une demande GET. Par exemple : `GET /Account/SignIn/`.
2. Le contrôleur de compte renvoie un `ChallengeResult`.
3. Le middleware OIDC renvoie une réponse HTTP 302 qui redirige l’utilisateur vers Azure AD.
4. Le navigateur envoie la demande d’authentification à Azure AD.
5. L’utilisateur se connecte à Azure AD, qui à son tour renvoie une réponse d’authentification.
6. Le middleware OIDC crée une entité de revendications et la transmet au middleware Cookie Authentication.
7. Ce middleware sérialise l’entité de revendications et définit un cookie.
8. Le middleware OIDC force une redirection vers l’URL de rappel de l’application.
10. Le navigateur suit la redirection en envoyant le cookie dans la demande.
11. Le middleware de cookie désérialise le cookie dans une entité de revendications et définit le paramètre `HttpContext.User` sur une valeur correspondant à l’entité de revendications. La demande est acheminée vers un contrôleur MVC.

### Ticket d’authentification

Si l’authentification réussit, le middleware OIDC crée un ticket d’authentification, qui contient une entité de revendications regroupant les revendications de l’utilisateur. Vous pouvez accéder au ticket depuis l’événement **AuthenticationValidated** ou **TicketReceived**.

> [AZURE.NOTE] Tant que le flux d’authentification n’est pas terminé, `HttpContext.User` renferme toujours une entité anonyme, _différente_ de l’utilisateur authentifié. L’entité anonyme comporte un ensemble de revendications vide. Une fois l’authentification effectuée et l’utilisateur redirigé par l’application, le middleware cookie désérialise le cookie d’authentification et définit le paramètre `HttpContext.User` sur une entité de revendications représentant l’utilisateur authentifié.

### Événements d’authentification

Pendant le processus d’authentification, le middleware OpenID Connect déclenche une série d’événements :

- **RedirectToAuthenticationEndpoint**. Appelé juste avant que le middleware ne redirige vers le point de terminaison de l’authentification. Vous pouvez utiliser cet événement pour modifier l’URL de redirection (par exemple, pour ajouter des paramètres de requête). Consultez l’exemple décrit dans la rubrique [Adding the admin consent prompt](guidance-multitenant-identity-signup.md#adding-the-admin-consent-prompt) (Ajout de l’invite de consentement administrateur).

- **AuthorizationResponseReceived**. Appelé après que le middleware reçoit la réponse d’authentification du fournisseur d’identité (IDP), mais avant que le middleware ne valide la réponse.

- **AuthorizationCodeReceived**. Appelé avec le code d’autorisation.

- **TokenResponseReceived**. Appelé après que le middleware obtient un jeton d’accès auprès du fournisseur d’identité. S’applique uniquement au flux de code d’autorisation.

- **AuthenticationValidated**. Appelé après validation du jeton d’ID par le middleware. À ce stade, l’application comporte un ensemble de revendications validées concernant l’utilisateur. Vous pouvez utiliser cet événement pour effectuer une validation supplémentaire sur les revendications, ou pour transformer les revendications. Consultez la page [Working with claims](guidance-multitenant-identity-claims.md) (Utilisation des revendications).

- **UserInformationReceived**. Appelé si le middleware obtient le profil utilisateur à partir du point de terminaison des informations utilisateur. S’applique uniquement au flux de code d’autorisation et uniquement lorsque `GetClaimsFromUserInfoEndpoint = true` dans les options du middleware.

- **TicketReceived**. Appelé lorsque l’authentification est effectuée. Il s’agit du dernier événement, en supposant que l’authentification aboutisse. Une fois cet événement traité, l’utilisateur est connecté à l’application.

- **AuthenticationFailed**. Appelé en cas d’échec de l’authentification. Utilisez cet événement pour gérer les échecs d’authentification (par exemple, en redirigeant l’utilisateur vers une page d’erreur).

Pour fournir des rappels pour ces événements, définissez l’option **Events** sur le middleware. Il existe deux différentes manières de déclarer les gestionnaires d’événements : à la volée dans les expressions lambda, ou dans une classe dérivée de **OpenIdConnectEvents**.

À la volée avec les expressions lambda :

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    // Other options not shown.

    options.Events = new OpenIdConnectEvents
    {
        OnTicketReceived = (context) =>
        {
             // Handle event
             return Task.FromResult(0);
        },
        // other events
    }
});
```

Classe dérivée de **OpenIdConnectEvents** :

```csharp
public class SurveyAuthenticationEvents : OpenIdConnectEvents
{
    public override Task TicketReceived(TicketReceivedContext context)
    {
        // Handle event
        return base.TicketReceived(context);
    }
    // other events
}

// In Startup.cs:
app.UseOpenIdConnectAuthentication(options =>
{
    // Other options not shown.

    options.Events = new SurveyAuthenticationEvents();
});
```

La deuxième approche est recommandée si vos rappels d’événement ont une logique substantielle, afin qu’ils ne viennent pas alourdir votre classe de démarrage. Notre implémentation de référence utilise cette approche ; consultez la page [SurveyAuthenticationEvents.cs](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/SurveyAuthenticationEvents.cs).

### Points de terminaison OpenID Connect

Azure AD prend en charge [OpenID Connect Discovery](https://openid.net/specs/openid-connect-discovery-1_0.html), où le fournisseur d’identité (IDP) renvoie un document de métadonnées JSON à partir d’un [point de terminaison connu](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig). Le document de métadonnées contient des informations telles que :

-	L’URL du point de terminaison d’autorisation. Il s’agit du point de redirection de l’application pour l’authentification de l’utilisateur.
-	L’URL du point de terminaison de « fin de session », où l’application se rend pour déconnecter l’utilisateur.
-	L’URL permettant d’obtenir les clés de signature utilisées par le client pour valider les jetons OIDC obtenus à partir du fournisseur d’identité.

Par défaut, le middleware OIDC sait comment récupérer ces métadonnées. Définissez l’option **Authority** dans le middleware pour que le middleware construise l’URL des métadonnées (vous pouvez remplacer l’URL des métadonnées en définissant l’option **MetadataAddress**).

### Flux OpenID Connect

Par défaut, le middleware OIDC utilise un flux hybride avec un mode de réponse POST de type formulaire.

-	Un _flux hybride_ signifie que le client peut obtenir un jeton d’ID et un code d’autorisation dans le même aller-retour vers le serveur d’autorisation.
-	Le _mode de réponse POST de type formulaire_ signifie que le serveur d’autorisation utilise une demande HTTP POST pour envoyer le jeton d’ID et le code d’autorisation à l’application. Les valeurs utilisent la méthode form-urlencode (type de contenu = "application/x-www-form-urlencoded").

Lorsque le middleware OIDC redirige vers le point de terminaison d’autorisation, l’URL de redirection inclut tous les paramètres de chaîne de requête requis par OIDC. Pour un flux hybride :

-	client\_id. Cette valeur est définie dans l’option **ClientId**
-	scope = "openid profile", ce qui signifie qu’il s’agit bien de la requête OIDC et que nous voulons le profil de l’utilisateur.
-	response\_type = "code id\_token". Spécifie le flux hybride.
-	response\_mode = "form\_post". Spécifie la réponse POST du formulaire.

Pour spécifier un autre flux, définissez la propriété **ResponseType** sur les options. Par exemple :

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    options.ResponseType = "code"; // Authorization code flow

    // Other options
}
```

[cookie-options]: https://docs.asp.net/en/latest/security/authentication/cookie.html#controlling-cookie-options
[session-cookie]: https://en.wikipedia.org/wiki/HTTP_cookie#Session_cookie
[exemple d’application]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->