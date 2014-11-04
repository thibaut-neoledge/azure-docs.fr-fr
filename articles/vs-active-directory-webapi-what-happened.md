<properties title="Prise en main de l'authentification Active Directory" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Mise en route][Mise en route]
> -   [Que s'est-il passé ?][Que s'est-il passé ?]

### <span id="whathappened">Qu'est-il arrivé à mon projet ?</span>

Des références ont été ajoutées.

##### Références du package NuGet

-   `Microsoft.Owin`
-   `Microsoft.Owin.Host.SystemWeb`
-   `Microsoft.Owin.Security`
-   `Microsoft.Owin.Security.ActiveDirectory`
-   `Microsoft.Owin.Security.Jwt`
-   `Microsoft.Owin.Security.OAuth`
-   `Newtonsoft.Json`
-   `Owin`
-   `System.IdentityModel.Tokens.Jwt`

##### Références .NET

-   `Microsoft.Owin`
-   `Microsoft.Owin.Host.SystemWeb`
-   `Microsoft.Owin.Security`
-   `Microsoft.Owin.Security.ActiveDirectory`
-   `Microsoft.Owin.Security.Jwt`
-   `Microsoft.Owin.Security.OAuth`
-   `Newtonsoft.Json`
-   `Owin`
-   `System.IdentityModel.Tokens.Jwt`

##### Des fichiers de code ont été ajoutés à votre projet

La classe de démarrage d'authentification **App\_Start/Startup.Auth.cs** a été ajoutée à votre projet. Elle contient la logique de démarrage permettant l'authentification Azure AD.

##### Un code de démarrage a été ajouté à votre projet

Si vous disposiez déjà d'une classe de démarrage dans votre projet, la méthode **Configuration** a été mise à jour afin d'inclure un appel vers `ConfigureAuth(app)`. Sinon, une classe de démarrage a été ajoutée à votre projet.

##### Votre fichier app.config ou web.config comporte de nouvelles valeurs de configuration.

Les entrées de configuration ci-dessous ont été ajoutées.

     <appSettings>              <add key="ida:ClientId" value="ClientId from the new Azure AD App" />              <add key="ida:Tenant" value="Your selected Azure AD Tenant" />              <add key="ida:Audience" value="The App ID Uri from the wizard" />      </appSettings> 

</p>
### Une application Azure AD App a été créée

Une application Azure AD a été créée dans le répertoire que vous avez sélectionné dans l'Assistant.

[En savoir plus sur Azure Active Directory][En savoir plus sur Azure Active Directory]

  [Mise en route]: /documentation/articles/vs-active-directory-webapi-getting-started/
  [Que s'est-il passé ?]: /documentation/articles/vs-active-directory-webapi-what-happened/
  [En savoir plus sur Azure Active Directory]: http://azure.microsoft.com/services/active-directory/
