<properties 
	pageTitle="Créer une application web .NET MVC dans Azure App Service avec authentification AD FS" 
	description="Apprenez à créer une application métier ASP.NET MVC dans Azure App Service Web Apps qui s’authentifie avec STS sur site. Ce didacticiel cible AD FS comme service STS local." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="04/09/2015" 
	ms.author="cephalin"/>

# Créer une application web .NET MVC dans Azure App Service avec authentification AD FS

Dans cet article, vous apprendrez à créer une application métier ASP.NET MVC dans [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) à l’aide de [services AD FS (Active Directory Federation Services)](http://technet.microsoft.com/library/hh831502.aspx) sur site comme fournisseur d’identité. Ce scénario peut fonctionner lorsque vous souhaitez créer des applications métier dans Azure App Service Web Apps, mais que votre organisation a besoin que toutes les données soient stockées localement.

>[AZURE.NOTE]Pour avoir une vue d’ensemble des différentes options d’authentification et d’autorisation d’entreprise pour Azure App Service Web Apps, consultez la rubrique [Utiliser Active Directory pour l’authentification dans Azure App Service](web-sites-authentication-authorization.md).

<a name="bkmk_build"></a>
## Ce que vous allez créer ##

Vous allez générer une application ASP.NET de base dans Azure App Service Web Apps avec les fonctionnalités suivantes :

- Authentification des utilisateurs auprès d'AD FS
- Utilisation de `[Authorize]` pour autoriser les utilisateurs à effectuer différentes actions
- Configuration statique pour le débogage dans Visual Studio et la publication dans App Service Web Apps (configuration unique, débogage et publication à tout moment)  

<a name="bkmk_need"></a>
## Éléments requis ##

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

Vous devez disposer des éléments suivants pour suivre ce didacticiel :

- Déploiement AD FS local (pour une procédure de bout en bout du laboratoire de test que j'utilise, consultez la rubrique [Laboratoire de test : service STS autonome avec AD FS dans les machines virtuelles Azure (à des fins de test uniquement)](TODO))
- Autorisations pour créer des approbations de partie de confiance dans Gestion AD FS
- Visual Studio 2013
- Le [Kit de développement logiciel (SDK) Azure 2.5.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) ou une version ultérieure

<a name="bkmk_sample"></a>
## Utiliser l’exemple d’application pour le modèle métier ##

L’exemple d’application de ce didacticiel, [WebApp-WSFederation-DotNet)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet), est créé par l’équipe Azure Active Directory. Étant donné qu’AD FS prend en charge WS-Federation, vous pouvez l’utiliser en tant que modèle pour créer facilement de nouvelles applications métier. Il présente les caractéristiques suivantes :

- Utilisation de [WS-Federation](http://msdn.microsoft.com/library/bb498017.aspx) pour l’authentification avec un déploiement AD FS local
- Fonctionnalités de connexion et de déconnexion
- Utilisation de [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) (au lieu de Windows Identity Foundation, ou WIF), qui représente le futur d’ASP.NET et qui est beaucoup plus simple à configurer que WIF pour l’authentification et l’autorisation

<a name="bkmk_setup"></a>
## Configurer l'exemple d'application ##

2.	Clonez ou téléchargez l’exemple de solution à la page [WebApp-WSFederation-DotNet](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) dans votre répertoire local.

	> [AZURE.NOTE]Bien que les instructions de la section [README.md](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet/blob/master/README.md) vous montrent comment configurer l'application avec Azure Active Directory, dans ce didacticiel, vous la configurerez avec AD FS. Par conséquent, suivez les étapes ci-dessous à la place.

3.	Ouvrez la solution, puis ouvrez Controllers\AccountController.cs dans l'**Explorateur de solutions**.

	Vous verrez que le code envoie simplement une demande d'authentification pour authentifier l'utilisateur à l'aide de WS-Federation. Toute l'authentification est configurée dans App_Start\Startup.Auth.cs.

4.  Ouvrez App_Start\Startup.Auth.cs. Dans la méthode `ConfigureAuth`, notez la ligne :

        app.UseWsFederationAuthentication(
            new WsFederationAuthenticationOptions
            {
                Wtrealm = realm,
                MetadataAddress = metadata                                      
            });

	Dans le contexte d'OWIN, il s'agit véritablement du strict minimum dont vous avez besoin pour configurer l'authentification WS-Federation. Cela est plus simple et plus « élégant » que WIF, où Web.config est injecté avec du code XML à différents endroits. Les seules informations dont vous avez besoin sont l'identificateur de la partie de confiance et l'URL du fichier de métadonnées de votre service AD FS. Voici un exemple :

	-	Identificateur de la partie de confiance : `https://contoso.com/MyLOBApp`
	-	Adresse des métadonnées : `http://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

5.	Dans App_Start\Startup.Auth.cs, modifiez les définitions de chaînes statiques comme illustré ci-dessous :
	<pre class="prettyprint">
private static string realm = ConfigurationManager.AppSettings["ida:<mark>RPIdentifier</mark>"];
<mark><del>private static string aadInstance = ConfigurationManager.AppSettings["ida:AADInstance"];</del></mark>
<mark><del>private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];</del></mark>
<mark><del>private static string metadata = string.Format("{0}/{1}/federationmetadata/2007-06/federationmetadata.xml", aadInstance, tenant);</del></mark>
<mark>private static string metadata = string.Format("https://{0}/federationmetadata/2007-06/federationmetadata.xml", ConfigurationManager.AppSettings["ida:ADFS"]);</mark>

<mark><del>string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant);</del></mark>
</pre>

6.	Vous allez maintenant effectuer les modifications correspondantes dans le fichier Web.config. Ouvrez le fichier Web.config et modifiez les paramètres d'application comme illustré ci-dessous :
	<pre class="prettyprint">
&lt;appSettings>
  &lt;add key="webpages:Version" value="3.0.0.0" />
  &lt;add key="webpages:Enabled" value="false" />
  &lt;add key="ClientValidationEnabled" value="true" />
  &lt;add key="UnobtrusiveJavaScriptEnabled" value="true" />
  <mark><del>&lt;add key="ida:Wtrealm" value="[Entrez l’URI ID d’application de WebApp-WSFederation-DotNet https://contoso.onmicrosoft.com/WebApp-WSFederation-DotNet]" /></del></mark>
  <mark><del>&lt;add key="ida:AADInstance" value="https://login.windows.net" /></del></mark>
  <mark><del>&lt;add key="ida:Tenant" value="[Entrez le nom du client, par exemple contoso.onmicrosoft.com]" /></del></mark>
  <mark>&lt;add key="ida:RPIdentifier" value="[Entrez l’identifiant de la partie de confiance tel que configuré dans AD&#160;FS, par exemple https://localhost:44320/]" /></mark>
  <mark>&lt;add key="ida:ADFS" value="[Entrez le nom de domaine complet du service AD&#160;FS, par exemple adfs.contoso.com]" /></mark>

&lt;/appSettings>
</pre>Renseignez les valeurs de clé en fonction de votre environnement respectif.

7.	Générez l'application pour vous assurer qu'aucune erreur n'est détectée.

Vous avez terminé. L'exemple d'application est maintenant prêt à fonctionner avec AD FS. Vous devrez toujours prochainement configurer une approbation de partie de confiance avec cette application dans AD FS.

<a name="bkmk_deploy"></a>
## Déployer l’exemple d’application sur Azure App Service Web Apps

Ici, vous allez publier l’application dans une application web d’App Service Web Apps tout en préservant l’environnement de débogage. Notez que vous allez publier l'application avant qu'elle ait une approbation de partie de confiance avec AD FS. L'authentification ne fonctionne donc pas encore. Toutefois, si vous le faites maintenant, vous pouvez obtenir l’URL de l’application web que vous utiliserez également pour configurer l’approbation de partie de confiance ultérieurement.

1. Cliquez avec le bouton droit sur votre projet et sélectionnez **Publier**.

	![](./media/web-sites-dotnet-lob-application-adfs/01-publish-website.png)

2. Sélectionnez **Microsoft Azure Web Apps**.
3. Si vous n’êtes pas connecté à Azure, cliquez sur **Se connecter** et utilisez le compte Microsoft de votre abonnement Azure.
4. Une fois connecté, cliquez sur **Nouveau** pour créer une application Web.
5. Renseignez tous les champs obligatoires. Comme vous vous connecterez plus tard à des données locales, vous n’allez pas créer de base de données pour cette application web.

	![](./media/web-sites-dotnet-lob-application-adfs/02-create-website.png)

6. Cliquez sur **Create**. Une fois l’application web créée, la boîte de dialogue Publier le site web s’ouvre.
7. Dans **URL de destination**, remplacez **http** par **https**. Copiez l’URL entière dans un éditeur de texte. Vous allez l’utiliser ultérieurement. Cliquez ensuite sur **Publier**.

	![](./media/web-sites-dotnet-lob-application-adfs/03-destination-url.png)

11. Dans Visual Studio, ouvrez **Web.Release.config** dans votre projet. Insérez le code XML suivant dans la balise `<configuration>`, puis remplacez la valeur de clé par l’URL de votre application Web de publication.
	<pre class="prettyprint">
&lt;appSettings>
   &lt;add key="ida:RPIdentifier" value="<mark>[e.g. https://mylobapp.azurewebsites.net/]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
&lt;/appSettings></pre>

Lorsque vous avez terminé, deux identificateurs de partie de confiance sont configurés dans votre projet, un pour votre environnement de débogage dans Visual Studio et un pour l’application web publiée dans Azure. Vous allez configurer une approbation de partie de confiance pour chacun des deux environnements dans AD FS. Pendant le débogage, les paramètres d'application du fichier Web.config sont utilisés pour que votre configuration **Debug** fonctionne avec AD FS, et après la publication (par défaut, la configuration de **Release** est publiée), un fichier Web.config transformé est téléchargé et il intègre les modifications de paramètre d'application dans Web.Release.config.

Si vous voulez attacher l’application web publiée dans Azure au débogueur (dans ce cas, vous devez télécharger les symboles de débogage de votre code dans l’application web publiée), vous pouvez créer un clone de la configuration Debug pour le débogage Azure, mais avec sa propre transformation Web.config personnalisée (par exemple, Web.AzureDebug.config) qui utilise les paramètres d’application du fichier Web.Release.config. Cela vous permet de maintenir une configuration statique entre les différents environnements.

<a name="bkmk_rptrusts"></a>
## Configuration d'approbations de partie de confiance dans Gestion AD FS ##

Maintenant, vous devez configurer une approbation de partie de confiance dans Gestion AD FS avant que votre exemple d'application puisse s'authentifier auprès d'AD FS. Vous devez configurer deux approbations de partie de confiance distinctes, une pour votre environnement de débogage et une pour votre application web publiée.

> [AZURE.NOTE]Assurez-vous que vous répétez les étapes ci-dessous pour les deux environnements.

4.	Sur votre serveur AD FS, connectez-vous avec les informations d'identification disposant de droits de gestion pour AD FS.
5.	Ouvrez Gestion AD FS. Cliquez avec le bouton droit sur **AD FS\Relations de confiance\Approbations de partie de confiance** et sélectionnez **Ajouter une approbation de partie de confiance**.

	![](./media/web-sites-dotnet-lob-application-adfs/1-add-rptrust.png)

5.	Dans la page **Sélectionner une source de données**, sélectionnez **Entrer manuellement les données relatives à la partie de confiance**.

	![](./media/web-sites-dotnet-lob-application-adfs/2-enter-rp-manually.png)

6.	Dans la page **Indiquer le nom complet**, tapez un nom complet pour l'application, puis cliquez sur **Suivant**.
7.	Dans la page **Choisir le protocole**, cliquez sur **Suivant**.
8.	Dans la page **Configurer le certificat**, cliquez sur **Suivant**.

	> [AZURE.NOTE]Étant donné que vous utilisez déjà HTTPS, les jetons chiffrés sont facultatifs. Si vous voulez vraiment chiffrer les jetons d'AD FS sur cette page, vous devez également ajouter une logique de déchiffrement de jetons dans votre code. Pour plus d'informations, consultez la page [Configuration manuelle de l'intergiciel (middleware) OWIN WS-Federation et acceptation des jetons chiffrés](http://chris.59north.com/post/2014/08/21/Manually-configuring-OWIN-WS-Federation-middleware-and-accepting-encrypted-tokens.aspx).
  
5.	Avant de passer à l'étape suivante, vous avez besoin d'un élément de votre projet Visual Studio. Dans les propriétés du projet, notez l'**URL SSL** de l'application.

	![](./media/web-sites-dotnet-lob-application-adfs/3-ssl-url.png)

6.	De retour dans Gestion AD FS, dans la page **Configurer l'URL** de l'**Assistant Ajouter une approbation de partie de confiance**, sélectionnez **Prendre en charge le protocole WS-Federation Passive** et tapez l'URL SSL de votre projet Visual Studio que vous avez notée à l'étape précédente. Cliquez ensuite sur **Suivant**.

	![](./media/web-sites-dotnet-lob-application-adfs/4-configure-url.png)

	> [AZURE.NOTE]L'URL spécifie où envoyer le client après la réussite de l'authentification. Pour l'environnement de débogage, elle doit être : <code>https://localhost:&lt;port&gt;/</code>. Pour l’application web publiée, elle doit être l’URL d’application web.

7.	Dans la page **Configurer les identificateurs**, vérifiez que l'URL SSL de votre projet est déjà répertoriée, puis cliquez sur **Suivant**. Cliquez sur **Suivant** en conservant les sélections par défaut jusqu'à la fin de l'Assistant.

	> [AZURE.NOTE]Dans App_Start\Startup.Auth.cs de votre projet Visual Studio, cet identificateur est comparé à la valeur de <code>WsFederationAuthenticationOptions.Wtrealm</code> au cours de l’authentification fédérée. Par défaut, l'URL de l'application de l'étape précédente est ajoutée comme identificateur de la partie de confiance.

8.	Vous avez maintenant terminé la configuration de l'application de la partie de confiance pour votre projet dans AD FS. Ensuite, vous allez configurer cette application pour envoyer les revendications requises par votre application. La boîte de dialogue **Modifier les règles de revendication** s'ouvre par défaut à la fin de l'Assistant. Vous pouvez ainsi commencer sans attendre. Nous allons configurer au moins les déclarations suivantes (avec les schémas entre parenthèses) :

	-	Nom (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name) - utilisé par ASP.NET pour alimenter `User.Identity.Name`.
	-	Nom d'utilisateur principal (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn) - permet d'identifier de manière unique les utilisateurs de l'organisation.
	-	Appartenances de groupe en tant que rôles (http://schemas.microsoft.com/ws/2008/06/identity/claims/role) - peut être utilisé avec `[Authorize(Roles="role1, role2,...")]` pour autoriser les contrôleurs et les actions. En réalité, il se peut que cela ne soit pas l'approche la plus performante pour l'autorisation de rôle, surtout si vos utilisateurs Active Directory appartiennent régulièrement à des centaines de groupes de sécurité, et donc à des centaines de revendications de rôle dans le jeton SAML. Une autre approche consiste à envoyer une revendication de rôle unique de manière conditionnelle en fonction de l'appartenance de l'utilisateur à un groupe particulier. Toutefois, pour ce didacticiel, nous allons procéder plus simplement.
	-	ID de nom (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier) - peut être utilisé pour la validation anti-contrefaçon. Pour plus d’informations sur son fonctionnement avec la validation anti-contrefaçon, consultez la section **Ajouter des fonctionnalités métier à l’exemple d’application** de [Créer une application Web .NET MVC dans Azure App Service avec authentification Azure Active Directory](web-sites-dotnet-lob-application-azure-ad.md#bkmk_crud).

	> [AZURE.NOTE]Les types de revendication que vous devez configurer pour votre application sont déterminés par les besoins de votre application. Pour la liste des revendications prises en charge par les applications Azure Active Directory (c’est-à-dire les approbations de partie de confiance), par exemple, consultez la page [Types de revendication et de jeton pris en charge](http://msdn.microsoft.com/library/azure/dn195587.aspx).

8.	Dans la boîte de dialogue Modifier les règles de revendication, cliquez sur **Ajouter une règle**.
9.	Configurez les revendications de nom, de nom d'utilisateur principal et de rôle comme indiqué ci-dessous et cliquez sur **Terminer**.

	![](./media/web-sites-dotnet-lob-application-adfs/5-ldap-claims.png)

	Vous créerez ensuite une revendication d'ID de nom temporaire à l'aide de la procédure présentée dans [Identificateurs de nom dans les assertions SAML](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx).

9.	Cliquez de nouveau sur **Ajouter une règle**.
10.	Sélectionnez **Envoyer les revendications à l'aide d'une règle personnalisée**, puis cliquez sur **Suivant**.
11.	Collez la langue de règle suivante dans la zone **Règle personnalisée**, nommez la règle **Identificateur de session** et cliquez sur **Terminer**.  
	<pre class="prettyprint">
c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] &amp;&amp;
c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant"]
	=> add(
		store = "_OpaqueIdStore",
		types = ("<mark>http://contoso.com/internal/sessionid</mark>"),
		query = "{0};{1};{2};{3};{4}",
		param = "useEntropy",
		param = c1.Value,
		param = c1.OriginalIssuer,
		param = "",
		param = c2.Value);
</pre>Votre règle personnalisée doit ressembler à ceci :

	![](./media/web-sites-dotnet-lob-application-adfs/6-per-session-identifier.png)

9.	Cliquez de nouveau sur **Ajouter une règle**.
10.	Sélectionnez **Transformer une revendication entrante** et cliquez sur **Suivant**.
11.	Configurez la règle comme indiqué ci-dessous (en utilisant le type de revendication que vous avez créé dans la règle personnalisée) et cliquez sur **Terminer**.

	![](./media/web-sites-dotnet-lob-application-adfs/7-transient-name-id.png)

	Pour plus d'informations sur les étapes de la revendication d'ID de nom temporaire ci-dessus, consultez la page [Identificateurs de noms dans les assertions SAML](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx).

12.	Cliquez sur **Appliquer** dans la boîte de dialogue **Modifier les règles de revendication**. Elle doit maintenant ressembler à la capture d'écran ci-dessous :

	![](./media/web-sites-dotnet-lob-application-adfs/8-all-claim-rules.png)

	> [AZURE.NOTE]Là encore, assurez-vous que vous répétez ces étapes pour votre environnement de débogage et l’application web publiée.

<a name="bkmk_test"></a>
## Tester l'authentification fédérée pour votre application

Vous êtes prêt à tester la logique d'authentification de votre application dans AD FS. Dans mon environnement de laboratoire AD FS, je dispose d'un utilisateur de test qui appartient à un groupe de test dans Active Directory (AD).

![](./media/web-sites-dotnet-lob-application-adfs/10-test-user-and-group.png)

Pour tester l'authentification dans le débogueur, il vous suffit de taper sur la touche `F5`. Si vous souhaitez tester l’authentification de l’application web publiée, accédez à l’URL.

Après le chargement de l'application Web, cliquez sur **Se connecter**. Une boîte de dialogue ou une page de connexion doit maintenant s'afficher dans AD FS, selon la méthode d'authentification choisie par AD FS. Voici ce que nous obtenons dans Internet Explorer 11.

![](./media/web-sites-dotnet-lob-application-adfs/9-test-debugging.png)

Une fois que vous vous connectez avec un utilisateur dans le domaine Active Directory du déploiement AD FS, la page d’accueil doit désormais afficher **Bonjour, <User Name> !** dans le coin. Voici ce que nous obtenons.

![](./media/web-sites-dotnet-lob-application-adfs/11-test-debugging-success.png)

Jusqu'à présent, vous avez réussi ce qui suit :

- Votre application a atteint AD FS et un identificateur de partie de confiance correspondant a été trouvé dans la base de données AD FS
- AD FS a correctement authentifié un utilisateur Active Directory et vous a redirigé vers la page d'accueil de l'application
- AD FS a correctement envoyé la revendication de nom à votre application (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name), comme l'indique le fait que le nom d'utilisateur soit affiché dans le coin. 

Si la revendication de nom était manquante, vous auriez vu le texte **Bonjour, !**. Si vous examinez Views\Shared_LoginPartial.cshtml, vous constatez qu’il utilise `User.Identity.Name` pour afficher le nom d’utilisateur. Comme mentionné précédemment, ASP.NET alimente cette propriété avec la revendication de nom de l'utilisateur authentifié, si elle est disponible dans le jeton SAML. Pour afficher toutes les revendications qui sont envoyées par AD FS, placez un point d'arrêt dans Controllers\HomeController.cs, dans la méthode d'action Index. Une fois que l’utilisateur est authentifié, inspectez la collection `System.Security.Claims.Current.Claims`.

![](./media/web-sites-dotnet-lob-application-adfs/12-test-debugging-all-claims.png)

<a name="bkmk_authorize"></a>
## Autorisation des utilisateurs pour des contrôleurs ou des actions spécifiques

Étant donné que vous avez inclus les appartenances aux groupes en tant que revendications de rôle dans votre configuration d'approbation de partie de confiance, vous pouvez désormais les utiliser directement dans la décoration `[Authorize(Roles="...")]` Authorize(Roles="...") pour les contrôleurs et les actions. Dans une application métier suivant le modèle Create-Read-Update-Delete (CRUD), vous pouvez autoriser des rôles spécifiques à accéder à chaque action. À ce stade, vous allez simplement essayer cette fonctionnalité sur le contrôleur Home existant.

1. Ouvrez Controllers\HomeController.cs.
2. Décorez les méthodes d’action `About` et `Contact` similaires à celles ci-dessous, à l’aide des appartenances aux groupes de sécurité de l’utilisateur authentifié.  
	<pre class="prettyprint">
<mark>[Authorize(Roles="Groupe de test")]</mark>
public ActionResult About()
{
    ViewBag.Message = "Page de description de votre application.";

    return View();
}

<mark>[Authorize(Roles="Admins du domaine")]</mark>
public ActionResult Contact()
{
    ViewBag.Message = "Votre page de contacts.";

    return View();
}
</pre>Étant donné que j’ai ajouté **Utilisateur de test** à **Groupe de test** dans mon environnement de laboratoire AD FS, je vais utiliser le groupe de test pour tester l’autorisation sur `About`. Pour `Contact`, je vais tester le cas négatif de **Admins du domaine**, auquel l’**utilisateur de test** n’appartient pas.

3. Démarrez le débogueur en tapant `F5` et connectez-vous, puis cliquez sur **À propos**. Vous devez maintenant afficher correctement la page `~/About/Index`, si votre utilisateur authentifié est autorisé à effectuer cette action.
4. Cliquez maintenant sur **Contact**, ce qui, dans mon cas, ne doit pas autoriser l'**utilisateur de test** à effectuer l'action. Toutefois, le navigateur est redirigé vers AD FS, qui finit par afficher le message suivant :

	![](./media/web-sites-dotnet-lob-application-adfs/13-authorize-adfs-error.png)

	Si vous examinez cette erreur dans l'Observateur d'événements sur le serveur AD FS, vous verrez ce message d'exception : <pre class="prettyprint"> Microsoft.IdentityServer.Web.InvalidRequestException: MSIS7042: <mark>La même session du navigateur client a effectué « 6 » demandes durant les « 11 » dernières secondes.</mark> Pour plus d'informations, contactez votre administrateur, à Microsoft.IdentityServer.Web.Protocols.PassiveProtocolHandler.UpdateLoopDetectionCookie(WrappedHttpListenerContext context), à Microsoft.IdentityServer.Web.Protocols.WSFederation.WSFederationProtocolHandler.SendSignInResponse(WSFederationContext context, MSISSignInResponse response), à Microsoft.IdentityServer.Web.PassiveProtocolListener.ProcessProtocolRequest(ProtocolContext protocolContext, PassiveProtocolHandler protocolHandler) et à Microsoft.IdentityServer.Web.PassiveProtocolListener.OnGetContext(WrappedHttpListenerContext context) </pre>

	Cela se produit, car, par défaut, MVC renvoie une erreur 401 Non autorisé lorsque les rôles d'un utilisateur ne sont pas autorisés. Cela déclenche une demande de réauthentification auprès de votre fournisseur d'identité (AD FS). Étant donné que l'utilisateur est déjà authentifié, AD FS renvoie la même page, qui émet alors une autre erreur 401, créant ainsi une boucle de redirection. Vous devez remplacer la méthode `HandleUnauthorizedRequest` de AuthorizeAttribute par une logique simple pour obtenir un affichage adéquat au lieu de continuer la boucle de redirection.

5. Créez un fichier dans le projet appelé AuthorizeAttribute.cs et collez-y le code ci-dessous.

		using System;
		using System.Web.Mvc;
		using System.Web.Routing;
		
		namespace WebApp_WSFederation_DotNet
		{
		    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
		    public class AuthorizeAttribute : System.Web.Mvc.AuthorizeAttribute
		    {
		        protected override void HandleUnauthorizedRequest(AuthorizationContext filterContext)
		        {
		            if (filterContext.HttpContext.Request.IsAuthenticated)
		            {
		                filterContext.Result = new System.Web.Mvc.HttpStatusCodeResult((int)System.Net.HttpStatusCode.Forbidden);
		            }
		            else
		            {
		                base.HandleUnauthorizedRequest(filterContext);
		            }
		        }
		    }
		}

	Le code de substitution envoie une erreur HTTP 403 (Interdit) au lieu de HTTP 401 (Non autorisé) dans le cas d'une authentification sans autorisation.

6. Réexécutez le débogueur à l'aide de `F5`. Lorsque vous cliquez sur **Contact**, un message d'erreur plus explicite s'affiche à présent :

	![](./media/web-sites-dotnet-lob-application-adfs/14-unauthorized-forbidden.png)

7. Publiez de nouveau l’application sur Azure App Service Web Apps et testez le comportement de l’application active.

<a name="bkmk_data"></a>
## Se connecter à des données locales

Vous pouvez implémenter votre application métier avec AD FS plutôt qu'avec Azure Active Directory en cas de problèmes de conformité concernant la conservation des données hors site de l'organisation. Cela signifie également que votre application Web dans Azure doit accéder aux bases de données locales, dans la mesure où vous n'êtes pas autorisé à utiliser [Base de données SQL](/services/sql-database/) comme couche de données pour vos applications Web.

Azure App Service Web Apps prend en charge l’accès aux bases de données locales avec deux approches : [Connexions hybrides](../integration-hybrid-connection-overview.md) et [Réseaux virtuels](web-sites-integrate-with-vnet.md). Pour plus d’informations, consultez la rubrique [Utilisation de l’intégration VNET et des connexions hybrides avec Azure App Service Web Apps](http://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/).

<a name="bkmk_resources"></a>
## Ressources supplémentaires

- [Protection de l’application à l’aide du protocole SSL et de l’attribut Authorize](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute)
- [Utiliser Active Directory pour l’authentification dans Azure App Service](web-sites-authentication-authorization.md)
- [Créer une application Web .NET MVC dans Azure App Service avec authentification AD FS](web-sites-dotnet-lob-application-azure-ad.md)
- [Utilisation de l'option d'authentification organisationnelle locale (AD FS) avec ASP.NET dans Visual Studio 2013](http://www.cloudidentity.com/blog/2014/02/12/use-the-on-premises-organizational-authentication-option-adfs-with-asp-net-in-visual-studio-2013/)
- [Blog de Vittorio Bertocci](http://blogs.msdn.com/b/vbertocci/)
- [Migration d’un projet Web VS2013 de WIF vers Katana](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
- [Vue d’ensemble des services AD FS](http://technet.microsoft.com/library/hh831502.aspx)
- [Spécification WS-Federation 1.1](http://download.boulder.ibm.com/ibmdl/pub/software/dw/specs/ws-fed/WS-Federation-V1-1B.pdf?S_TACT=105AGX04&S_CMP=LP)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 
 

<!---HONumber=62-->