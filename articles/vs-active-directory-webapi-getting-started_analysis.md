<properties title="Getting Started with Active Directory Authentication" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### Que s'est-il passé ?

Des références ont été ajoutées à votre projet

###### Références du package NuGet

Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.ActiveDirectory, Microsoft.Owin.Security.Jwt, Microsoft.Owin.Security.OAuth, Newtonsoft.Json, Owin, System.IdentityModel.Tokens.Jwt

###### Références .NET

Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.ActiveDirectory, Microsoft.Owin.Security.Jwt, Microsoft.Owin.Security.OAuth, Newtonsoft.Json, Owin, System.IdentityModel.Tokens.Jwt

###### Des fichiers de code ont été ajoutés à votre projet

La classe de démarrage d'authentification App\_Start/Startup.Auth.cs a été ajoutée à votre projet. Elle contient la logique de démarrage permettant l'authentification Azure AD.

###### Un code de démarrage a été ajouté à votre projet

Si vous disposiez déjà d'une classe de démarrage dans votre projet, la méthode Configuration() a été mise à jour afin d'inclure un appel vers ConfigureAuth(app). Sinon, une classe de démarrage a été ajoutée à votre projet.

###### Votre fichier app.config ou web.config comporte de nouvelles valeurs de configuration.

Les entrées de configuration ci-dessous ont été ajoutées.

	<pre>
	`<appSettings> 
    		<add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
    		<add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
    		<add key="ida:Audience" value="The App ID Uri from the wizard" /> 
	</appSettings>` </pre>

### Une application Azure Active Directory (AD) a été créée

Une application Azure AD a été créée dans le répertoire que vous avez sélectionné dans l'Assistant.

### Prise en main d'Azure Active Directory (AD)

Voici ce que vous pouvez faire avec le code qui a été ajouté.

###### Demander une authentification pour l'accès aux contrôleurs

Tous les contrôleurs de votre projet ont été dotés de l'attribut Authorize. Cet attribut permet de demander à l'utilisateur de s'authentifier avant d'accéder aux API définies par ces contrôleurs. Pour autoriser un accès anonyme au contrôleur, cet attribut doit être supprimé du contrôleur. Pour définir plus précisément les autorisations, appliquez cet attribut à chaque méthode nécessitant une autorisation, au lieu de l'appliquer à la classe de contrôleur.

