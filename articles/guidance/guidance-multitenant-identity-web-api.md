<properties
   pageTitle="Sécurisation d’une API web de serveur principal dans une application multi-locataire | Microsoft Azure"
   description="Comment sécuriser une API web de serveur principal"
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

# Sécurisation d’une API web de serveur principal dans une application multi-locataire

Cet article fait [partie d’une série]. Un [exemple d’application] accompagne également cette série.

L’application [Surveys de Tailspin] utilise une API web de serveur principal pour gérer les opérations CRUD sur les enquêtes. Par exemple, quand un utilisateur clique sur « My Surveys », l’application web envoie une requête HTTP à l’API web :

```
GET /users/{userId}/surveys
```

L’API web renvoie un objet JSON :

```
{
  "Published":[],
  "Own":[
    {"Id":1,"Title":"Survey 1"},
    {"Id":3,"Title":"Survey 3"},
    ],
  "Contribute": [{"Id":8,"Title":"My survey"}]
}
```

Comme l’API web n’autorise pas les requêtes anonymes, l’application web doit s’authentifier à l’aide des jetons du porteur OAuth 2.

> [AZURE.NOTE] Il s’agit d’un scénario serveur à serveur. L’application ne fait aucun appel AJAX à l’API à partir du client navigateur.

Il existe deux approches principales possibles :

- Identité d’utilisateur délégué. L’application web s’authentifie avec l’identité de l’utilisateur.
- Identité d’application. L’application web s’authentifie avec son ID client, à l’aide du flux d’informations d’identification du client OAuth2.

L’application de Tailspin implémente l’identité d’utilisateur délégué. Voici les principales différences :

**Identité d’utilisateur délégué**

- Le jeton du porteur envoyé à l’API web contient l’identité de l’utilisateur.
- L’API web prend des décisions d’autorisation basées sur l’identité de l’utilisateur.
- L’application web doit gérer les erreurs 403 (Refusé) à partir de l’API web, si l’utilisateur n’est pas autorisé à effectuer une action.
- En général, l’application web prend toujours des décisions d’autorisation qui affectent l’interface utilisateur, comme l’affichage ou le masquage des éléments d’interface utilisateur).
- Avec cette approche, l’API web peut potentiellement être utilisée par des clients non approuvés, par exemple une application JavaScript ou d’une application cliente native.

**Identité d’application**

- L’API web n’obtient pas d’informations sur l’utilisateur.
- L’API web ne peut déclarer aucune autorisation basée sur l’identité de l’utilisateur. Toutes les décisions d’autorisation sont prises par l’application web.  
- L’API web ne peut pas être utilisée par un client non approuvé (application JavaScript ou cliente native).
- Cette approche peut être un peu plus simple à implémenter, car il n’existe aucune logique d’autorisation dans l’API web.

Dans les deux approches, l’application web doit obtenir un jeton d’accès, qui correspond aux informations d’identification nécessaires pour appeler l’API web.

- Pour l’identité d’utilisateur délégué, le jeton doit provenir d’un fournisseur d’identité, qui peut émettre un jeton pour le compte de l’utilisateur.

- Pour les informations d’identification du client, une application peut obtenir le jeton à partir du fournisseur d’identité ou héberger son propre serveur de jetons. (Mais n’écrivez pas un serveur de jetons à partir de zéro : utilisez une infrastructure bien testée comme [IdentityServer3].) Si vous vous authentifiez auprès Azure AD, il est fortement recommandé d’obtenir le jeton d’accès à partir d’Azure AD, même avec les flux d’informations d’identification du client.

Le reste de cet article part du principe que l’application s’authentifie auprès d’Azure AD.

![Obtention du jeton d’accès](media/guidance-multitenant-identity/access-token.png)

## Inscription de l’API web dans Azure AD

Pour qu’Azure AD émette un jeton du porteur pour l’API web, vous devez configurer certains paramètres dans Azure AD.

1. [Inscription de l’API web dans Azure AD].

2. Ajoutez l’ID client de l’application web au manifeste d’application de l’API web, dans la propriété `knownClientApplications`. Consultez la page [Update the application manifests].

3. [Donnez à l’application web l’autorisation d’appeler l’API web].

  Dans le portail de gestion Azure, vous pouvez définir deux types d’autorisations : les « autorisations d’application » pour l’identité d’application (flux d’informations d’identification du client) ou les « autorisations déléguées » pour l’identité d’utilisateur délégué.

  ![Autorisations déléguées](media/guidance-multitenant-identity/delegated-permissions.png)

## Obtention d’un jeton d’accès

Avant d’appeler l’API web, l’application web obtient un jeton d’accès à partir d’Azure AD. Dans une application .NET, utilisez la [bibliothèque d’authentification Azure AD (ADAL) pour .NET][ADAL].

Dans le flux du code d’autorisation OAuth 2, l’application échange un code d’autorisation contre un jeton d’accès. Le code suivant utilise la bibliothèque ADAL pour obtenir le jeton d’accès. Ce code est appelé pendant l’événement `AuthorizationCodeReceived`.

```csharp
// The OpenID Connect middleware sends this event when it gets the authorization code.   
public override async Task AuthorizationCodeReceived(AuthorizationCodeReceivedContext context)
{
    string authorizationCode = context.ProtocolMessage.Code;
    string authority = "https://login.microsoftonline.com/" + tenantID
    string resourceID = "https://tailspin.onmicrosoft.com/surveys.webapi" // App ID URI
    ClientCredential credential = new ClientCredential(clientId, clientSecret);

    AuthenticationContext authContext = new AuthenticationContext(authority, tokenCache);
    AuthenticationResult authResult = await authContext.AcquireTokenByAuthorizationCodeAsync(
        authorizationCode, new Uri(redirectUri), credential, resourceID);

    // If successful, the token is in authResult.AccessToken
}
```

Voici les différents paramètres qui sont nécessaires :

- `authority`. Dérivé de l’ID de locataire de l’utilisateur connecté (pas de l’ID de locataire du fournisseur SaaS).  
- `authorizationCode`. Code d’authentification que vous avez obtenu du fournisseur d’identité.
- `clientId`. ID client de l’application web.
- `clientSecret`. Clé secrète client de l’application web.
- `redirectUri`. URI de redirection que vous définissez pour OpenID Connect. Il s’agit de l’endroit où le fournisseur d’identité rappelle avec le jeton.
- `resourceID`. URI d’ID d’application de l’API web, que vous avez créé quand vous avez inscrit l’API web dans Azure AD.
- `tokenCache`. Objet qui met en cache les jetons d’accès. Consultez la page [Token caching].

Si `AcquireTokenByAuthorizationCodeAsync` réussit, la bibliothèque ADAL met en cache le jeton. Vous pouvez obtenir ultérieurement le jeton à partir du cache en appelant AcquireTokenSilentAsync :

```csharp
AuthenticationContext authContext = new AuthenticationContext(authority, tokenCache);
var result = await authContext.AcquireTokenSilentAsync(resourceID, credential, new UserIdentifier(userId, UserIdentifierType.UniqueId));
```

où `userId` est l’ID objet de l’utilisateur, qui se trouve dans la revendication `http://schemas.microsoft.com/identity/claims/objectidentifier`.

## Utilisation du jeton d’accès pour appeler l’API web.

Une fois que vous avez le jeton, envoyez-le dans l’en-tête d’autorisation des requêtes HTTP à l’API web.

```
Authorization: Bearer xxxxxxxxxx
```

La méthode d’extension suivante de l’application Surveys définit l’en-tête d’autorisation sur une requête HTTP, à l’aide de la classe **HttpClient**.

```csharp
public static async Task<HttpResponseMessage> SendRequestWithBearerTokenAsync(this HttpClient httpClient, HttpMethod method, string path, object requestBody, string accessToken, CancellationToken ct)
{
    var request = new HttpRequestMessage(method, path);
    if (requestBody != null)
    {
        var json = JsonConvert.SerializeObject(requestBody, Formatting.None);
        var content = new StringContent(json, Encoding.UTF8, "application/json");
        request.Content = content;
    }

    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
    request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

    var response = await httpClient.SendAsync(request, ct);
    return response;
}
```

> [AZURE.NOTE] Consultez la page [HttpClientExtensions.cs].

## Authentification dans l’API web

L’API web doit authentifier le jeton du porteur. Dans ASP.NET Core 1.0, vous pouvez utiliser le package [Microsoft.AspNet.Authentication.JwtBearer][JwtBearer]. Ce package fournit un middleware qui permet à l’application de recevoir des jetons de porteur OpenID Connect.

Inscrivez le middleware dans la classe `Startup` de votre API web.

```csharp
app.UseJwtBearerAuthentication(options =>
{
    options.Audience = "[app ID URI]";
    options.Authority = "https://login.microsoftonline.com/common/";
    options.TokenValidationParameters = new TokenValidationParameters
    {
        //Instead of validating against a fixed set of known issuers, we perform custom multi-tenant validation logic
        ValidateIssuer = false,
    };
    options.Events = new SurveysJwtBearerEvents();
});
```

> [AZURE.NOTE] Consultez la page [Startup.cs].

- **Audience**. Affectez à ce paramètre l’URL d’ID d’application de l’API web que vous avez créée quand vous avez inscrit l’API web dans Azure AD.
- **Authority**. Pour une application multi-locataire, affectez à ce paramètre la valeur https://login.microsoftonline.com/common/``.
- **TokenValidationParameters**. Pour une application multi-locataire, affectez au paramètre **ValidateIssuer** la valeur false. Cela signifie que l’application valide l’émetteur.
- **Events** est une classe qui dérive de **JwtBearerEvents**.

### Validation de l’émetteur

Validez l’émetteur du jeton dans l’événement **JwtBearerEvents.ValidatedToken**. L’émetteur est envoyé dans la revendication « iss ».

Dans l’application Surveys, l’API web ne gère pas [l’inscription du locataire]. Par conséquent, elle vérifie uniquement si l’émetteur se trouve déjà dans la base de données de l’application. Si ce n’est pas le cas, elle lève une exception, ce qui provoque l’échec de l’authentification.

```csharp
public override async Task ValidatedToken(ValidatedTokenContext context)
{
    var principal = context.AuthenticationTicket.Principal;
    var tenantManager = context.HttpContext.RequestServices.GetService<TenantManager>();
    var userManager = context.HttpContext.RequestServices.GetService<UserManager>();
    var issuerValue = principal.GetIssuerValue();
    var tenant = await tenantManager.FindByIssuerValueAsync(issuerValue);

    if (tenant == null)
    {
        // the caller was not from a trusted issuer - throw to block the authentication flow
        throw new SecurityTokenValidationException();
    }
}
```

> [AZURE.NOTE] Consultez la page [SurveysJwtBearerEvents.cs].

Vous pouvez également utiliser l’événement **ValidatedToken** pour effectuer une [transformation des revendications]. N’oubliez pas que les revendications proviennent directement d’Azure AD. Par conséquent, si l’application web a effectué des transformations de revendications, elles ne sont pas reflétées dans le jeton du porteur que l’API web reçoit.

## Autorisation

Pour obtenir une description générale de l’autorisation, consultez la page [Authorization]. Pour une API web, la principale différence est que le middleware JwtBearer gère les réponses des autorisations.

Par exemple, pour limiter une action de contrôleur aux utilisateurs authentifiés :

```csharp
[Authorize(ActiveAuthenticationSchemes = JwtBearerDefaults.AuthenticationScheme)]
```


Cela renvoie un code d’état 401 si l’utilisateur n’est pas authentifié.

Pour limiter une action de contrôleur par stratégie d’autorisation :

```csharp
[Authorize(Policy = PolicyNames.RequireSurveyCreator)]
```

Cela renvoie un code d’état 401 si l’utilisateur n’est pas authentifié, et 403 si l’utilisateur est authentifié mais non autorisé. Inscrivez la stratégie au démarrage :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthorization(options =>
    {
        options.AddPolicy(PolicyNames.RequireSurveyCreator,
            policy =>
            {
                policy.AddRequirements(new SurveyCreatorRequirement());
                policy.AddAuthenticationSchemes(JwtBearerDefaults.AuthenticationScheme);
            });
    });
}
```


## Ressources supplémentaires

- [Scénarios d’authentification pour Azure AD][auth-scenarios]

<!-- links -->
[ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx
[auth-scenarios]: ../active-directory/active-directory-authentication-scenarios.md/#web-application-to-web-api
[JwtBearer]: https://www.nuget.org/packages/Microsoft.AspNet.Authentication.JwtBearer
[partie d’une série]: guidance-multitenant-identity.md
[Surveys de Tailspin]: guidance-multitenant-identity-tailspin.md
[IdentityServer3]: https://github.com/IdentityServer/IdentityServer3
[Inscription de l’API web dans Azure AD]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#register-the-surveys-web-api
[Update the application manifests]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#update-the-application-manifests
[Donnez à l’application web l’autorisation d’appeler l’API web]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#give-the-web-app-permissions-to-call-the-web-api
[Token caching]: guidance-multitenant-identity-token-cache.md
[HttpClientExtensions.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Common/HttpClientExtensions.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/Startup.cs
[l’inscription du locataire]: guidance-multitenant-identity-signup.md
[SurveysJwtBearerEvents.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/SurveyJwtBearerEvents.cs
[transformation des revendications]: guidance-multitenant-identity-claims.md#claims-transformations
[Authorization]: guidance-multitenant-identity-authorize.md
[exemple d’application]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->