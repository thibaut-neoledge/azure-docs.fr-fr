<properties 
	pageTitle="Erreur lors de la détection de l'authentification" 
	description="L'Assistant de connexion Active Directory a détecté un type d'authentification incompatible" 
	services="active-directory" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

# Erreur lors de la détection de l'authentification

L'Assistant a trouvé un type d'authentification incompatible lors de l'opération de détection du code d'authentification précédent.   

###Quel est l'objet de la vérification ?

####Types de projet

L'Assistant vérifie le type de projet que vous développez afin d'injecter la logique d'authentification appropriée au projet.  S'il existe un contrôleur qui dérive de `ApiController` dans le projet, celui-ci est considéré comme un projet WebAPI.  S'il existe uniquement des contrôleurs qui dérivent de `MVC.Controller` dans le projet, celui-ci est considéré comme un projet MVC.  Tout autre élément est considéré comme non pris en charge par l'Assistant.  Les projets WebForms ne sont pas pris en charge actuellement.

#####Code d'authentification compatible

L'Assistant vérifie également les paramètres d'authentification qui ont été précédemment configurés ou sont compatibles avec celui-ci.  Si tous les paramètres sont présents, il est considéré comme réentrant et l'Assistant s'ouvre et affiche les paramètres.  Si seuls certains paramètres sont présents, il est considéré comme une erreur.

Dans un projet MVC, l'Assistant vérifie les paramètres suivants, qui résultent de l'utilisation précédente de l'Assistant :

	<add key="ida:ClientId" value="" />
	<add key="ida:Tenant" value="" />
	<add key="ida:AADInstance" value="" />
	<add key="ida:PostLogoutRedirectUri" value="" />

En outre, l'Assistant vérifie les paramètres suivants dans un projet d'API web, qui résultent de l'utilisation précédente de l'Assistant :

	<add key="ida:ClientId" value="" />
	<add key="ida:Tenant" value="" />
	<add key="ida:Audience" value="" />

#####Code d'authentification incompatible

Pour terminer, l'Assistant détecte l'utilisation éventuelle de versions du code d'authentification configurées avec des versions antérieures de Visual Studio. Si cette erreur s'affiche, cela signifie que votre projet comporte un type d'authentification incompatible. L'Assistant détecte les types d'authentification suivants, provenant de versions antérieures de Visual Studio :

* Authentification Windows 
* Comptes d'utilisateur individuels 
* Comptes professionnels 
 

Pour détecter l'authentification Windows dans un projet MVC, l'Assistant recherche l'élément `authentication` à partir de votre fichier **web.config**.

<PRE class="prettyprint">
	&lt;configuration&gt;
	    &lt;system.web&gt;
	        <span style="background-color: yellow">&lt;authentication mode="Windows" /&gt;</span>
	    &lt;/system.web&gt;
	&lt;/configuration&gt;
</pre>

Pour détecter l'authentification Windows dans un projet API web, l'Assistant recherche l'élément `IISExpressWindowsAuthentication` à partir de votre fichier **.csproj** :

<PRE class="prettyprint">
	&lt;Project&gt;
	    &lt;PropertyGroup&gt;
	        <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;</span>
	    &lt;/PropertyGroup>
	&lt;/Project&gt;
</PRE>

Pour détecter l'authentification des comptes d'utilisateur individuels, l'Assistant recherche l'élément package à partir de votre fichier **Packages.config**.

<PRE class="prettyprint">
	&lt;packages&gt;
	    <span style="background-color: yellow">&lt;package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /&gt;</span>
	&lt;/packages&gt;
</PRE>

Pour détecter une ancienne forme d'authentification d'un compte professionnel, l'Assistant recherche l'élément suivant à partir de votre fichier **web.config** :

<PRE class="prettyprint">
	&lt;configuration*gt;
	    &lt;appSettings&gt;
	        <span style="background-color: yellow">&lt;add key="ida:Realm" value="***" /&gt;</span>
	    &lt;/appSettings&gt;
	&lt;/configuration&gt;
</PRE>

Pour modifier le type d'authentification, supprimez le type d'authentification incompatible, puis réexécutez l'Assistant.

Pour plus d'informations, consultez la page [Scénarios d'authentification pour Azure AD](http://msdn.microsoft.com/library/azure/dn499820.aspx).

<!--HONumber=49--> 