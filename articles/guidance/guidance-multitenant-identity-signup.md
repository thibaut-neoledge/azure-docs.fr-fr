<properties
   pageTitle="Inscription et intégration de locataire dans des applications multi-locataires | Microsoft Azure"
   description="Comment intégrer des locataires à une application multi-locataire"
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

# Inscription et intégration de locataire dans une application multi-locataire

Cet article fait [partie d’une série]. Un [exemple d’application] accompagne également cette série.

Cet article décrit comment implémenter un processus d’_inscription_ dans une application multi-locataire, ce qui permet à un client d’inscrire son organisation à votre application. Il existe plusieurs raisons d’implémenter un processus d’inscription :

-	Autoriser un administrateur Active Directory à consentir que l’ensemble de l’organisation du client utilise l’application.
-	Collecter des informations relatives au paiement par carte de crédit ou d’autres informations du client.
-	Effectuer toute configuration par locataire en une seule fois nécessaire à votre application.

## Consentement administrateur et autorisations Azure AD

Pour s’authentifier auprès d’Azure AD, une application doit accéder au répertoire de l’utilisateur. L’application doit avoir au minimum une autorisation de lecteur du profil utilisateur. La première fois qu’un utilisateur se connecte, Azure AD affiche une page de consentement qui répertorie les autorisations demandées. En cliquant sur **Accepter**, l’utilisateur accorde l’autorisation à l’application.

Par défaut, le consentement est accordé par utilisateur. Chaque utilisateur qui se connecte voit la page de consentement. Toutefois, Azure AD prend également en charge le _consentement administrateur_, qui permet à un administrateur Active Directory de donner son consentement pour l’intégralité d’une organisation.

Quand le flux de consentement administrateur est utilisé, la page de consentement indique que l’administrateur Active Directory accorde l’autorisation pour le compte de l’intégralité du locataire :

![Invite de consentement administrateur](media/guidance-multitenant-identity/admin-consent.png)

Une fois que l’administrateur a cliqué sur **Accepter**, les autres utilisateurs appartenant au même locataire peuvent se connecter, et Azure AD omet alors l’écran de consentement.

Seul un administrateur Active Directory peut donner un consentement administrateur, car il accorde l’autorisation pour le compte de l’ensemble de l’organisation. Si un utilisateur non-administrateur tente de s’authentifier avec le flux de consentement administrateur, Azure AD affiche une erreur :

![Erreur de consentement](media/guidance-multitenant-identity/consent-error.png)

Si l’application nécessite des autorisations supplémentaires ultérieurement, le client doit s’inscrire à nouveau et donner son consentement pour les autorisations mises à jour.

## Implémentation de l’inscription du locataire

Pour l’application [Surveys de Tailspin][Tailspin], nous avons défini plusieurs conditions requises pour le processus d’inscription :

-	Un locataire doit s’inscrire avant que des utilisateurs puissent se connecter.
-	L’inscription utilise le flux de consentement administrateur.
-	L’inscription ajoute le locataire de l’utilisateur à la base de données de l’application.
-	Une fois un locataire inscrit, l’application affiche une page d’intégration.

Dans cette section, nous allons passer en revue notre implémentation de la procédure d’inscription. Il est important de comprendre que l’opposition de l’« inscription » et de la « connexion » est un concept d’application. Pendant le flux d’authentification, Azure AD ne sais pas, de façon intrinsèque, si l’utilisateur est en cours d’inscription. C’est à l’application d’effectuer le suivi du contexte.

Quand un utilisateur anonyme visite l’application Surveys, deux boutons s’affichent à lui : un pour se connecter et un pour « abonner votre société » (inscription).

![Page d’inscription de l’application](media/guidance-multitenant-identity/sign-up-page.png)

Ces boutons appellent des actions dans la classe [AccountController].

L’action `SignIn` renvoie un **ChallegeResult**, ce qui entraîne la redirection, par le middleware OpenID Connect, vers le point de terminaison d’authentification. Il s’agit de la façon par défaut de déclencher l’authentification dans ASP.NET Core 1.0.

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

Comparez maintenant l’action `SignUp` :

```csharp
[AllowAnonymous]
public IActionResult SignUp()
{
    // Workaround for https://github.com/aspnet/Security/issues/546
    HttpContext.Items.Add("signup", "true");

    var state = new Dictionary<string, string> { { "signup", "true" }};
    return new ChallengeResult(
        OpenIdConnectDefaults.AuthenticationScheme,
        new AuthenticationProperties(state)
        {
            RedirectUri = Url.Action(nameof(SignUpCallback), "Account")
        });
}
```

Comme `SignIn`, l’action `SignUp` renvoie également un `ChallengeResult`. Mais cette fois, nous ajoutons une information d’état aux `AuthenticationProperties` dans le `ChallengeResult` :

-	signup : indicateur booléen signalant que l’utilisateur a commencé le processus d’inscription.

Les informations d’état dans `AuthenticationProperties` sont ajoutées au paramètre [state] OpenID Connect, qui effectue des allers-retours pendant le flux d’authentification.

![Paramètre d’état](media/guidance-multitenant-identity/state-parameter.png)

Une fois que l’utilisateur s’authentifie dans Azure AD et qu’il est redirigé vers l’application, le ticket d’authentification contient l’état. Nous utilisons cela pour garantir que la valeur « signup » est conservée dans l’intégralité du flux d’authentification.

## Ajout de l’invite de consentement administrateur

Dans Azure AD, le flux de consentement administrateur est déclenché en ajoutant un paramètre « prompt » à la chaîne de requête dans la demande d’authentification :

```
/authorize?prompt=admin_consent&...
```

L’application Surveys ajoute l’invite pendant l’événement `RedirectToAuthenticationEndpoint`. Cet événement est appelé juste avant que le middleware ne redirige vers le point de terminaison de l’authentification.

```csharp
public override Task RedirectToAuthenticationEndpoint(RedirectContext context)
{
    if (context.IsSigningUp())
    {
        context.ProtocolMessage.Prompt = "admin_consent";
    }

    _logger.RedirectToIdentityProvider();
    return Task.FromResult(0);
}
```

> [AZURE.NOTE] Consultez la page [SurveyAuthenticationEvents.cs].

Le paramètre ` ProtocolMessage.Prompt` indique au middleware d’ajouter le paramètre « prompt » à la demande d’authentification.

Notez que l’invite est nécessaire uniquement pendant l’inscription. Une connexion normale ne doit pas l’inclure. Pour faire la distinction entre les deux, nous vérifions la valeur `signup` dans l’état d’authentification. La méthode d’extension suivante vérifie cette condition :

```csharp
internal static bool IsSigningUp(this BaseControlContext context)
{
    Guard.ArgumentNotNull(context, nameof(context));

    string signupValue;
    object obj;
    // Check the HTTP context and convert to string
    if (context.HttpContext.Items.TryGetValue("signup", out obj))
    {
        signupValue = (string)obj;
    }
    else
    {
        // It's not in the HTTP context, so check the authentication ticket.  If it's not there, we aren't signing up.
        if ((context.AuthenticationTicket == null) ||
            (!context.AuthenticationTicket.Properties.Items.TryGetValue("signup", out signupValue)))
        {
            return false;
        }
    }

    // We have found the value, so see if it's valid
    bool isSigningUp;
    if (!bool.TryParse(signupValue, out isSigningUp))
    {
        // The value for signup is not a valid boolean, throw                
        throw new InvalidOperationException($"'{signupValue}' is an invalid boolean value");
    }

    return isSigningUp;
}
```

> [AZURE.NOTE] Consultez la page [BaseControlContextExtensions.cs].

> [AZURE.NOTE] Remarque : Ce code inclut une solution de contournement pour un bogue connu dans ASP.NET Core 1.0 RC1. Dans l’événement `RedirectToAuthenticationEndpoint`, il n’existe aucun moyen d’obtenir les propriétés d’authentification qui contiennent l’état « signup ». Pour résoudre ce problème, la méthode `AccountController.SignUp` met également l’état « signup » dans le `HttpContext`. Cela fonctionne car `RedirectToAuthenticationEndpoint` se produit avant la redirection. Nous avons donc toujours le même `HttpContext`.

## Inscription d’un locataire

L’application Surveys stocke certaines informations sur chaque locataire et utilisateur dans la base de données de l’application.

![Table Tenant](media/guidance-multitenant-identity/tenant-table.png)

Dans la table Tenant, IssuerValue est la valeur de la revendication de l’émetteur pour le locataire. Pour Azure AD, cette valeur est `https://sts.windows.net/<tentantID>` et elle donne une valeur unique par client.

Quand un nouveau locataire se connecte, l’application Surveys écrit un enregistrement de locataire dans la base de données. Cela se produit à l’intérieur de l’événement `AuthenticationValidated`. (Ne le faites pas avant cet événement, car le jeton d’ID n’est alors pas être validé, et vous ne pouvez donc pas approuver les valeurs de revendication.) Consultez la page [Authentication].

Voici le code approprié de l’application Surveys :

```csharp
public override async Task AuthenticationValidated(AuthenticationValidatedContext context)
{
    var principal = context.AuthenticationTicket.Principal;
    var userId = principal.GetObjectIdentifierValue();
    var tenantManager = context.HttpContext.RequestServices.GetService<TenantManager>();
    var userManager = context.HttpContext.RequestServices.GetService<UserManager>();
    var issuerValue = principal.GetIssuerValue();
    _logger.AuthenticationValidated(userId, issuerValue);

    // Normalize the claims first.
    NormalizeClaims(principal);
    var tenant = await tenantManager.FindByIssuerValueAsync(issuerValue)
        .ConfigureAwait(false);

    if (context.IsSigningUp())
    {
        // Originally, we were checking to see if the tenant was non-null, however, this would not allow
        // permission changes to the application in AAD since a re-consent may be required.  Now we just don't
        // try to recreate the tenant.
        if (tenant == null)
        {
            tenant = await SignUpTenantAsync(context, tenantManager)
                .ConfigureAwait(false);
        }

        // In this case, we need to go ahead and set up the user signing us up.
        await CreateOrUpdateUserAsync(context.AuthenticationTicket, userManager, tenant)
            .ConfigureAwait(false);
    }
    else
    {
        if (tenant == null)
        {
            _logger.UnregisteredUserSignInAttempted(userId, issuerValue);
            throw new SecurityTokenValidationException($"Tenant {issuerValue} is not registered");
        }

        await CreateOrUpdateUserAsync(context.AuthenticationTicket, userManager, tenant)
            .ConfigureAwait(false);
    }
}
```

> [AZURE.NOTE] Consultez la page [SurveyAuthenticationEvents.cs].

Ce code effectue les actions suivantes :

1.	Vérifier si la valeur d’émetteur du locataire se trouve déjà dans la base de données. Si le locataire ne s’est pas inscrit, `FindByIssuerValueAsync` renvoie la valeur null.
2.	Si l’utilisateur s’inscrit :
  1.	Ajouter le locataire à la base de données (`SignUpTenantAsync`).
  2.	Ajouter l’utilisateur authentifié à la base de données (`CreateOrUpdateUserAsync`).
3.	Sinon, exécuter le flux de connexion normal :
  1.	Si l’émetteur du locataire est introuvable dans la base de données, cela signifie que le locataire n’est pas inscrit, et le client doit s’inscrire. Dans ce cas, lever une exception pour provoquer l’échec de l’authentification.
  2.	Sinon, créer un enregistrement de base de données pour cet utilisateur, s’il n’existe pas déjà (`CreateOrUpdateUserAsync`).

Voici la méthode [SignUpTenantAsync] qui ajoute le locataire à la base de données.

```csharp
private async Task<Tenant> SignUpTenantAsync(BaseControlContext context, TenantManager tenantManager)
{
    Guard.ArgumentNotNull(context, nameof(context));
    Guard.ArgumentNotNull(tenantManager, nameof(tenantManager));

    var principal = context.AuthenticationTicket.Principal;
    var issuerValue = principal.GetIssuerValue();
    var tenant = new Tenant
    {
        IssuerValue = issuerValue,
        Created = DateTimeOffset.UtcNow
    };

    try
    {
        await tenantManager.CreateAsync(tenant)
            .ConfigureAwait(false);
    }
    catch(Exception ex)
    {
        _logger.SignUpTenantFailed(principal.GetObjectIdentifierValue(), issuerValue, ex);
        throw;
    }

    return tenant;
}
```

Voici un résumé de l’intégralité du flux d’inscription dans l’application Surveys :

1.	L’utilisateur clique sur le bouton **S’inscrire**.
2.	L’action `AccountController.SignUp` renvoie un résultat du test. L’état d’authentification inclut la valeur de « signup ».
3.	Dans l’événement `RedirectToAuthenticationEndpoint`, ajoutez l’invite `admin_consent`.
4.	Le middleware OpenID Connect redirige vers Azure AD et l’utilisateur s’authentifie.
5.	Dans l’événement `AuthenticationValidated`, recherchez l’état « signup ».
6.	Ajoutez le locataire à la base de données.


<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[partie d’une série]: guidance-multitenant-identity.md
[AccountController]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Controllers/AccountController.cs
[state]: http://openid.net/specs/openid-connect-core-1_0.html#AuthRequest
[SurveyAuthenticationEvents.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/SurveyAuthenticationEvents.cs
[BaseControlContextExtensions.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/BaseControlContextExtensions.cs
[Authentication]: guidance-multitenant-identity-authenticate.md
[SignUpTenantAsync]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/SurveyAuthenticationEvents.cs
[exemple d’application]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->