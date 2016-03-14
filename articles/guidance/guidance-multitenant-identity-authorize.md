<properties
   pageTitle="Autorisation dans les applications multi-locataires | Microsoft Azure"
   description="Comment déclarer des autorisations dans une application multi-locataire"
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

# Autorisation dans les applications multi-locataires

Cet article fait [partie d’une série]. Un [exemple d’application] accompagne également cette série.

Dans cet article, nous allons étudier deux approches générales de l’autorisation.

-	**Autorisation basée sur les rôles**. Autorisation d’une action en fonction des rôles affectés à un utilisateur. Par exemple, certaines actions nécessitent un rôle d’administrateur.
-	**Autorisation basée sur les ressources**. Autorisation d’une action en fonction d’une ressource particulière. Par exemple, chaque ressource a un propriétaire. Le propriétaire peut supprimer la ressource. Les autres utilisateurs ne le peuvent pas.

Une application classique utilise une combinaison des deux. Par exemple, pour supprimer une ressource, l’utilisateur doit être le propriétaire de la ressource _ou_ un administrateur.

## Autorisation basée sur les rôles

L’application [Surveys de Tailspin][Tailspin] définit les rôles suivants :

- Administrateur. Peut effectuer toutes les opérations CRUD sur toute enquête appartenant à ce locataire.
- Créateur. Peut créer de nouvelles enquêtes.
- Lecteur. Peut lire toutes les enquêtes qui appartiennent à ce locataire.

Les rôles s’appliquent aux _utilisateurs_ de l’application. Dans l’application Surveys, un utilisateur est un administrateur, un créateur ou un lecteur.

Pour plus d’informations sur la façon de définir et de gérer les rôles, consultez [Rôles d’application].

Quelle que soit la façon dont vous gérez les rôles, votre code d’autorisation se présente de la même façon. ASP.NET Core 1.0 introduit une abstraction appelée _stratégies d’autorisation_. Avec cette fonctionnalité, vous définissez des stratégies d’autorisation dans le code, puis vous appliquez ces stratégies à des actions de contrôleur. La stratégie est dissociée du contrôleur.

### Création des stratégies

Pour définir une stratégie, commencez par créer une classe qui implémente `IAuthorizationRequirement`. Il est plus facile de dériver de `AuthorizationHandler`. Dans la méthode `Handle`, examinez la ou les revendications pertinentes.

Voici un exemple de l’application Surveys de Tailspin :

```csharp
public class SurveyCreatorRequirement : AuthorizationHandler<SurveyCreatorRequirement>, IAuthorizationRequirement
{
    protected override void Handle(AuthorizationContext context, SurveyCreatorRequirement requirement)
    {
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin) ||
            context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            context.Succeed(requirement);
        }
    }
}
```

> [AZURE.NOTE] Consultez la page [SurveyCreatorRequirement.cs].

Cette classe définit la configuration requise pour qu’un utilisateur crée une enquête. L’utilisateur doit avoir le rôle SurveyAdmin ou SurveyCreator.

Dans votre classe de démarrage, définissez une stratégie nommée qui inclut une ou plusieurs conditions. S’il existe plusieurs conditions, l’utilisateur doit satisfaire _chaque_ condition pour être autorisé. Le code suivant définit deux stratégies :

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy(PolicyNames.RequireSurveyCreator,
        policy =>
        {
            policy.AddRequirements(new SurveyCreatorRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });

    options.AddPolicy(PolicyNames.RequireSurveyAdmin,
        policy =>
        {
            policy.AddRequirements(new SurveyAdminRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });
});
```

> [AZURE.NOTE] Consultez [Startup.cs].

Ce code définit également le schéma d’authentification, qui indique à ASP.NET quel middleware d’authentification doit s’exécuter si l’autorisation échoue. Dans le cas présent, nous spécifions le middleware d’authentification des cookies, car il peut rediriger l’utilisateur vers une page « Refusé ». L’emplacement de la page « Refusé » est défini dans l’option AccessDeniedPath du middleware. Consultez la page [Configuration du middleware d’authentification].

### Autorisation des actions de contrôleur

Enfin, pour autoriser une action dans un contrôleur MVC, définissez la stratégie dans l’attribut `Authorize` :

```csharp
[Authorize(Policy = "SurveyCreatorRequirement")]
public IActionResult Create()
{
    // ...
}
```

Dans les versions antérieures d’ASP.NET, vous devez définir la propriété **Roles** sur l’attribut :

```csharp
// old way
[Authorize(Roles = "SurveyCreator")]

```

Elle est toujours prise en charge dans ASP.NET Core 1.0, mais elle présente certains inconvénients par rapport aux stratégies d’autorisation :

-	Elle suppose un type de revendication particulier. Les stratégies peuvent identifier n’importe quel type de revendication. Les rôles sont simplement un type de revendication.
-	Le nom de rôle est codé en dur dans l’attribut. Avec les stratégies, la logique d’autorisation se trouve à un seul et même endroit, ce qui facilite la mise à jour ou même le chargement à partir des paramètres de configuration.
-	Les stratégies permettent des décisions d’autorisation plus complexes (par exemple, un âge >= 21) qui ne peuvent pas être exprimées par la simple appartenance à un rôle.

## Autorisation basée sur les ressources

Une _autorisation basée sur les ressources_ se produit chaque fois que l’autorisation dépend d’une ressource spécifique qui sera affectée par une opération. Dans l’application Surveys de Tailspin, chaque enquête a un propriétaire et des collaborateurs zéro-à-plusieurs.

-	Le propriétaire peut lire, mettre à jour, supprimer et publier l’enquête, ainsi qu’annuler sa publication.
-	Le propriétaire peut affecter des collaborateurs à l’enquête.
-	Les collaborateurs peuvent lire et mettre à jour l’enquête.

Notez que « propriétaire » et « collaborateur » ne sont pas des rôles d’application : ils sont stockés par enquête, dans la base de données de l’application. Pour vérifier si un utilisateur peut supprimer une enquête, par exemple, l’application vérifie si l’utilisateur est le propriétaire de cette enquête.

Dans ASP.NET Core 1.0, implémentez une autorisation basée sur les ressources en dérivant d’**AuthorizationHandler** et en remplaçant la méthode **Handle**.

```csharp
public class SurveyAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Survey>
{
     protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
    {
    }
}
```

Notez que cette classe est fortement typée pour les objets de Surveys. Inscrivez la classe pour l’injection de dépendances au démarrage :

```csharp
services.AddSingleton<IAuthorizationHandler>(factory =>
{
    return new SurveyAuthorizationHandler();
});
```

Pour effectuer des vérifications d’autorisation, utilisez l’interface **IAuthorizationService**, que vous pouvez injecter dans vos contrôleurs. Le code suivant vérifie si un utilisateur peut lire une enquête :

```csharp
if (await _authorizationService.AuthorizeAsync(User, survey, Operations.Read) == false)
{
    return new HttpStatusCodeResult(403);
}
```

Étant donné que nous passons un objet `Survey`, cet appel invoque `SurveyAuthorizationHandler`.

Dans votre code d’autorisation, une bonne approche consiste à agréger toutes les autorisations de l’utilisateur basées sur les rôles et basées sur les ressources, puis vérifiez l’agrégat défini par rapport à l’opération souhaitée. Voici un exemple de l’application Surveys. L’application définit plusieurs types d’autorisations :

- Administrateur
- Collaborateur
- Créateur
- Propriétaire
- Lecteur

L’application définit également un ensemble d’opérations possibles sur les enquêtes :

- Créer
- Lire
- Mettre à jour
- Supprimer
- Publier
- Annuler la publication

Le code suivant crée une liste d’autorisations pour un utilisateur et une enquête particuliers. Notez que ce code examine aussi bien les rôles d’application de l’utilisateur que les champs du propriétaire/collaborateur dans l’enquête.

```csharp
protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
{
    var permissions = new List<UserPermissionType>();
    string userTenantId = context.User.GetTenantIdValue();
    int userId = ClaimsPrincipalExtensions.GetUserKey(context.User);
    string user = context.User.GetUserName();

    if (resource.TenantId == userTenantId)
    {
        // Admin can do anything, as long as the resource belongs to the admin's tenant.
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin))
        {
            context.Succeed(operation);
            return;
        }

        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            permissions.Add(UserPermissionType.Creator);
        }
        else
        {
            permissions.Add(UserPermissionType.Reader);
        }

        if (resource.OwnerId == userId)
        {
            permissions.Add(UserPermissionType.Owner);
        }
    }
    if (resource.Contributors != null && resource.Contributors.Any(x => x.UserId == userId))
    {
        permissions.Add(UserPermissionType.Contributor);
    }
    if (ValidateUserPermissions[operation](permissions))
    {
        context.Succeed(operation);
    }
}
```

> [AZURE.NOTE] Consultez la page [SurveyAuthorizationHandler.cs].

Dans une application multi-locataire, vous devez vérifier que les autorisations ne sont pas « divulguées » dans les données d’un autre locataire. Dans l’application Surveys, l’autorisation Collaborateur est autorisée sur tous les locataires : vous pouvez affecter quelqu’un comme collaborateur à partir d’un autre locataire. Les autres types d’autorisations sont limités aux ressources qui appartiennent au locataire de cet utilisateur. Le code vérifie donc l’ID du locataire avant d’accorder ces types d’autorisations (le champ `TenantId` tel qu’affecté au moment de la création de l’enquête.)

L’étape suivante consiste à vérifier l’opération (lecture, mise à jour, suppression, etc.) par rapport aux autorisations. L’application Surveys implémente cette étape à l’aide d’une table de choix de fonctions :

```csharp
static readonly Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>> ValidateUserPermissions
    = new Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>>

    {
        { Operations.Create, x => x.Contains(UserPermissionType.Creator) },

        { Operations.Read, x => x.Contains(UserPermissionType.Creator) ||
                                x.Contains(UserPermissionType.Reader) ||
                                x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Update, x => x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Delete, x => x.Contains(UserPermissionType.Owner) },

        { Operations.Publish, x => x.Contains(UserPermissionType.Owner) },

        { Operations.UnPublish, x => x.Contains(UserPermissionType.Owner) }
    };
```

## Ressources supplémentaires

- [Resource Based Authorization](https://docs.asp.net/en/latest/security/authorization/resourcebased.html)
- [Custom Policy-Based Authorization](https://docs.asp.net/en/latest/security/authorization/policies.html)

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[partie d’une série]: guidance-multitenant-identity.md
[Rôles d’application]: guidance-multitenant-identity-app-roles.md
[SurveyCreatorRequirement.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyCreatorRequirement.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[Configuration du middleware d’authentification]: guidance-multitenant-identity-authenticate.md#configuring-the-authentication-middleware
[SurveyAuthorizationHandler.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyAuthorizationHandler.cs
[exemple d’application]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->