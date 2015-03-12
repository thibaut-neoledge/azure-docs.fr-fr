<properties 
	pageTitle="Prise en main de l'authentification Active Directory - Que s'est-il passé ?" 
	description="Décrit ce qui est arrivé à votre projet Azure Active Directory dans Visual Studio" 
	services="active-directory" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Mise en route](/documentation/articles/vs-active-directory-dotnet-getting-started/)
> - [Que s'est-il passé ?](/documentation/articles/vs-active-directory-dotnet-what-happened/)

###<span id="whathappened">Qu'est-il arrivé à mon projet ?</span>
 
Des références ont été ajoutées.

#####Références du package NuGet

- Microsoft.IdentityModel.Protocol.Extensions
- Microsoft.Owin
- Microsoft.Owin.Host.SystemWeb
- Microsoft.Owin.Security
- Microsoft.Owin.Security.Cookies
- Microsoft.Owin.Security.OpenIdConnect
- Owin
- System.IdentityModel.Tokens.Jwt

#####Références .NET

- Microsoft.IdentityModel.Protocol.Extensions
- Microsoft.Owin
- Microsoft.Owin.Host.SystemWeb
- Microsoft.Owin.Security
- Microsoft.Owin.Security.Cookies
- Microsoft.Owin.Security.OpenIdConnect
- Owin
- System
- System.Data
- System.Drawing
- System.IdentityModel
- System.IdentityModel.Tokens.Jwt
- System.Runtime.Serialization

#####Des fichiers de code ont été ajoutés à votre projet 

La classe de démarrage d'authentification App_Start/Startup.Auth.cs a été ajoutée à votre projet. Elle contient la logique de démarrage permettant l'authentification Azure AD. La classe de contrôleur Controllers/AccountController.cs a également été ajoutée. Elle contient les méthodes SignIn() et SignOut(). Enfin, la vue partielle Views/Shared/_LoginPartial.cshtml a été ajoutée. Elle contient un lien d'action pour la fonctionnalité SignIn/SignOut. 

#####Un code de démarrage a été ajouté à votre projet
 
Si vous disposiez déjà d'une classe de démarrage dans votre projet, la méthode **Configuration** a été mise à jour afin d'inclure un appel vers ConfigureAuth(app). Sinon, une classe de démarrage a été ajoutée à votre projet. 

#####Votre fichier app.config ou web.config comporte de nouvelles valeurs de configuration 

Les entrées de configuration ci-dessous ont été ajoutées. 
	<pre>
	`<appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
	    <add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
	    <add key="ida:AADInstance" value="https://login.windows.net/{0}" /> 
	    <add key="Ida:PostLogoutRedirectURI" value="Your project start page" /> 
	</appSettings>` </pre>

#####Une application Azure Active Directory (AD) a été créée 
Une application Azure AD a été créée dans le répertoire que vous avez sélectionné dans l'Assistant. 

[En savoir plus sur Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

<!--HONumber=46--> 
