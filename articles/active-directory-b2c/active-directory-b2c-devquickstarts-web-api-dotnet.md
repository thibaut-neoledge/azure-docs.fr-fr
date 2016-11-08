---
title: Azure Active Directory B2C | Microsoft Docs
description: Comment créer une application web qui appelle une API web à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: msmbaldwin
editor: ''

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/22/2016
ms.author: dastrock

---
# Azure AD B2C : appel d’une API web à partir d’une application web .NET
Avec Azure Active Directory (Azure AD) B2C, vous pouvez ajouter de puissantes fonctionnalités de gestion des identités en libre-service à vos applications web et API web, en seulement quelques étapes. Cet article explique comment créer une application web de type « liste des tâches » MVC (Model-View-Controller) .NET, qui appelle une API web en utilisant des jetons du porteur.

Cet article ne couvre pas l'implémentation de la connexion, de l'inscription et de la gestion de profil avec Azure AD B2C. Il s'intéresse principalement à l'appel d'API web après que l'utilisateur s'est authentifié. Si ce n’est pas déjà fait, vous devriez commencer par lire le [didacticiel sur la prise en main de l’application web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) pour en savoir plus sur les principes de base d’Azure AD B2C.

## Obtention d'un répertoire Azure AD B2C
Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou un client. Un répertoire est un conteneur destiné à recevoir tous vos utilisateurs, applications, groupes et autres. Si vous n’en possédez pas déjà un, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant d’aller plus loin dans ce guide.

## Création d'une application
Vous devez maintenant créer dans votre répertoire B2C une application fournissant à Azure AD certaines informations nécessaires pour communiquer de manière sécurisée avec votre application. Dans ce cas, l’application web et l’API web sont toutes les deux représentées par un seul **ID d’application**, car elles constituent une application logique. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md). Veillez à effectuer les opérations suivantes :

* Incluez une **application web/API web** dans l’application.
* Entrez `https://localhost:44316/` comme **URL de réponse**. Il s’agit de l’URL par défaut pour cet exemple de code.
* Copiez **l’ID d’application** affecté à votre application. Vous en aurez besoin ultérieurement.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Création de vos stratégies
Dans Azure AD B2C, chaque expérience utilisateur est définie par une [stratégie](active-directory-b2c-reference-policies.md). Cette application web contient trois expériences liées à l’identité : l’inscription, la connexion et la modification du profil. Vous devez créer une stratégie pour chaque type, comme décrit dans [l’article de référence sur les stratégies](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Lors de la création des trois stratégies, assurez-vous de :

* Choisir le **Nom d’affichage** et d’autres attributs d’inscription dans votre stratégie d’inscription.
* Choisir les revendications d’applications **Nom d’affichage** et **ID objet** pour chaque stratégie. Vous pouvez aussi choisir d'autres revendications.
* Copiez le **nom** de chaque stratégie après sa création. Il doit porter le préfixe `b2c_1_`. Vous aurez besoin des noms de ces stratégies ultérieurement.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Une fois vos 3 stratégies créées, vous pouvez générer votre application.

Remarque : cet article n’explique pas comment utiliser les stratégies que vous venez de créer. Pour en savoir plus sur le fonctionnement des stratégies dans Azure AD B2C, commencez par consulter le [didacticiel sur la prise en main de l’application web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Téléchargement du code
Le code associé à ce didacticiel [est stocké sur GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet). Pour générer l’exemple à mesure que vous avancez, vous pouvez [télécharger la structure de projet sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip). Vous pouvez également cloner la structure :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

L’application terminée est également [disponible en tant que fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip) ou sur la branche `complete` du même référentiel.

Une fois l’exemple de code téléchargé, ouvrez le fichier .sln Visual Studio pour commencer.

## Configuration de l’application web de la tâche
Pour que `TaskWebApp` communique avec Azure AD B2C, vous devez renseigner certains paramètres courants. Dans le projet `TaskWebApp`, ouvrez le fichier `web.config` qui se trouve à la racine du projet et remplacez les valeurs de la section `<appSettings>` : Vous pouvez conserver les valeurs `AadInstance`, `RedirectUri` et `TaskServiceUrl` en l’état.

```
  <appSettings>

    ...

    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

<appSettings> <add key="webpages:Version" value="3.0.0.0" /> <add key="webpages:Enabled" value="false" /> <add key="ClientValidationEnabled" value="true" /> <add key="UnobtrusiveJavaScriptEnabled" value="true" /> <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" /> <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" /> <add key="ida:ClientSecret" value="E:i~5GHYRF$Y7BcM" /> <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" /> <add key="ida:RedirectUri" value="https://localhost:44316/" /> <add key="ida:SignUpPolicyId" value="b2c\_1_sign\_up" /> <add key="ida:SignInPolicyId" value="b2c_1_sign\_in" /> <add key="ida:UserProfilePolicyId" value="b2c_1\_edit\_profile" /> <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" /> </appSettings>

## Obtention des jetons d'accès et appel de l'API de tâche
Cette section traite de l’utilisation du jeton reçu pendant la connexion avec Azure AD B2C afin d’accéder à une API web qui est également sécurisée avec Azure AD B2C.

Cet article n’explique pas comment sécuriser l’API. Pour apprendre comment une API web authentifie en toute sécurité les demandes à l’aide d’Azure AD B2C, consultez [l’article sur la prise en main de l’API web](active-directory-b2c-devquickstarts-api-dotnet.md).

### Enregistrement du jeton de connexion
Tout d’abord, authentifier l’utilisateur (à l’aide de l’une de vos stratégies) et recevez un jeton de connexion Azure AD B2C. Si vous pensez ne pas savoir comment exécuter des stratégies, revenez en arrière et consultez le [didacticiel sur la prise en main de l’application web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) pour en savoir plus sur les principes de base d’Azure AD B2C.

Ouvrez le fichier `App_Start\Startup.Auth.cs`. Vous devez effectuer une modification importante aux `OpenIdConnectAuthenticationOptions` : vous devez définir `SaveSignInToken = true`.

```C#
// App_Start\Startup.Auth.cs

return new OpenIdConnectAuthenticationOptions
{
    // For each policy, give OWIN the policy-specific metadata address, and
    // set the authentication type to the id of the policy
    MetadataAddress = String.Format(aadInstance, tenant, policy),
    AuthenticationType = policy,

    // These are standard OpenID Connect parameters, with values pulled from web.config
    ClientId = clientId,
    RedirectUri = redirectUri,
    PostLogoutRedirectUri = redirectUri,
    Notifications = new OpenIdConnectAuthenticationNotifications
    {
        AuthenticationFailed = OnAuthenticationFailed,
    },
    Scope = "openid",
    ResponseType = "id_token",

    TokenValidationParameters = new TokenValidationParameters
    {
        NameClaimType = "name",

        // Add this line to reserve the sign in token for later use
        SaveSigninToken = true,
    },
};
```

### Obtention d’un jeton dans les contrôleurs
Le `TasksController` est responsable de la communication avec l’API web, et envoie des demandes HTTP à celle-ci pour lire, créer et supprimer des tâches. Puisque l’API est sécurisée par Azure AD B2C, vous devez d’abord récupérer le jeton que vous avez enregistré à l’étape précédente.

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        var bootstrapContext = ClaimsPrincipal.Current.Identities.First().BootstrapContext as System.IdentityModel.Tokens.BootstrapContext;

    ...
}
```

Le `BootstrapContext` contient le jeton de connexion que vous avez acquis en exécutant l’une de vos stratégies B2C.

### Lecture de tâches à partir de l'API Web
Quand vous avez un jeton, vous pouvez le joindre à la demande `GET` HTTP dans l’en-tête `Authorization` pour appeler `TaskService` en toute sécurité :

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        ...

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

        // Add the token acquired from ADAL to the request headers
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", bootstrapContext.Token);
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
            // If the call failed with access denied, show the user an error indicating they might need to sign-in again.
            if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
            {
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
Suivez le même modèle lorsque vous envoyez les demandes `POST` et `DELETE` à l’API web, à l’aide du `BootstrapContext` pour récupérer le jeton de connexion. Nous avons implémenté l’action de création pour vous. Vous pouvez essayer de terminer l’action de suppression dans `TasksController.cs`.

## Exécution de l'exemple d'application
Enfin, créez et exécutez l’application. Inscrivez-vous et connectez-vous et créez des tâches pour l’utilisateur connecté. Déconnectez-vous et connectez-vous en tant qu’autre utilisateur. Créer des tâches pour cet utilisateur. Notez la façon dont les tâches sont stockées par utilisateur sur l’API, car l’API extrait l’identité de l’utilisateur à partir du jeton qu’elle reçoit.

Pour référence, l’exemple terminé [est fourni en tant que fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->

<!---HONumber=AcomDC_0727_2016-->