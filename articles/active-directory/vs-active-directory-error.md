<properties 
	pageTitle="Erreur lors de la détection de l'authentification" 
	description="L’Assistant de connexion Active Directory a détecté un type d’authentification incompatible" 
	services="active-directory" 
	documentationCenter="" 
	authors="TomArcher" 
	manager="douge" 
	editor=""/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/15/2016" 
	ms.author="tarcher"/>

# Erreur lors de la détection de l'authentification

L'Assistant a trouvé un type d'authentification incompatible lors de l'opération de détection du code d'authentification précédent.

##Quel est l'objet de la vérification ?

**Remarque :** afin de détecter correctement le code d’authentification précédent dans un projet, le projet doit être généré. Si vous avez rencontré cette erreur et que vous n’avez pas de code d’authentification précédent dans votre projet, régénérez et réessayez.

###Types de projet

L’Assistant vérifie le type de projet que vous développez afin d’injecter la logique d’authentification appropriée au projet. S’il existe un contrôleur qui dérive de `ApiController` dans le projet, le projet est considéré comme un projet WebAPI. S’il existe uniquement des contrôleurs qui dérivent de `MVC.Controller` dans le projet, le projet est considéré comme un projet MVC. Les autres éléments ne sont pas pris en charge par l’assistant. Les projets WebForms ne sont pas pris en charge actuellement.

###Code d’authentification compatible

L’Assistant vérifie également les paramètres d’authentification qui ont été précédemment configurés ou sont compatibles avec celui-ci. Si tous les paramètres sont présents, il est considéré comme réentrant et l'Assistant s'ouvre et affiche les paramètres. Si seuls certains paramètres sont présents, il est considéré comme une erreur.

Dans un projet MVC, l'Assistant vérifie les paramètres suivants, qui résultent de l'utilisation précédente de l'Assistant :

	<add key="ida:ClientId" value="" />
	<add key="ida:Tenant" value="" />
	<add key="ida:AADInstance" value="" />
	<add key="ida:PostLogoutRedirectUri" value="" />

En outre, l'Assistant vérifie les paramètres suivants dans un projet d'API web, qui résultent de l'utilisation précédente de l'Assistant :

	<add key="ida:ClientId" value="" />
	<add key="ida:Tenant" value="" />
	<add key="ida:Audience" value="" />

###Code d’authentification incompatible

Pour terminer, l’Assistant détecte l’utilisation éventuelle de versions du code d’authentification configurées avec des versions antérieures de Visual Studio. Si cette erreur s'affiche, cela signifie que votre projet comporte un type d'authentification incompatible. L'Assistant détecte les types d'authentification suivants, provenant de versions antérieures de Visual Studio :

* Authentification Windows
* Comptes d'utilisateur individuels
* Comptes professionnels
 

Pour détecter l’authentification Windows dans un projet MVC, l’Assistant recherche l’élément `authentication` à partir de votre fichier **web.config**.

<pre>
	&lt;configuration>
	    &lt;system.web>
	        <span style="background-color: yellow">&lt;authentication mode="Windows" /></span>
	    &lt;/system.web>
	&lt;/configuration>
</pre>

Pour détecter l’authentification Windows dans un projet API web, l’Assistant recherche l’élément `IISExpressWindowsAuthentication` à partir de votre fichier **.csproj** :

<pre>
	&lt;Project>
	    &lt;PropertyGroup>
	        <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication>enabled&lt;/IISExpressWindowsAuthentication></span>
	    &lt;/PropertyGroup>
	&lt;/Project>
</pre>

Pour détecter l'authentification des comptes d'utilisateur individuels, l'Assistant recherche l'élément package à partir de votre fichier **Packages.config**.

<pre>
	&lt;packages>
	    <span style="background-color: yellow">&lt;package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /></span>
	&lt;/packages>
</pre>

Pour détecter une ancienne forme d’authentification d’un compte professionnel, l’Assistant recherche l’élément suivant à partir de votre fichier **web.config** :

<pre>
	&lt;configuration>
	    &lt;appSettings>
	        <span style="background-color: yellow">&lt;add key="ida:Realm" value="***" /></span>
	    &lt;/appSettings>
	&lt;/configuration>
</pre>

Pour modifier le type d'authentification, supprimez le type d'authentification incompatible, puis réexécutez l'Assistant.

Pour plus d’informations, consultez la page [Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md).

<!---HONumber=AcomDC_0817_2016-->