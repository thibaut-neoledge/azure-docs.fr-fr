<properties title="Erreur lors de la d&eacute;tection de l'authentification" pageTitle="Erreur lors de la d&eacute;tection de l'authentification" metaKeywords="" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

### Erreur lors de la détection de l'authentification

L'Assistant a trouvé un type d'authentification incompatible lors de l'opération de détection du code d'authentification précédent.

##### Quel est l'objet de la vérification ?

L'Assistant détecte l'utilisation éventuelle de versions du code d'authentification configurées avec des versions antérieures de Visual Studio. Si cette erreur s'affiche, cela signifie que votre projet comporte un type d'authentification incompatible. L'Assistant détecte les types d'authentification suivants, provenant de versions antérieures de Visual Studio :

-   Authentification Windows
-   Comptes d'utilisateur individuels
-   Comptes professionnels

Pour détecter l'authentification Windows dans un projet MVC, l'Assistant recherche l'élément `authentication` à partir de votre fichier **web.config**.

<pre class="prettyprint">
    &lt;configuration&gt;
        &lt;system.web&gt;
            &lt;authentication mode=&quot;Windows&quot; /&gt;
        &lt;/system.web&gt;
    &lt;/configuration&gt;
</pre>

Pour détecter l'authentification Windows dans un projet API web, l'Assistant recherche l'élément `IISExpressWindowsAuthentication` à partir de votre fichier **.csproj** :

<pre class="prettyprint">
    &lt;Project&gt;
        &lt;PropertyGroup&gt;
            &lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;
        &lt;/PropertyGroup&gt;
    &lt;/Project&gt;
</pre>

Pour détecter l'authentification des comptes d'utilisateur individuels, l'Assistant recherche l'élément package à partir de votre fichier **Packages.config**.

<pre class="prettyprint">
    &lt;packages&gt;
        &lt;package id=&quot;Microsoft.AspNet.Identity.EntityFramework&quot; version=&quot;2.1.0&quot; targetFramework=&quot;net45&quot; /&gt;
    &lt;/packages&gt;
</pre>

Pour détecter une ancienne forme d'authentification d'un compte professionnel, l'Assistant recherche l'élément suivant à partir de votre fichier **web.config** :

<pre class="prettyprint">
    &lt;configuration*gt;
        &lt;appSettings&gt;
            &lt;add key=&quot;ida:Realm&quot; value=&quot;***&quot; /&gt;
        &lt;/appSettings&gt;
    &lt;/configuration&gt;
</pre>

Pour modifier le type d'authentification, supprimez le type d'authentification incompatible, puis réexécutez l'Assistant.

Pour plus d'informations, consultez la page [Scénarios d'authentification pour Azure AD][Scénarios d'authentification pour Azure AD].

  [Scénarios d'authentification pour Azure AD]: http://msdn.microsoft.com/library/azure/dn499820.aspx
