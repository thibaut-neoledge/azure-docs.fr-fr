---
title: "Prise en main d’Azure AD .NET | Microsoft Docs"
description: "Création d’une API web MVC .NET qui s’intègre avec Azure AD pour l’authentification et l’autorisation."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: c579135f798ea0c2a5461fdd7c88244d2d6d78c6
ms.openlocfilehash: c401d473d3951ed5853170e8761a09a915329164


---
# <a name="protect-a-web-api-using-bearer-tokens-from-azure-ad"></a>Protection d’une API web à l’aide de jetons porteurs à partir d’Azure AD
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Si vous créez une application qui fournit un accès aux ressources protégées, vous devez savoir comment protéger ces ressources contre tout accès injustifié.
Azure AD facilite la protection d’une API web à l’aide de jetons d’accès OAuth Bearer 2.0 avec seulement quelques lignes de code.

Dans les applications web Asp.NET, vous pouvez y parvenir en utilisant l’implémentation Microsoft de l’intergiciel communautaire OWIN inclus dans .NET Framework 4.5.  Ici, nous allons utiliser OWIN pour créer une API web « Liste des tâches » qui effectue les actions suivantes :

* Désigne les API qui sont protégées.
* Valide le fait que les appels d’API web contiennent un jeton d’accès valide.

Pour ce faire, vous devez :

1. inscrire une application auprès d’Azure AD ;
2. configurer votre application pour utiliser le pipeline d’authentification OWIN ;
3. configurer une application cliente pour appeler l’API web To Do List.

Pour commencer, téléchargez [la structure de l’application](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) ou [l’exemple terminé](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip).  Chaque option est une solution Visual Studio 2013.  Vous aurez également besoin d’un client Azure AD dans lequel inscrire votre application.  Si ce n’est pas déjà fait, [découvrez comment en obtenir un](active-directory-howto-tenant.md).

## <a name="1----register-an-application-with-azure-ad"></a>1.    Inscrire une application auprès d’Azure AD
Pour sécuriser votre application, vous devez tout d’abord créer une application dans votre client et fournir quelques informations essentielles à Azure AD.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans la barre supérieure, cliquez sur votre compte et, dans la liste **Répertoire**, choisissez le locataire Active Directory auprès duquel vous voulez inscrire votre application.
3. Cliquez sur **Autres services** dans le volet de navigation gauche et choisissez **Azure Active Directory**.
4. Cliquez sur **Inscriptions des applications**, puis sur **Ajouter**.
5. Suivez les invites et créez une **Application Web et/ou API Web**.
  * Le **nom** de l’application doit décrire votre application aux utilisateurs finaux.  Entrez « To Do List Service ».
  * L’ **URI de redirection** est une combinaison de schémas et de chaînes qu’Azure AD utilise pour renvoyer les jetons demandés par l’application. Entrez `https://localhost:44321/` pour cette valeur.
  * Dans le champ **URI ID d’application**, entrez un identificateur propre au locataire, par exemple `https://contoso.onmicrosoft.com/TodoListService`.
6. Enregistrez la configuration.  Laissez le portail ouvert, car vous devrez également bientôt inscrire votre application cliente.

## <a name="2-set-up-your-app-to-use-the-owin-authentication-pipeline"></a>2. Configurez votre application pour utiliser le pipeline d'authentification OWIN
Maintenant que vous avez inscrit l’application auprès d’Azure AD, vous devez la configurer pour qu’elle communique avec Azure AD afin de valider les demandes et les jetons entrants.

* Pour commencer, ouvrez la solution et ajoutez les packages NuGet de l’intergiciel OWIN au projet TodoListService à l’aide de la console du gestionnaire de package.

```
PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

* Ajoutez une classe de démarrage OWIN au projet TodoListService appelé `Startup.cs`.  Cliquez avec le bouton droit sur le projet --> **Ajouter** --> **Nouvel élément** --> Recherchez « OWIN ».  L’intergiciel OWIN appelle la méthode `Configuration(…)` lorsque votre application démarre.
* Modifiez la déclaration de classe en `public partial class Startup` .  Dans la méthode `Configuration(…)`, appelez ConfgureAuth(...) pour configurer l’authentification pour votre application web.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

* Ouvrez le fichier `App_Start\Startup.Auth.cs` et implémentez la méthode `ConfigureAuth(…)`.  Les paramètres que vous fournissez dans `WindowsAzureActiveDirectoryBearerAuthenticationOptions` serviront de coordonnées pour que votre application puisse communiquer avec Azure AD.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.UseWindowsAzureActiveDirectoryBearerAuthentication(
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions
        {
            Audience = ConfigurationManager.AppSettings["ida:Audience"],
            Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
        });
}
```

* Vous pouvez désormais utiliser les attributs `[Authorize]` pour protéger vos contrôleurs et vos actions avec l’authentification de porteur JWT.  Décorez la classe `Controllers\TodoListController.cs` avec une balise authorize.  Cela force l’utilisateur à se connecter avant d’accéder à cette page.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

* Lorsqu’un appelant autorisé appelle correctement l’une des API `TodoListController` , l’action peut avoir besoin d’accéder aux informations sur l’appelant.  OWIN fournit l’accès aux revendications dans le jeton porteur via l’objet `ClaimsPrincpal` .  
* Une exigence courante pour les API web consiste à valider les étendues présentes dans le jeton. Cela garantit que l’utilisateur final a octroyé les autorisations requises pour accéder au service To Do List :

```C#
public IEnumerable<TodoItem> Get()
{
    // user_impersonation is the default permission exposed by applications in AAD
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
    {
        throw new HttpResponseException(new HttpResponseMessage {
          StatusCode = HttpStatusCode.Unauthorized,
          ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
        });
    }
    ...
}
```

* Enfin, ouvrez le fichier `web.config` dans la racine du projet ToDoListService, puis entrez les valeurs de configuration dans la section `<appSettings>`.
  * `ida:Tenant` est le nom de votre client Azure AD, par exemple, « contoso.onmicrosoft.com ».
  * `ida:Audience` est l’URI ID d’application que vous avez entré dans le portail Azure.

## <a name="3-configure-a-client-application--run-the-service"></a>3. Configuration d’une application cliente et exécution du service
Avant de pouvoir voir le service Todo List en action, vous devez configurer le client Todo List afin qu’il puisse obtenir des jetons d’AAD et effectuer des appels au service.

* Revenez au [portail Azure](https://portal.azure.com).
* Créez une application dans votre client Azure AD, puis sélectionnez **Application cliente native** dans l’invite qui en résulte.
  * Le **nom** de l’application doit décrire votre application aux utilisateurs finaux.
  * Pour **l’URI de redirection**, entrez `http://TodoListClient/`.
* Une fois l’inscription terminée, AAD affecte un **ID d’application** unique à votre application. Copiez cette valeur à partir de la page de l’application, car vous en aurez besoin dans les étapes suivantes.
* Sur la page **Paramètres**, choisissez **Autorisations requises** et **Ajouter**.  Recherchez et sélectionnez TodoListService, ajoutez l’autorisation d’**accès à TodoListService** sous **Autorisations déléguées**, puis choisissez **Terminé**.

* Dans Visual Studio, ouvrez `App.config` dans le projet TodoListClient et entrez les valeurs de votre configuration dans la section `<appSettings>`.
  
  * `ida:Tenant` est le nom de votre client Azure AD, par exemple, « contoso.onmicrosoft.com ».
  * `ida:ClientId` est l’ID d’application que vous avez copié à partir du portail Azure.
  * `todo:TodoListResourceId` est l’URI ID d’application de l’application To Do List Service que vous avez entré dans le portail Azure.

Enfin, nettoyez, générez et exécutez chaque projet.  Si vous ne l’avez pas encore fait, il est temps de créer un nouvel utilisateur dans votre client, avec un domaine *.onmicrosoft.com.  Connectez-vous au client To Do List avec cet utilisateur et ajoutez quelques tâches à la liste des tâches de cet utilisateur.

Pour référence, l’exemple terminé (sans vos valeurs de configuration) est fourni [ici](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip).  Vous pouvez à présent aborder d’autres scénarios d’identité.

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]




<!--HONumber=Jan17_HO3-->


