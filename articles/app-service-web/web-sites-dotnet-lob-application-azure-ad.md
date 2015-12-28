<properties 
	pageTitle="Créer une application web .NET MVC dans Azure App Service avec authentification Azure Active Directory" 
	description="Apprenez à créer une application métier ASP.NET MVC dans Azure App Service qui s’authentifie avec Azure Active Directory." 
	services="app-service\web, active-directory" 
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
	ms.date="12/10/2015" 
	ms.author="cephalin"/>

# Créer une application web .NET MVC dans Azure App Service avec authentification Azure Active Directory #

Dans cet article, vous allez apprendre à créer une application métier ASP.NET MVC dans [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) avec [Azure Active Directory](/services/active-directory/) comme fournisseur d’identité. Vous apprendrez aussi à utiliser la bibliothèque cliente [Azure Active Directory Graph](http://blogs.msdn.com/b/aadgraphteam/archive/2014/06/02/azure-active-directory-graph-client-library-1-0-publish.aspx) pour interroger les données d’annuaire dans l’application.

Le locataire Azure Active Directory que vous utilisez peut être un annuaire Azure uniquement ou bien il peut être synchronisé avec votre annuaire Active Directory local pour créer une expérience d’authentification unique pour les utilisateurs locaux ou distants.

>[AZURE.NOTE] Pour Azure App Service Web Apps, vous pouvez configurer l’authentification par rapport à un locataire Azure Active Directory en quelques clics. Pour plus d’informations, consultez [Utiliser Active Directory pour l’authentification dans Azure App Service](web-sites-authentication-authorization.md).

<a name="bkmk_build"></a>
## Ce que vous allez créer ##

Vous allez créer une application CRUD (Create-Read-Update-Delete) métier simple dans App Service Web Apps qui assure le suivi des éléments de travail et qui présente les caractéristiques suivantes :

- Authentification des utilisateurs à l’aide d’Azure Active Directory
- Implémentation des fonctionnalités de connexion et de déconnexion
- Utilisation de `[Authorize]` pour autoriser les utilisateurs à effectuer différentes actions CRUD
- Requêtes de données Azure Active Directory à l’aide de l’[API Azure Active Directory Graph](http://msdn.microsoft.com/library/azure/hh974476.aspx)
- Utilisation de [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) (au lieu de Windows Identity Foundation, ou WIF), qui représente l’avenir d’ASP.NET et qui est beaucoup plus simple à configurer que WIF pour l’authentification et l’autorisation

<a name="bkmk_need"></a>
## Éléments requis ##

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

Vous devez disposer des éléments suivants pour suivre ce didacticiel :

- Un locataire Azure Active Directory avec les utilisateurs de différents groupes
- Les autorisations permettant de créer des applications sur le locataire AAD
- Visual Studio 2013 ou une version ultérieure
- [Azure SDK 2.8.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) ou version ultérieure

<a name="bkmk_sample"></a>
## Utiliser l’exemple d’application pour le modèle métier ##

L’exemple d’application présenté dans ce didacticiel, [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims), a été créé par l’équipe Azure Active Directory et peut être utilisé comme modèle pour créer facilement des applications métier. Il intègre les fonctionnalités suivantes :

- Utilisation de [OpenID Connect](http://openid.net/connect/) pour s’authentifier auprès d’Azure Active Directory
- Contient un exemple de contrôleur `TaskTracker` qui vous montre comment autoriser différents rôles pour des actions spécifiques dans une application, notamment l’utilisation standard de `[Authorize]`. 
- Est une application mutualisée avec des rôles prédéfinis que vous pouvez affecter immédiatement à vos utilisateurs et groupes. 

<a name="bkmk_run" />
## Exécution de l’exemple d’application ##

1.	Clonez ou téléchargez l’exemple de solution depuis [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) dans votre répertoire local.

2.	Suivez les instructions dans [Exécution de l’échantillon comme application à client unique](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims#how-to-run-the-sample-as-a-single-tenant-app) pour configurer l’application et le projet Azure Active Directory. Veillez à suivre toutes les instructions pour convertir l'application mutualisée en application à client unique.

3.	Dans l’affichage du [portail Azure Classic](https://manage.windowsazure.com) de votre application Azure Active Directory nouvellement créée, cliquez sur l’onglet **UTILISATEURS**. Ensuite, attribuez les rôles que vous souhaitez aux utilisateurs de votre choix.

	>[AZURE.NOTE]Si vous souhaitez attribuer des rôles à des groupes en plus des utilisateurs, vous devez mettre à niveau votre client Azure Active Directory vers [Azure Active Directory Premium](/pricing/details/active-directory/). Dans l’interface utilisateur du portail Azure Classic de votre application, si vous voyez l’onglet **UTILISATEURS** au lieu de l’onglet **UTILISATEURS ET GROUPES, vous pouvez essayer Azure Active Directory Premium en accédant à l’onglet **LICENCES** de votre client Azure Active Directory.

3.	Une fois la configuration de l’application achevée, tapez `F5` dans Visual Studio pour exécuter l’application ASP.NET.

4.	Une fois que l’application se charge, cliquez sur **Connexion** et connectez-vous avec un nom d’utilisateur qui possède le rôle d’administrateur dans le portail Azure Classic.

5.	Si l’application Azure Active Directory est correctement configurée et que les paramètres correspondants dans le fichier Web.config sont bien définis, vous devriez être redirigé vers le journal. Connectez-vous avec le compte que vous avez utilisé pour créer l’application Azure Active Directory dans le portail Azure Classic, puisqu’il est le propriétaire de l’application Azure Active Directory par défaut.
	
<a name="bkmk_deploy"></a>
## Déployer l’exemple d’application vers App Service Web Apps

Ici, vous allez publier l’application sur une application web dans Azure App Service. Le fichier [README.md](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims/blob/master/README.md) contient déjà des instructions de déploiement vers App Service Web Apps. Or, ces étapes ont aussi pour effet d’annuler la configuration de votre environnement de débogage local. Je vais vous montrer comment déployer tout en conservant la configuration de débogage.

1. Cliquez avec le bouton droit sur votre projet et sélectionnez **Publier**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/publish-app.png)

2. Sélectionnez **Microsoft Azure Web Apps**.

3. Si vous n’êtes pas connecté à Azure, cliquez sur **Ajouter un compte** et utilisez le compte Microsoft de votre abonnement Azure pour vous connecter.

4. Une fois connecté, cliquez sur **Nouveau** pour créer une application web dans Azure.

5. Renseignez tous les champs obligatoires dans **Hébergement**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/4-create-website.png)

5. Vous aurez besoin d’une connexion de base de données pour permettre à cette application de stocker les mappages de rôles, les jetons mis en cache et les données d’application éventuelles. Dans la boîte de dialogue **Créer App Service**, cliquez sur **Services**. Cliquez sur le signe plus (+) à côté de **Base de données SQL** pour ajouter une nouvelle base de données.

	![](./media/web-sites-dotnet-lob-application-azure-ad/4-create-database.png)

5. Dans la boîte de dialogue**Configurer une base de données SQL**, sélectionnez ou créez un serveur, définissez un nom et cliquez sur **OK**.

	 ![](./media/web-sites-dotnet-lob-application-azure-ad/4-config-database.png)

6. Cliquez sur **Créer**. Une fois l’application web créée, la boîte de dialogue **Publier le site web** s’ouvre.

7. Dans **URL de destination**, remplacez **http** par **https**. Copiez l’URL entière dans un éditeur de texte. Vous allez l’utiliser ultérieurement. Cliquez ensuite sur **Suivant**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/5-change-to-https.png)

8. Désactivez la case à cocher **Autoriser l’authentification d’une organisation**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/6-enable-code-first-migrations.png)

8. Développez **RoleClaimContext** et sélectionnez **Exécuter les migrations Code First (s’exécute au démarrage de l’application)**. Les [migrations Code First](https://msdn.microsoft.com/data/jj591621.aspx) permettent de mettre à jour le schéma de base de données de votre application dans Azure lorsque vous définissez des modèles de données Code First supplémentaires ultérieurement.

9. Au lieu de cliquer sur **Publier** pour procéder à la publication du site web, cliquez sur **Fermer**. Cliquez sur **Oui** pour enregistrer les modifications dans le profil de publication.

2. Dans le [portail Azure Classic](https://manage.windowsazure.com), accédez à votre client Azure Active Directory et cliquez sur l’onglet **Applications**.

2. Cliquez sur **Ajouter** en bas de la page.

2. Cliquez sur **Ajouter une application développée par mon organisation**.

3. Sélectionnez **Application Web et/ou API Web**.

4. Nommez l’application et cliquez sur **Suivant**.

5. Dans les propriétés de l’application, définissez l’**URL de connexion** sur l’URL de l’application web enregistrée précédemment (par exemple, `https://<site-name>.azurewebsites.net/`) et l’**URI ID d’application** sur `https://<aad-tenanet-name>/<app-name>`. Cliquez ensuite sur **Terminé**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/7-app-properties.png)

2.	Une fois l’application créée, mettez à jour le manifeste de l’application de la même façon qu’auparavant en suivant les instructions dans [Définir vos rôles d’application](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims#step-2-define-your-application-roles).

3.	Dans l’affichage du [portail Azure Classic](https://manage.windowsazure.com) de votre application Azure Active Directory nouvellement créée, cliquez sur l’onglet **UTILISATEURS**. Ensuite, attribuez les rôles que vous souhaitez aux utilisateurs de votre choix.

6. Cliquez sur l'onglet **Configurer**.

7. Sous **Clés**, créez une clé en sélectionnant **1 an** dans la liste déroulante.

8. Sous **Autorisations pour d’autres applications**, pour l’entrée **Azure Active Directory**, sélectionnez **Connexion et lecture de profils utilisateurs** et **Lire les données du répertoire** dans la liste déroulante **Autorisations déléguées**.

	> [AZURE.NOTE] Les autorisations exactes dont vous avez besoin ici dépendent de la fonctionnalité voulue pour votre application. Si certaines autorisations nécessitent de définir le rôle **Administrateur global**, ce didacticiel n’a besoin que du rôle **Utilisateur**.

9.  Cliquez sur **Save**.

10.  Avant de quitter la page de la configuration enregistrée, copiez les informations suivantes dans un éditeur de texte.

	-	ID client
	-	Clé (si vous quittez la page, vous ne pourrez pas revoir la clé)

11. Dans Visual Studio, ouvrez **Web.Release.config** dans votre projet. Insérez le code XML suivant dans la balise `<configuration>`, puis remplacez la valeur de chaque clé par les informations que vous avez enregistrées pour votre nouvelle application Azure Active Directory.
	<pre class="prettyprint">
&lt;appSettings&gt;
   &lt;add key="ida:ClientId" value="<mark>[e.g. 82692da5-a86f-44c9-9d53-2f88d52b478b]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" /&gt;
   &lt;add key="ida:AppKey" value="<mark>[e.g. rZJJ9bHSi/cYnYwmQFxLYDn/6EfnrnIfKoNzv9NKgbo=]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" /&gt;
   &lt;add key="ida:PostLogoutRedirectUri" value="<mark>[e.g. https://mylobapp.azurewebsites.net/]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" /&gt;
&lt;/appSettings&gt;</pre>

	Assurez-vous que la valeur ida:PostLogoutRedirectUri se termine par une barre oblique « / ».

1. Cliquez avec le bouton droit sur votre projet et sélectionnez **Publier**.

2. Cliquez sur **Publier** pour publier sur Azure App Service Web Apps.

À l’issue de cette opération, vous avez deux applications Azure Active Directory configurées dans le portail Azure Classic : une pour votre environnement de débogage dans Visual Studio et une autre pour l’application web publiée dans Azure. Pendant le débogage, les paramètres d’application du fichier Web.config sont utilisés pour rendre votre configuration **Debug** compatible avec Azure Active Directory. Ensuite, dès qu’elle est publiée (par défaut, il s’agit de la configuration **Release**), un fichier Web.config transformé qui incorpore les modifications apportées aux paramètres d’application dans le fichier Web.Release.config est téléchargé.

Si vous voulez attacher l’application web publiée au débogueur (dans ce cas, vous devez télécharger les symboles de débogage de votre code dans l’application web publiée), vous pouvez créer un clone de la configuration Debug pour le débogage Azure, mais avec sa propre transformation Web.config personnalisée (par exemple, Web.AzureDebug.config) qui utilise les paramètres Azure Active Directory du fichier Web.Release.config. Cela vous permet de maintenir une configuration statique entre les différents environnements.

<a name="bkmk_crud"></a>
## Ajouter des fonctionnalités métier à l’exemple d’application

Dans cette partie du didacticiel, vous allez apprendre à créer la fonctionnalité métier souhaitée à partir de l’exemple d’application. Vous allez créer un dispositif de suivi d’éléments de travail CRUD simple, semblable au contrôleur TaskTracker, mais en utilisant la génération de modèles automatique et le modèle de conception CRUD standard. Vous allez aussi utiliser le fichier Scripts\\AadPickerLibrary.js inclus pour enrichir votre application avec les données de l’API Azure Active Directory Graph.

5.	Dans le dossier Modèles, créez un nouveau modèle [Code First](http://www.asp.net/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) intitulé WorkItem.cs et remplacez le code par le code suivant :

		using System.ComponentModel.DataAnnotations;
		
		namespace WebApp_RoleClaims_DotNet.Models
		{
		    public class WorkItem
		    {
		        [Key]
		        public int ItemID { get; set; }
		        public string AssignedToID { get; set; }
		        public string AssignedToName { get; set; }
		        public string Description { get; set; }
		        public WorkItemStatus Status { get; set; }
		    }
		
		    public enum WorkItemStatus
		    {
		        Open, 
		        Investigating, 
		        Resolved, 
		        Closed
		    }
		}

6.	Ouvrez DAL\\RoleClaimContext.cs et ajoutez le code en surbrillance :
	<pre class="prettyprint">
public class RoleClaimContext : DbContext
{
    public RoleClaimContext() : base("RoleClaimContext") { }

    public DbSet&lt;Task> Tasks { get; set; }
    <mark>public DbSet&lt;WorkItem> WorkItems { get; set; }</mark>
    public DbSet&lt;TokenCacheEntry> TokenCacheEntries { get; set; }
}</pre>

7.	Générez le projet pour rendre votre nouveau modèle accessible à la logique de génération de modèles automatique dans Visual Studio.

8.	Ajoutez un nouvel élément généré automatiquement `WorkItemsController` dans le dossier Contrôleurs. Pour ce faire, cliquez avec le bouton droit sur **Contrôleurs**, pointez sur **Ajouter**, puis sélectionnez **Nouvel élément généré automatiquement**.

9.	Sélectionnez **Contrôleur MVC 5 avec vues, en utilisant Entity Framework**, puis cliquez sur **Ajouter**.

10.	Sélectionnez le modèle que vous venez de créer, puis cliquez sur **Ajouter**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/8-add-scaffolded-controller.png)

9.	Ouvrez Controllers\\WorkItemsController.cs

11. Ajoutez les décorations [Authorize] en surbrillance aux actions correspondantes ci-dessous.
	<pre class="prettyprint">
	...

    <mark>[Authorize(Roles = "Admin, Observer, Writer, Approver")]</mark>
    public class WorkItemsController : Controller
    {
		...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public ActionResult Create()
        ...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public async Task&lt;ActionResult&gt; Create([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
        ...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public async Task&lt;ActionResult&gt; Edit(int? id)
        ...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public async Task&lt;ActionResult&gt; Edit([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
        ...

        <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
        public async Task&lt;ActionResult&gt; Delete(int? id)
        ...

        <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
        public async Task&lt;ActionResult&gt; DeleteConfirmed(int id)
        ...
	}</pre>
	
	Sachant que vous vous occupez des mappages de rôles dans l’interface utilisateur du portail Azure Classic, il vous suffit de vérifier que chaque action autorise les rôles appropriés.

	> [AZURE.NOTE] Vous avez peut-être remarqué la décoration <code>[ValidateAntiForgeryToken]</code> sur certaines des actions. En raison du comportement décrit par [Brock Allen](https://twitter.com/BrockLAllen) dans son article intitulé [MVC 4, AntiForgeryToken and Claims](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/), votre HTTP POST risque d’échouer lors de la validation du jeton d’anti-contrefaçon pour les motifs suivants :
	> + Azure Active Directory n’envoie pas la revendication http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, qui, par défaut, est nécessaire au jeton d’anti-contrefaçon.
	> + S’il y a synchronisation d’annuaire entre Azure Active Directory et AD FS, l’approbation AD FS par défaut n’envoie pas la revendication http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, même si vous pouvez configurer manuellement l’envoi de cette revendication par les services AD FS.
	> Vous vous chargerez de cela à l’étape suivante.

12.  Dans App\_Start\\Startup.Auth.cs, ajoutez la ligne de code suivante à la méthode `ConfigureAuth`. Cliquez avec le bouton droit sur chaque erreur de résolution de noms pour résoudre le problème.

		AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
	
	`ClaimTypes.NameIdentifies` spécifie la revendication `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`, qu’Azure Active Directory ne fournit pas. Maintenant que vous en avez fini avec la partie autorisation (sérieusement, cela n’a pas pris beaucoup de temps), vous pouvez vous consacrer à la fonctionnalité effective des actions.

13.	Dans Create() et Edit(), ajoutez le code suivant pour permettre à votre code JavaScript d'accéder par la suite à certaines variables. Cliquez avec le bouton droit sur chaque erreur de résolution de noms pour résoudre le problème.

        ViewData["token"] = AcquireToken(ClaimsPrincipal.Current.FindFirst(Globals.ObjectIdClaimType).Value);
        ViewData["tenant"] = ConfigHelper.Tenant;

13.	La méthode `AcquireToken()` n’est pas encore définie. Définissez-la maintenant dans la classe `WorkItemsController`. Cliquez avec le bouton droit sur chaque erreur de résolution de noms pour résoudre le problème.

        static string AcquireToken(string userObjectId)
        {
            ClientCredential cred = new ClientCredential(ConfigHelper.ClientId, ConfigHelper.AppKey);
            Claim tenantIdClaim = ClaimsPrincipal.Current.FindFirst(Globals.TenantIdClaimType);
            AuthenticationContext authContext = new AuthenticationContext(String.Format(CultureInfo.InvariantCulture, ConfigHelper.AadInstance, tenantIdClaim.Value), new TokenDbCache(userObjectId));
            AuthenticationResult result = authContext.AcquireTokenSilent(ConfigHelper.GraphResourceId, cred, new UserIdentifier(userObjectId, UserIdentifierType.UniqueId));
            return result.AccessToken;
        }
		
14.	Dans Views\\WorkItems\\Create.cshtml (élément généré automatiquement), recherchez la méthode d’assistance `Html.BeginForm` et modifiez-la comme suit :
	<pre class="prettyprint">@using (Html.BeginForm(<mark>"Create", "WorkItems", FormMethod.Post, new { id = "main-form" }</mark>))
	{
	    @Html.AntiForgeryToken()
	    
	    &lt;div class="form-horizontal"&gt;
	        &lt;h4&gt;WorkItem&lt;/h4&gt;
	        &lt;hr /&gt;
	        @Html.ValidationSummary(true, "", new { @class = "text-danger" })
	
	        &lt;div class="form-group"&gt;
	            &lt;div class="col-md-10"&gt;
	                @Html.EditorFor(model =&gt; model.AssignedToID, new { htmlAttributes = new { @class = "form-control"<mark>, @type=&quot;hidden&quot;</mark> } })
	                @Html.ValidationMessageFor(model =&gt; model.AssignedToID, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            @Html.LabelFor(model =&gt; model.AssignedToName, htmlAttributes: new { @class = "control-label col-md-2" })
	            &lt;div class="col-md-10"&gt;
	                @Html.EditorFor(model =&gt; model.AssignedToName, new { htmlAttributes = new { @class = "form-control" } })
	                @Html.ValidationMessageFor(model =&gt; model.AssignedToName, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            @Html.LabelFor(model =&gt; model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
	            &lt;div class="col-md-10"&gt;
	                @Html.EditorFor(model =&gt; model.Description, new { htmlAttributes = new { @class = "form-control" } })
	                @Html.ValidationMessageFor(model =&gt; model.Description, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            @Html.LabelFor(model =&gt; model.Status, htmlAttributes: new { @class = "control-label col-md-2" })
	            &lt;div class="col-md-10"&gt;
	                @Html.EnumDropDownListFor(model =&gt; model.Status, htmlAttributes: new { @class = "form-control" })
	                @Html.ValidationMessageFor(model =&gt; model.Status, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            &lt;div class="col-md-offset-2 col-md-10"&gt;
	                &lt;input type="submit" value="Create" class="btn btn-default" <mark>id="submit-button"</mark> /&gt;
	            &lt;/div&gt;
	        &lt;/div&gt;
	    &lt;/div&gt;
	
	    <mark>&lt;script&gt;
	            // People/Group Picker Code
	            var maxResultsPerPage = 14;
	            var input = document.getElementById("AssignedToName");
	            var token = "@ViewData["token"]";
	            var tenant = "@ViewData["tenant"]";
	
            var picker = new AadPicker(maxResultsPerPage, input, token, tenant);
	
	            // Submit the selected user/group to be asssigned.
	            $("#submit-button").click({ picker: picker }, function () {
	                if (!picker.Selected())
	                    return;
	                $("#main-form").get()[0].elements["AssignedToID"].value = picker.Selected().objectId;
	            });
	    &lt;/script&gt;</mark>
	
	}</pre>
Dans le script, l’objet AadPicker appelle l’[API Graph Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) pour rechercher des utilisateurs et des groupes qui correspondent à l’entrée.

15. Ouvrez la [Console du Gestionnaire de package](http://docs.nuget.org/Consume/Package-Manager-Console) et exécutez **Enable-Migrations –EnableAutomaticMigrations**. Cette commande, similaire à l’option que vous avez sélectionnée lorsque vous avez publié l’application dans Azure, permet de mettre à jour le schéma de base de données de votre application dans [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) lorsque vous la déboguez dans Visual Studio.

15. Exécutez maintenant l’application dans le débogueur Visual Studio ou republiez-la sur App Service Web Apps. Connectez-vous en tant que propriétaire d’application et accédez à `https://<webappname>.azurewebsites.net/WorkItems/Create`. Vous pouvez maintenant sélectionner un utilisateur ou un groupe Azure Active Directory dans la liste déroulante ou taper un texte pour filtrer la liste.

	![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-workitem.png)

16. Complétez le reste du formulaire, puis cliquez sur **Créer**. La page ~/WorkItems/Index affiche maintenant l’élément de travail nouvellement créé. Vous remarquerez également dans la capture d’écran ci-dessous que j’ai supprimé la colonne `AssignedToID` dans Views\\WorkItems\\Index.cshtml.

	![](./media/web-sites-dotnet-lob-application-azure-ad/10-workitem-index.png)

11.	À présent, apportez les mêmes modifications dans la vue **Edit**. Dans Views\\WorkItems\\Edit.cshtml, apportez les mêmes modifications à la méthode d’assistance `Html.BeginForm` que celles que nous avons effectuées pour Views\\WorkItems\\Create.cshtml à l’étape précédente (remplacez « Create » par « Edit » dans le code en surbrillance ci-dessus).

Et voilà !

Maintenant que vous avez configuré les autorisations et la fonctionnalité métier pour les différentes actions du contrôleur WorkItems, vous pouvez essayer de vous connecter en tant qu’utilisateurs ayant des rôles d’application différents pour voir le comportement de l’application.

![](./media/web-sites-dotnet-lob-application-azure-ad/11-edit-unauthorized.png)

<a name="bkmk_resources"></a>
## Ressources supplémentaires

- [Protection de l’application à l’aide du protocole SSL et de l’attribut Authorize](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute)
- [Utiliser Active Directory pour l’authentification dans Azure App Service](web-sites-authentication-authorization.md)
- [Créer une application web .NET MVC dans Azure App Service avec authentification AD FS](web-sites-dotnet-lob-application-adfs.md)
- [Documentation et exemples Microsoft Azure Active Directory](https://github.com/AzureADSamples)
- [Blog de Vittorio Bertocci](http://blogs.msdn.com/b/vbertocci/)
- [Migration d’un projet web VS2013 de WIF vers Katana](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
- [Les nouvelles connexions hybrides Azure et ce qui les distingue du #Cloudhybride d’ancienne génération](/documentation/videos/new-hybrid-connections-not-your-fathers-hybridcloud/)
- [Points communs entre Active Directory et Azure Active Directory](http://technet.microsoft.com/library/dn518177.aspx)
- [Synchronisation d’annuaires avec le scénario d’authentification unique](http://technet.microsoft.com/library/dn441213.aspx)
- [Types de jeton et de revendication pris en charge par Azure Active Directory](http://msdn.microsoft.com/library/azure/dn195587.aspx)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!---HONumber=AcomDC_1217_2015-->