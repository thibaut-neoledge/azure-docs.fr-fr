<properties pageTitle="Qu’est-il arrivé à mon projet WebApi (service connecté Azure Active Directory de Visual Studio) ? |Microsoft Azure " description="Décrit ce qui arrive à votre projet WebApi MVC quand vous vous connectez à Azure AD à l’aide de services Visual Studio="active-directory" services="active-directory" services="active-directory" documentationCenter="" authors="TomArcher" manager="douge" editor="tglee"/>

<tags
	ms.service="active-directory"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/03/2015"
	ms.author="tarcher"/>

# Qu’est-il arrivé à mon projet WebApi (service connecté Azure Active Directory de Visual Studio) ?

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-webapi-getting-started.md)
> - [What Happened](vs-active-directory-webapi-what-happened.md)

##Des références ont été ajoutées.

###Références du package NuGet

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

###Références .NET

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

##Modifications du code

###Des fichiers de code ont été ajoutés à votre projet

La classe de démarrage d'authentification **App\_Start/Startup.Auth.cs** a été ajoutée à votre projet. Elle contient la logique de démarrage permettant l'authentification Azure AD.

###Un code de démarrage a été ajouté à votre projet

Si vous disposiez déjà d’une classe de démarrage dans votre projet, la méthode **Configuration** a été mise à jour afin d’inclure un appel vers `ConfigureAuth(app)`. Sinon, une classe de démarrage a été ajoutée à votre projet.


###Votre fichier app.config ou web.config comporte de nouvelles valeurs de configuration.

Les entrées de configuration suivantes ont été ajoutées. ```
	`<appSettings>
    		<add key="ida:ClientId" value="ClientId from the new Azure AD App" />
    		<add key="ida:Tenant" value="Your selected Azure AD Tenant" />
    		<add key="ida:Audience" value="The App ID Uri from the wizard" />
	</appSettings>`
```

###Une application Azure AD App a été créée

Une application Azure AD a été créée dans le répertoire que vous avez sélectionné dans l’Assistant.

[En savoir plus sur Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

##Si j’ai coché *Désactiver l’authentification des comptes d’utilisateur individuels*, quelles autres modifications ont été apportées à mon projet ?
Des références du package NuGet ont été supprimées, et des fichiers ont été supprimés et sauvegardés. Selon l’état de votre projet, vous pouvez avoir besoin de supprimer manuellement d’autres références ou fichiers, ou de modifier le code le cas échéant.

###Références du package NuGet supprimées (pour celles présentes)

- `Microsoft.AspNet.Identity.Core`
- `Microsoft.AspNet.Identity.EntityFramework`
- `Microsoft.AspNet.Identity.Owin`

###Fichiers de code sauvegardés et supprimés (pour ceux présents)

Chacun des fichiers suivants a été sauvegardé et supprimé du projet. Les fichiers de sauvegarde sont situés dans un dossier « Backup » à la racine du répertoire du projet.

- `App_Start\IdentityConfig.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Providers\ApplicationOAuthProvider.cs`

###Fichiers de code sauvegardés (pour ceux présents)

Chacun des fichiers suivants a été sauvegardé avant d’être remplacé. Les fichiers de sauvegarde sont situés dans un dossier « Backup » à la racine du répertoire du projet.

- `Startup.cs`
- `App_Start\Startup.Auth.cs`

##Si j’ai coché *Lire les données de l’annuaire*, quelles autres modifications ont été apportées à mon projet ?

###Des modifications supplémentaires ont été apportées à votre fichier app.config ou web.config

Les entrées de configuration ci-dessous ont été ajoutées.

```
	`<appSettings>
	    <add key="ida:Password" value="Your Azure AD App's new password" />
	</appSettings>`
```

###Votre application Azure Active Directory a été mise à jour
Votre application Azure Active Directory a été mise à jour pour inclure l’autorisation *Lire les données de l’annuaire*, et une clé supplémentaire a été créée pour être ensuite utilisée comme *ida:Password* dans le fichier `web.config`.

[En savoir plus sur Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

<!---HONumber=Nov15_HO3-->