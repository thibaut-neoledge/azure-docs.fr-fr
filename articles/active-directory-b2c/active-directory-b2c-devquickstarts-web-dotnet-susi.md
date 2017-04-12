---
title: Azure Active Directory B2C | Microsoft Docs
description: "Comment créer une application web avec inscription/connexion, modification du profil et réinitialisation du mot de passe à l’aide d’Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: krassk
editor: 
ms.assetid: 30261336-d7a5-4a6d-8c1a-7943ad76ed25
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 4d7476156b51ca82b1f119becb1576a97d2bd457
ms.lasthandoff: 03/23/2017


---
# <a name="azure-ad-b2c-sign-up--sign-in-in-a-aspnet-web-app"></a>Azure AD B2C : inscription et connexion dans une application web ASP.NET

Avec Azure AD B2C, vous pouvez ajouter à votre application web des fonctionnalités de gestion des identités performantes. Cet article explique comment créer une application web ASP.NET qui inclut l’inscription/la connexion des utilisateurs, la modification du profil et la réinitialisation du mot de passe.

## <a name="create-an-azure-ad-b2c-directory"></a>Créer un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou un client. Un répertoire est un conteneur destiné à recevoir tous vos utilisateurs, applications, groupes, etc. Si vous n’en possédez pas déjà un, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant d’aller plus loin dans ce guide.

## <a name="create-an-application"></a>Création d'une application

Vous devez maintenant créer dans votre répertoire B2C une application web. fournissant à Azure AD certaines informations nécessaires pour communiquer de manière sécurisée avec votre application. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md). Veillez à effectuer les opérations suivantes :

* Incluez une **application web/API web** dans l’application.
* Entrez `https://localhost:44316/` comme **URI de redirection**. Il s’agit de l’URL par défaut pour cet exemple de code.
* Notez également l' **ID d'application** affecté à votre application.  Vous en aurez besoin ultérieurement.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Création de vos stratégies

Dans Azure AD B2C, chaque expérience utilisateur est définie par une [stratégie](active-directory-b2c-reference-policies.md). Cet exemple de code contient trois expériences liées à l’identité : **l’inscription et la connexion**, la **modification du profil** et la **réinitialisation du mot de passe**.  Vous devez créer une stratégie de chaque type, comme décrit dans [l’article de référence sur les stratégies](active-directory-b2c-reference-policies.md). Lorsque vous créez des stratégies, veillez à :

* Choisir **Inscription par le biais d’un ID utilisateur** ou **Inscription par le biais d’une adresse e-mail** dans le panneau des fournisseurs d’identité.
* Choisir le **Nom d’affichage** et d’autres attributs d’inscription dans votre stratégie d’inscription et de connexion.
* Choisir la revendication **Nom d’affichage** comme revendication d’application pour chaque stratégie. Vous pouvez aussi choisir d'autres revendications.
* Copiez le **nom** de chaque stratégie après sa création. Vous aurez besoin des noms de ces stratégies ultérieurement.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Une fois vos stratégies créées, vous pouvez générer votre application.

## <a name="download-the-code"></a>Téléchargement du code

Le code associé à ce didacticiel est stocké sur [GitHub](https://github.com/Azure-Samples/b2c-dotnet-webapp-and-webapi). Vous pouvez cloner l’exemple en exécutant :

```console
git clone https://github.com/Azure-Samples/b2c-dotnet-webapp-and-webapi.git
```

Une fois l’exemple de code téléchargé, ouvrez le fichier .sln Visual Studio pour commencer. Le fichier solution contient deux projets : `TaskWebApp` et `TaskService`. `TaskWebApp` est l’application web MVC avec laquelle l’utilisateur interagit. `TaskService` est l’API web du serveur principal de l’application qui stocke la liste de tâches de chaque utilisateur. Cet article traite uniquement de l’application `TaskWebApp`. Pour savoir comment générer `TaskService` à l’aide d’Azure AD B2C, consultez [notre didacticiel sur les API web .NET](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Mettre à jour la configuration Azure AD B2C

Notre exemple est configuré pour utiliser les stratégies et l’ID client du locataire de notre démonstration. Si vous souhaitez utiliser votre propre locataire, vous devez ouvrir `web.config` dans le projet `TaskWebApp` et remplacer les valeurs comme suit :

* `ida:Tenant` par le nom de votre locataire
* `ida:ClientId` par votre ID d’application web
* `ida:ClientSecret` par votre clé secrète d’application web
* `ida:SignUpSignInPolicyId` par votre nom de stratégie d’inscription ou de connexion
* `ida:EditProfilePolicyId` par votre nom de stratégie « Modifier le profil »
* `ida:ResetPasswordPolicyId` par votre nom de stratégie « Réinitialiser le mot de passe »

## <a name="add-authentication-support"></a>Ajouter le support de l’authentification

Vous pouvez à présent configurer votre application pour utiliser Azure AD B2C. Votre application communique avec Azure AD B2C en envoyant des demandes d’authentification OpenID Connect. Les demandes déterminent l’expérience utilisateur que votre application souhaite exécuter en spécifiant la stratégie. Vous pouvez utiliser la bibliothèque OWIN de Microsoft pour envoyer des requêtes, exécuter les stratégies, gérer les sessions utilisateur, etc.

### <a name="install-owin"></a>Installer OWIN

Pour commencer, ajoutez au projet les packages NuGet du middleware OWIN à l’aide de la console du gestionnaire de package Visual Studio.

```Console
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="add-an-owin-startup-class"></a>Ajouter une classe de démarrage OWIN

Ajoutez une classe de démarrage OWIN à l’API appelée `Startup.cs`.  Cliquez avec le bouton droit sur le projet, sélectionnez **Ajouter** et **Nouvel élément**, puis recherchez OWIN. L’intergiciel OWIN appelle la méthode `Configuration(…)` lorsque votre application démarre.

Dans notre exemple, nous avons modifié la déclaration de classe sur `public partial class Startup` et implémenté l’autre partie de la classe dans `App_Start\Startup.Auth.cs`. À l’intérieur de la méthode `Configuration`, nous avons ajouté un appel vers `ConfigureAuth`, qui est défini dans `Startup.Auth.cs`. Après modification, `Startup.cs` ressemble à ceci :

```CSharp
// Startup.cs

public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        // ConfigureAuth defined in other part of the class
        ConfigureAuth(app);
    }
}
```

### <a name="configure-the-authentication-middleware"></a>Configurer le middleware d’authentification

Ouvrez le fichier `App_Start\Startup.Auth.cs` et implémentez la méthode `ConfigureAuth(...)`. Les paramètres que vous fournissez dans `OpenIdConnectAuthenticationOptions` servent de coordonnées pour que votre application puisse communiquer avec Azure AD B2C. Si vous ne spécifiez pas certains paramètres, la valeur par défaut est utilisée. Par exemple, nous ne spécifions pas `ResponseType` dans l’exemple. Par conséquent, la valeur par défaut `code id_token` est utilisée dans chaque requête sortante pour Azure Active Directory B2C.

Vous devez également configurer l’authentification des cookies. Le middleware OpenID Connect utilise des cookies pour gérer les sessions utilisateur, entre autres.

```CSharp
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // Initialize variables ...

    // Configure the OWIN middleware
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseCookieAuthentication(new CookieAuthenticationOptions());
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseOpenIdConnectAuthentication(
            new OpenIdConnectAuthenticationOptions
            {
                // Generate the metadata address using the tenant and policy information
                MetadataAddress = String.Format(AadInstance, Tenant, DefaultPolicy),

                // These are standard OpenID Connect parameters, with values pulled from web.config
                ClientId = ClientId,
                RedirectUri = RedirectUri,
                PostLogoutRedirectUri = RedirectUri,

                // Specify the callbacks for each type of notifications
                Notifications = new OpenIdConnectAuthenticationNotifications
                {
                    RedirectToIdentityProvider = OnRedirectToIdentityProvider,
                    AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    AuthenticationFailed = OnAuthenticationFailed,
                },

                // Specify the claims to validate
                TokenValidationParameters = new TokenValidationParameters
                {
                    NameClaimType = "name"
                },

                // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
                Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
            }
        );
    }

    // Implement the "Notification" methods...
}
```

Dans `OpenIdConnectAuthenticationOptions` ci-dessus, nous définissons un ensemble de fonctions de rappel pour les notifications spécifiques qui sont reçues par le middleware OpenID Connect. Ces comportements sont définis en utilisant un objet `OpenIdConnectAuthenticationNotifications` et sont stockés dans la variable `Notifications`. Dans notre exemple, nous définissons trois rappels différents en fonction de l’événement.

#### <a name="using-different-policies"></a>Utilisation de différentes stratégies

La notification `RedirectToIdentityProvider` est déclenchée chaque fois qu’une demande est envoyée à Azure AD B2C. Dans la fonction de rappel `OnRedirectToIdentityProvider`, nous vérifions dans l’appel sortant si nous souhaitons utiliser une stratégie différente. Pour effectuer une réinitialisation de mot de passe ou modifier un profil, vous devez utiliser la stratégie correspondante, par exemple la stratégie de réinitialisation du mot de passe, au lieu de la stratégie d’inscription ou de connexion par défaut.

Dans notre exemple, lorsqu’un utilisateur souhaite réinitialiser le mot de passe ou modifier le profil, nous ajoutons la stratégie que nous préférons utiliser dans le contexte d’OWIN. Pour cela, procédez comme suit :

```CSharp
    // Let the middleware know you are trying to use the edit profile policy
    HttpContext.GetOwinContext().Set("Policy", EditProfilePolicyId);
```

Et vous pouvez implémenter la fonction de rappel `OnRedirectToIdentityProvider` de la manière suivante :

```CSharp
/*
*  On each call to Azure AD B2C, check if a policy (e.g. the profile edit or password reset policy) has been specified in the OWIN context.
*  If so, use that policy when making the call. Also, don't request a code (since it won't be needed).
*/
private Task OnRedirectToIdentityProvider(RedirectToIdentityProviderNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    var policy = notification.OwinContext.Get<string>("Policy");

    if (!string.IsNullOrEmpty(policy) && !policy.Equals(DefaultPolicy))
    {
        notification.ProtocolMessage.Scope = OpenIdConnectScopes.OpenId;
        notification.ProtocolMessage.ResponseType = OpenIdConnectResponseTypes.IdToken;
        notification.ProtocolMessage.IssuerAddress = notification.ProtocolMessage.IssuerAddress.Replace(DefaultPolicy, policy);
    }

    return Task.FromResult(0);
}
```

#### <a name="handling-authorization-codes"></a>Gestion des codes d’autorisation

La notification `AuthorizationCodeReceived` est déclenchée lors de la réception d’un code d’autorisation. Le middleware OpenID Connect ne prend pas en charge l’échange de codes pour les jetons d’accès. Vous pouvez échanger manuellement le code pour le jeton dans une fonction de rappel. Pour plus d’informations, consultez cette [documentation](active-directory-b2c-devquickstarts-web-api-dotnet.md).

#### <a name="handling-errors"></a>Gestion des erreurs

La notification `AuthenticationFailed` est déclenchée lors de l’échec de l’authentification. Dans la méthode de rappel associée, vous pouvez gérer les erreurs comme vous le souhaitez. Vous devez toutefois ajouter une vérification du code d’erreur `AADB2C90118`. Lors de l’exécution de la stratégie d’inscription ou de connexion, l’utilisateur a la possibilité de cliquer sur un lien **Vous avez oublié votre mot de passe ?**. Dans ce cas, Azure AD B2C envoie à votre application un code d’erreur indiquant qu’elle doit effectuer une demande à l’aide de la stratégie de réinitialisation de mot de passe à la place.

```CSharp
/*
* Catch any failures received by the authentication middleware and handle appropriately
*/
private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    notification.HandleResponse();

    // Handle the error code that Azure AD B2C throws when trying to reset a password from the login page
    // because password reset is not supported by a "sign-up or sign-in policy"
    if (notification.ProtocolMessage.ErrorDescription != null && notification.ProtocolMessage.ErrorDescription.Contains("AADB2C90118"))
    {
        // If the user clicked the reset password link, redirect to the reset password route
        notification.Response.Redirect("/Account/ResetPassword");
    }
    else if (notification.Exception.Message == "access_denied")
    {
        notification.Response.Redirect("/");
    }
    else
    {
        notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
    }

    return Task.FromResult(0);
}
```

## <a name="send-authentication-requests-to-azure-ad"></a>Envoyer des demandes d’authentification à Azure AD

Votre application est maintenant correctement configurée pour communiquer avec Azure AD B2C en utilisant le protocole d’authentification OpenID Connect. OWIN a pris en charge tous les détails de la création de messages d’authentification, de la validation des jetons d’Azure AD B2C et de la gestion des sessions utilisateur. Il ne reste plus qu’à initier le flux de chaque utilisateur.

Lorsqu’un utilisateur sélectionne **S’inscrire/se connecter**, **Modifier le profil** ou **Réinitialiser le mot de passe** dans l’application web, l’action associée est appelée dans `Controllers\AccountController.cs` :

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign up or sign in
*/
public void SignUpSignIn()
{
    // Use the default policy to process the sign up / sign in flow
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge();
        return;
    }

    Response.Redirect("/");
}

/*
*  Called when requesting to edit a profile
*/
public void EditProfile()
{
    if (Request.IsAuthenticated)
    {
        // Let the middleware know you are trying to use the edit profile policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
        HttpContext.GetOwinContext().Set("Policy", Startup.EditProfilePolicyId);

        // Set the page to redirect to after editing the profile
        var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
        HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

        return;
    }

    Response.Redirect("/");

}

/*
*  Called when requesting to reset a password
*/
public void ResetPassword()
{
    // Let the middleware know you are trying to use the reset password policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
    HttpContext.GetOwinContext().Set("Policy", Startup.ResetPasswordPolicyId);

    // Set the page to redirect to after changing passwords
    var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
    HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

    return;
}
```

Vous pouvez également utiliser OWIN pour déconnecter l’utilisateur de l’application. Dans `Controllers\AccountController.cs`, voici ce que nous obtenons :

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign out
*/
public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request.
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

En plus d’appeler explicitement une stratégie, vous pouvez utiliser une balise `[Authorize]` dans vos contrôleurs qui exécutera une stratégie si l’utilisateur n’est pas connecté. Ouvrez `Controllers\HomeController.cs` et ajoutez la balise `[Authorize]` au contrôleur des revendications.  OWIN sélectionne la dernière stratégie configurée lorsque la balise `[Authorize]` est atteinte.

```CSharp
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

## <a name="display-user-information"></a>Afficher les informations utilisateur

Lors de l’authentification des utilisateurs avec OpenID Connect, Azure AD B2C retourne un jeton d’ID à l’application qui contient des **revendications**. Il s’agit d’assertions concernant l’utilisateur. Vous pouvez utiliser des revendications pour personnaliser votre application.

Ouvrez le fichier `Controllers\HomeController.cs` . Vous pouvez accéder aux revendications de l’utilisateur dans vos contrôleurs par le biais de l’objet principal de sécurité `ClaimsPrincipal.Current` .

```CSharp
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Vous pouvez accéder à toutes les revendications que votre application reçoit de la même façon.  Une liste de toutes les revendications reçues par l’application est disponible sur la page **Revendications** .

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

Enfin, vous pouvez générer et exécuter votre application. Inscrivez-vous à l’application à l’aide d’une adresse de messagerie ou d’un nom d’utilisateur. Déconnectez-vous, puis reconnectez-vous en tant que même utilisateur. Modifiez le profil ou réinitialisez le mot de passe. Déconnectez-vous et inscrivez-vous en tant qu’autre utilisateur. Notez que les informations affichées sous l’onglet **Revendications** correspondent aux informations configurées dans vos stratégies.

## <a name="add-social-idps"></a>Ajout d’IDP sociaux

Actuellement, l’application prend uniquement en charge l’inscription et la connexion d’utilisateurs avec des **comptes locaux**. Il s’agit de comptes stockés dans votre répertoire B2C qui utilisent un nom d’utilisateur et un mot de passe. Avec Azure AD B2C, vous pouvez ajouter la prise en charge d’autres **fournisseurs d’identité** sans modifier votre code.

Pour ajouter des fournisseurs d’identité sociaux à votre application, commencez par suivre les instructions détaillées figurant dans ces articles. Pour chaque fournisseur d’identité que vous souhaitez prendre en charge, vous devez inscrire une application dans ce système et obtenir un ID client.

* [Définir Facebook en tant qu’IDP](active-directory-b2c-setup-fb-app.md)
* [Définir Google en tant qu’IDP](active-directory-b2c-setup-goog-app.md)
* [Définir Amazon en tant qu’IDP](active-directory-b2c-setup-amzn-app.md)
* [Définir LinkedIn en tant qu’IDP](active-directory-b2c-setup-li-app.md)

Après avoir ajouté les fournisseurs d’identité à votre répertoire B2C, vous devez modifier chacune de vos trois stratégies pour inclure ces nouveaux fournisseurs, comme décrit dans [l’article de référence sur les stratégies](active-directory-b2c-reference-policies.md). Après avoir enregistré vos stratégies, exécutez à nouveau l’application.  Les nouveaux fournisseurs d’identité doivent être ajoutés comme options d’inscription et de connexion dans chacune de vos expériences relatives à l’identité.

Vous pouvez tester vos stratégies et observer le résultat sur votre exemple d’application. Ajoutez ou supprimez des fournisseurs d’identité, manipulez des revendications d’application ou modifiez des attributs d’inscription. Faites des essais jusqu’à ce que vous compreniez la façon dont les stratégies, les requêtes d’authentification et la bibliothèque OWIN sont liées.

