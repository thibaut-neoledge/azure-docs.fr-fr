<properties 
	pageTitle="" 
	description="Décrit les modifications que présente votre projet Visual Studio après l’exécution de l’Assistant d’Azure Active Directory" 
	services="active-directory" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>
 
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# Qu'est-il arrivé à mon projet ?

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-webapi-getting-started.md)
> - [What Happened](vs-active-directory-webapi-what-happened.md)

###<span id="whathappened">Qu’est-il arrivé à mon projet ?</span>

Des références ont été ajoutées.

#####Références du package NuGet 

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

#####Références .NET 

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt` 

#####Des fichiers de code ont été ajoutés à votre projet 

La classe de démarrage d'authentification **App_Start/Startup.Auth.cs** a été ajoutée à votre projet. Elle contient la logique de démarrage permettant l'authentification Azure AD.

#####Un code de démarrage a été ajouté à votre projet 

Si vous disposiez déjà d’une classe de démarrage dans votre projet, la méthode **Configuration** a été mise à jour afin d’inclure un appel vers `ConfigureAuth(app)`. Sinon, une classe de démarrage a été ajoutée à votre projet.


#####Votre fichier app.config ou web.config comporte de nouvelles valeurs de configuration.

Les entrées de configuration suivantes ont été ajoutées. <pre> `<appSettings> 
    		<add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
    		<add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
    		<add key="ida:Audience" value="The App ID Uri from the wizard" /> 
	</appSettings>` </pre>

###Une application Azure AD App a été créée 

Une application Azure AD a été créée dans le répertoire que vous avez sélectionné dans l’Assistant.


[En savoir plus sur Azure Active Directory](http://azure.microsoft.com/services/active-directory/)
 

<!---HONumber=62-->