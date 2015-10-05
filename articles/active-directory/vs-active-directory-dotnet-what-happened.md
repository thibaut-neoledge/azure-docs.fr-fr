<properties
	pageTitle="Qu'est-il arrivé à mon projet MVC (service connecté Azure Active Directory Visual Studio) ? | Microsoft Azure"
	description="Décrit les conséquences sur votre projet MVC lorsque vous vous connectez à Azure AD à l'aide des services connectés Visual Studio"
	services="active-directory"
	documentationCenter="na"
	authors="patshea123"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="active-directory"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/03/2015"
	ms.author="patshea"/>

# Qu'est-il arrivé à mon projet MVC (service connecté Azure Active Directory Visual Studio) ?

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-dotnet-getting-started.md)
> - [What Happened](vs-active-directory-dotnet-what-happened.md)



## Des références ont été ajoutées.

### Références du package NuGet

- **Microsoft.IdentityModel.Protocol.Extensions**
- **Microsoft.Owin**
- **Microsoft.Owin.Host.SystemWeb**
- **Microsoft.Owin.Security**
- **Microsoft.Owin.Security.Cookies**
- **Microsoft.Owin.Security.OpenIdConnect**
- **Owin**
- **System.IdentityModel.Tokens.Jwt**

### Références .NET

- **Microsoft.IdentityModel.Protocol.Extensions**
- **Microsoft.Owin**
- **Microsoft.Owin.Host.SystemWeb**
- **Microsoft.Owin.Security**
- **Microsoft.Owin.Security.Cookies**
- **Microsoft.Owin.Security.OpenIdConnect**
- **Owin**
- **System.IdentityModel**
- **System.IdentityModel.Tokens.Jwt**
- **System.Runtime.Serialization**

## Du code a été ajouté.

### Des fichiers de code ont été ajoutés à votre projet

La classe de démarrage d'authentification **App\_Start/Startup.Auth.cs** a été ajoutée à votre projet. Elle contient la logique de démarrage permettant l'authentification Azure AD. Une classe de contrôleur, Controllers/AccountController.cs, a également été ajoutée. Elle contient les méthodes **SignIn()** et **SignOut()**. Enfin, une vue partielle, **Views/Shared/\_LoginPartial.cshtml**, a été ajoutée. Elle contient un lien d'action pour la fonctionnalité SignIn/SignOut.

### Un code de démarrage a été ajouté à votre projet

Si vous disposiez déjà d'une classe de démarrage dans votre projet, la méthode **Configuration** a été mise à jour afin d'inclure un appel vers **ConfigureAuth(app)**. Sinon, une classe de démarrage a été ajoutée à votre projet.

### Votre fichier app.config ou web.config comporte de nouvelles valeurs de configuration

Les entrées de configuration ci-dessous ont été ajoutées.


	<appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
	    <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
	    <add key="ida:Domain" value="The selected Azure AD Domain" />
	    <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
	    <add key="ida:PostLogoutRedirectUri" value="Your project start page" />
	</appSettings>

### Une application Azure Active Directory (AD) a été créée
Une application Azure AD a été créée dans le répertoire que vous avez sélectionné dans l’Assistant.

##Si j'ai coché *Désactiver l'authentification des comptes d'utilisateur individuels*, quelles autres modifications ont été apportées à mon projet ?
Des références du package NuGet ont été supprimées, et des fichiers ont été supprimés et sauvegardés. Selon l’état de votre projet, vous pouvez avoir besoin de supprimer manuellement d’autres références ou fichiers, ou de modifier le code le cas échéant.

### Références du package NuGet supprimées (pour celles présentes)

- **Microsoft.AspNet.Identity.Core**
- **Microsoft.AspNet.Identity.EntityFramework**
- **Microsoft.AspNet.Identity.Owin**

### Fichiers de code sauvegardés et supprimés (pour ceux présents)

Chacun des fichiers suivants a été sauvegardé et supprimé du projet. Les fichiers de sauvegarde sont situés dans un dossier « Backup » à la racine du répertoire du projet.

- **App\_Start\\IdentityConfig.cs**
- **Controllers\\ManageController.cs**
- **Models\\IdentityModels.cs**
- **Models\\ManageViewModels.cs**

### Fichiers de code sauvegardés (pour ceux présents)

Chacun des fichiers suivants a été sauvegardé avant d’être remplacé. Les fichiers de sauvegarde sont situés dans un dossier « Backup » à la racine du répertoire du projet.

- **Startup.cs**
- **App\_Start\\Startup.Auth.cs**
- **Controllers\\AccountController.cs**
- **Views\\Shared\_LoginPartial.cshtml**

## Si j'ai coché *Lire les données de l'annuaire*, quelles autres modifications ont été apportées à mon projet ?

Des références supplémentaires ont été ajoutées.

###Références supplémentaires du package NuGet

- **EntityFramework**
- **Microsoft.Azure.ActiveDirectory.GraphClient**
- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.IdentityModel.Clients.ActiveDirectory**
- **System.Spatial**

###Références .NET supplémentaires

- **EntityFramework**
- **EntityFramework.SqlServer**
- **Microsoft.Azure.ActiveDirectory.GraphClient**
- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.IdentityModel.Clients.ActiveDirectory**
- **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms**
- **System.Spatial**

###Des fichiers de code supplémentaires ont été ajoutés à votre projet

Deux fichiers ont été ajoutés pour prendre en charge la mise en cache de jeton : **Models\\ADALTokenCache.cs** et **Models\\ApplicationDbContext.cs**. Un contrôleur et une vue supplémentaires ont été ajoutés pour illustrer l’accès aux informations de profil utilisateur à l’aide des API graphiques Azure. Ces fichiers sont **Controllers\\UserProfileController.cs** et **Views\\UserProfile\\Index.cshtml**.

###Un code de démarrage a été ajouté à votre projet

Dans le fichier **startup.auth.cs**, un nouvel objet **OpenIdConnectAuthenticationNotifications** a été ajouté au membre **Notifications** de **OpenIdConnectAuthenticationOptions**. Cette opération consiste à activer la réception du code OAuth reçu et à l’échanger contre un jeton d’accès.

###Des modifications supplémentaires ont été apportées à votre fichier app.config ou web.config

Les entrées de configuration ci-dessous ont été ajoutées.

	<appSettings>
	    <add key="ida:ClientSecret" value="Your Azure AD App's new client secret" />
	</appSettings>

Les sections de configuration et la chaîne de connexion suivantes ont été ajoutées.

	<configSections>
	    <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
	    <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
	</configSections>
	<connectionStrings>
	    <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-[AppName + Generated Id].mdf;Initial Catalog=aspnet-[AppName + Generated Id];Integrated Security=True" providerName="System.Data.SqlClient" />
	</connectionStrings>
	<entityFramework>
	    <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
	      <parameters>
	        <parameter value="mssqllocaldb" />
	      </parameters>
	    </defaultConnectionFactory>
	    <providers>
	      <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
	    </providers>
	</entityFramework>


###Votre application Azure Active Directory a été mise à jour
Votre application Azure Active Directory a été mise à jour pour inclure l'autorisation *Lire les données de l'annuaire*, et une clé supplémentaire a été créée pour être ensuite utilisée comme *ida:ClientSecret* dans le fichier **web.config**.

[En savoir plus sur Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

<!---HONumber=Sept15_HO4-->