<properties title="Getting Started with Active Directory Authentication" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### Que s'est-il passé ?

Des références ont été ajoutées à votre projet

###### Références du package NuGet

Microsoft.IdentityModel.Protocol.Extensions, Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.Cookies, Microsoft.Owin.Security.OpenIdConnect, Owin, System.IdentityModel.Tokens.Jwt

###### Références .NET

Microsoft.IdentityModel.Protocol.Extensions, Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.Cookies, Microsoft.Owin.Security.OpenIdConnect, Owin, System, System.Data, System.Drawing, System.IdentityModel, System.IdentityModel.Tokens.Jwt, System.Runtime.Serialization

###### Des fichiers de code ont été ajoutés à votre projet

La classe de démarrage d'authentification App\_Start/Startup.Auth.cs a été ajoutée à votre projet. Elle contient la logique de démarrage permettant l'authentification Azure AD. Une classe de contrôleur, Controllers/AccountController.cs, a également été ajoutée. Elle contient les méthodes SignIn() et SignOut(). Enfin, une vue partielle, Views/Shared/\_LoginPartial.cshtml, a été ajoutée. Elle contient un lien d'action pour la fonctionnalité SignIn/SignOut.

###### Un code de démarrage a été ajouté à votre projet

Si vous disposiez déjà d'une classe de démarrage dans votre projet, la méthode Configuration() a été mise à jour afin d'inclure un appel vers ConfigureAuth(app). Sinon, une classe de démarrage a été ajoutée à votre projet.

###### Votre fichier app.config ou web.config comporte de nouvelles valeurs de configuration

Les entrées de configuration ci-dessous ont été ajoutées.

	<pre>
	`<appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
	    <add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
	    <add key="ida:AADInstance" value="https://login.windows.net/{0}" /> 
	    <add key="Ida:PostLogoutRedirectURI" value="Your project start page" /> 
	</appSettings>` </pre>

</p>
###### Une application Azure Active Directory (AD) a été créée

Une application Azure AD a été créée dans le répertoire que vous avez sélectionné dans l'Assistant.

## Prise en main d'Azure Active Directory (AD)

Voici ce que vous pouvez faire avec le code qui a été ajouté.

###### Demander une authentification pour l'accès aux contrôleurs

Tous les contrôleurs de votre projet ont été dotés de l'attribut [Authorize]. Cet attribut permet de demander à l'utilisateur de s'authentifier avant d'accéder à ces contrôleurs. Pour autoriser un accès anonyme au contrôleur, cet attribut doit être supprimé du contrôleur.

###### Ajouter des contrôles SignIn/SignOut

Pour ajouter des contrôles SignIn/SignOut à l'une de vos vues, vous pouvez utiliser la vue partielle \_LoginPartial.cshtml. Voici un exemple où cette fonctionnalité est ajoutée à la vue \_Layout.cshtml standard :

	<pre> 
	`<!DOCTYPE html> 
	<html> 
	<head> 
	    <meta charset="utf-8" /> 
	    <meta name="viewport" content="width=device-width, initial-scale=1.0"> 
	    <title>@ViewBag.Title - My ASP.NET Application</title> 
	    @Styles.Render("~/Content/css") 
	    @Scripts.Render("~/bundles/modernizr") 
	</head> 
	<body> 
	    <div class="navbar navbar-inverse navbar-fixed-top"> 
	        <div class="container"> 
	            <div class="navbar-header"> 
	                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"> 
	                    <span class="icon-bar"></span> 
	                    <span class="icon-bar"></span> 
	                    <span class="icon-bar"></span> 
	                </button> 
	                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) 
	            </div> 
	            <div class="navbar-collapse collapse"> 
	                <ul class="nav navbar-nav"> 
	                    <li>@Html.ActionLink("Home", "Index", "Home")</li> 
	                    <li>@Html.ActionLink("About", "About", "Home")</li> 
	                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li> 
	                </ul> 
	                @Html.Partial("_LoginPartial") 
	            </div> 
	        </div> 
	    </div> 
	    <div class="container body-content"> 
	        @RenderBody() 
	        <hr /> 
	        <footer> 
	            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p> 
	        </footer> 
	    </div> 
	    @Scripts.Render("~/bundles/jquery") 
	    @Scripts.Render("~/bundles/bootstrap") 
	    @RenderSection("scripts", required: false) 
	</body> 
	</html>` </pre>



