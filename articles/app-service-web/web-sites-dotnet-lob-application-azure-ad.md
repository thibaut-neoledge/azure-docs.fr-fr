<properties 
	pageTitle="Créer une application Azure cœur de métier avec authentification Azure Active Directory | Microsoft Azure" 
	description="Apprenez à créer une application cœur de métier ASP.NET MVC dans Azure App Service qui s’authentifie avec Azure Active Directory." 
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
	ms.date="09/01/2016" 
	ms.author="cephalin"/>

# Créer une application Azure cœur de métier avec authentification Azure Active Directory #

Cet article vous montre comment créer une application Azure cœur de métier dans [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) à l’aide de la fonctionnalité d’[authentification/autorisation](../app-service/app-service-authentication-overview.md). Il indique également comment utiliser l’[API Graph Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) pour interroger les données d’annuaire dans l’application.

Le client Azure Active Directory que vous utilisez peut être un annuaire Azure uniquement. Il peut également être [synchronisé avec votre Active Directory local](../active-directory/active-directory-aadconnect.md) pour créer une expérience d’authentification unique pour les employés locaux et distants. Cet article utilise le répertoire par défaut pour votre compte Azure.

<a name="bkmk_build"></a>
## Ce que vous allez créer ##

Vous allez créer une application CRUD (Create-Read-Update-Delete) métier simple dans App Service Web Apps qui assure le suivi des éléments de travail et qui présente les caractéristiques suivantes :

- Authentification des utilisateurs à l’aide d’Azure Active Directory
- Interrogation des utilisateurs et des groupes de répertoires à l’aide de l’[API Graph Azure Active Directory](http://msdn.microsoft.com/library/azure/hh974476.aspx)
- Utilisation du modèle ASP.NET MVC *Aucune authentification*

Si vous avez besoin de contrôle d’accès en fonction du rôle (RBAC) pour votre application cœur de métier dans Azure, consultez l’[étape suivante](#next).

<a name="bkmk_need"></a>
## Ce dont vous avez besoin ##

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Vous devez disposer des éléments suivants pour suivre ce didacticiel :

- Un locataire Azure Active Directory avec les utilisateurs de différents groupes
- Les autorisations permettant de créer des applications sur le locataire AAD
- Visual Studio 2013 Update 4 (ou version ultérieure)
- [Kit de développement logiciel (SDK) Azure 2.8.1 ou version ultérieure](https://azure.microsoft.com/downloads/)

<a name="bkmk_deploy"></a>
## Créer et déployer une application web sur Azure ##

1. Dans Visual Studio, cliquez sur **Fichier** > **Nouveau** > **Projet**.

2. Sélectionnez **Application web ASP.NET**, nommez votre projet, puis cliquez sur **OK**.

3. Sélectionnez le modèle **MVC**, puis définissez l’authentification sur **Aucune authentification**. Assurez-vous que **Héberger dans le cloud** est sélectionné et cliquez sur **OK**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/1-create-mvc-no-authentication.png)

4. Dans la boîte de dialogue **Créer App Service**, cliquez sur **Ajouter un compte** (puis sur **Ajouter un compte** dans la liste déroulante) pour vous connecter à votre compte Azure.

5. Une fois connecté, configurez votre application web. Créez un groupe de ressources et un plan App Service en cliquant sur le bouton **Nouveau** correspondant. Cliquez sur **Exploration d’autres services Azure** pour continuer.

	![](./media/web-sites-dotnet-lob-application-azure-ad/2-create-app-service.png)

6. Dans l’onglet **Services**, cliquez sur **+** pour ajouter une base de données SQL pour votre application.

	![](./media/web-sites-dotnet-lob-application-azure-ad/3-add-sql-database.png)

7. Dans **Configurer la base de données SQL**, cliquez sur **Nouveau** pour créer une instance SQL Server.

8. Dans **Configurer SQL Server**, configurez votre instance SQL Server. Ensuite, cliquez sur **OK**, **OK**, et **Créer** pour lancer la création de l’application dans Azure.

9. Dans **Activité d’Azure App Service**, vous pouvez voir lorsque la création de l’application est terminée. Cliquez sur **Publier &lt;*appname*> dans cette application web maintenant**, puis cliquez sur **Publier**.

	Une fois que Visual Studio a terminé, il ouvre l’application de publication dans le navigateur.

	![](./media/web-sites-dotnet-lob-application-azure-ad/4-published-shown-in-browser.png)

<a name="bkmk_auth"></a>
## Configurer l’authentification et l’accès à l’annuaire

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu de gauche, cliquez sur **App Services** > **& lt ;*appname*>** > **Authentification/Autorisation**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/5-app-service-authentication.png)

3. Activez l’authentification Azure Active Directory en cliquant sur **Activé** > **Azure Active Directory** > **Express** > **OK**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/6-authentication-express.png)

4. Cliquez sur **Enregistrer** dans la barre de commandes.

	![](./media/web-sites-dotnet-lob-application-azure-ad/7-authentication-save.png)

    Une fois les paramètres d’authentification enregistrés correctement, essayez de nouveau d’accéder à votre application dans le navigateur. Les paramètres par défaut assurent une authentification sur l’ensemble de l’application. Si vous n’êtes pas encore connecté, vous êtes redirigé vers un écran de connexion. Une fois connecté, vous constatez que votre application est sécurisée via HTTPS. Ensuite, vous devez activer l’accès aux données de l’annuaire.

5. Accédez au [Portail Classic](https://manage.windowsazure.com).

6. Dans le menu de gauche, cliquez sur **Active Directory** > **Répertoire par défaut** > **Applications** > **& lt ;*appname*>**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/8-find-aad-application.png)

	Il s’agit de l’application Azure Active Directory créée pour vous par App Service pour activer la fonctionnalité d’autorisation/authentification.

7. Cliquez sur **Utilisateurs** et **Groupes** pour vous assurer que l’annuaire contient des utilisateurs et des groupes. Dans le cas contraire, créez plusieurs utilisateurs et groupes de test.

	![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-users-groups.png)

7. Cliquez sur **Configurer** pour configurer cette application.

8. Faites défiler jusqu’à la section **Clés** et ajoutez une clé en sélectionnant une durée. Ensuite, cliquez sur **Autorisations déléguées** et sélectionnez **Lire les données de l’annuaire**. Cliquez sur **Enregistrer**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/10-configure-aad-application.png)

8. Une fois que vos paramètres sont enregistrés, revenez à la section **Clés** et cliquez sur le bouton **Copier** pour copier la clé du client.

	![](./media/web-sites-dotnet-lob-application-azure-ad/11-get-app-key.png)

	>[AZURE.IMPORTANT] Si vous quittez cette page maintenant, vous ne pourrez plus accéder à cette clé de client.

9. Ensuite, vous devez configurer votre application web avec cette clé. Connectez-vous à [Azure Resource Explorer](https://resources.azure.com) avec votre compte Azure.

10. En haut de la page, cliquez sur **Lecture/écriture** pour apporter des modifications dans Azure Resource Explorer.

	![](./media/web-sites-dotnet-lob-application-azure-ad/12-resource-manager-writable.png)

11. Recherchez les paramètres d’authentification pour votre application situé dans subscriptions > **&lt;*subscriptionname*>** > **resourceGroups** > **&lt;*resourcegroupname*>** > **providers** > **Microsoft.Web** > **sites** > **&lt;*appname*>** > **config** > **authsettings**.

12. Cliquez sur **Modifier**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/13-edit-authsettings.png)

13. Dans le volet d’édition, définissez les propriétés `clientSecret` et `additionalLoginParams` comme suit.

		...
		"clientSecret": "<client key from the Azure Active Directory application>",
		...
		"additionalLoginParams": ["response_type=code id_token", "resource=https://graph.windows.net"],
		...

14. Cliquez sur **Put** en haut pour soumettre vos modifications.

	![](./media/web-sites-dotnet-lob-application-azure-ad/14-edit-parameters.png)

14. Maintenant, pour vérifier si vous disposez du jeton d’autorisation permettant d’accéder à l’API Graph Azure Active Directory, accédez à **https://&lt;*appname*>.azurewebsites.net/.auth/me** dans votre navigateur. Si vous avez configuré tous les éléments correctement, vous devez voir la propriété `access_token` dans la réponse JSON.

	Le chemin d’accès de l’URL `~/.auth/me` est géré par l’authentification/autorisation App Service, afin de vous donner toutes les informations relatives à votre session authentifiée. Pour plus d’informations, consultez la page [Authentification et autorisation dans Azure App Service](../app-service/app-service-authentication-overview.md).

	>[AZURE.NOTE] Le `access_token` a un délai d’expiration. Toutefois, l’authentification/autorisation App Service fournit des fonctionnalités d’actualisation du jeton grâce à `~/.auth/refresh`. Pour plus d’informations sur son utilisation, consultez la page [App Service Token Store](https://cgillum.tech/2016/03/07/app-service-token-store/) (Boutique de jetons App Service).

Ensuite, vous ferez quelque chose d’utile avec les données d’annuaire.

<a name="bkmk_crud"></a>
## Ajouter une fonctionnalité cœur de métier à votre application

Nous allons maintenant créer un outil de suivi simple des éléments de travail CRUD.

5.	Dans le dossier ~\\Models, créez un fichier de classe intitulé WorkItem.cs et remplacez le code `public class WorkItem {...}` par le code suivant :

		using System.ComponentModel.DataAnnotations;

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

7.	Générez le projet pour rendre votre nouveau modèle accessible à la logique de génération de modèles automatique dans Visual Studio.

8.	Ajoutez un nouvel élément généré automatiquement `WorkItemsController` au dossier ~\\Controllers (cliquez avec le bouton sur **Contrôleurs**, pointez sur **Ajouter**, puis sélectionnez **Nouvel élément généré automatiquement**).

9.	Sélectionnez **Contrôleur MVC 5 avec vues, en utilisant Entity Framework**, puis cliquez sur **Ajouter**.

10.	Sélectionnez le modèle que vous avez créé, puis cliquez sur **+**, puis sur **Ajouter** pour ajouter un contexte de données, puis cliquez sur **Ajouter**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/16-add-scaffolded-controller.png)

14.	Dans ~\\Views\\WorkItems\\Create.cshtml (élément généré automatiquement), recherchez la méthode d’assistance `Html.BeginForm` et modifiez-la comme suit :
	<pre class="prettyprint">
	@model WebApplication1.Models.WorkItem
	
	@{
		ViewBag.Title = "Create";
	}
	
	&lt;h2>Create&lt;/h2>
	
	@using (Html.BeginForm(<mark>"Create", "WorkItems", FormMethod.Post, new { id = "main-form" }</mark>)) 
	{
		@Html.AntiForgeryToken()
	
		&lt;div class="form-horizontal">
			&lt;h4>WorkItem&lt;/h4>
			&lt;hr />
			@Html.ValidationSummary(true, "", new { @class = "text-danger" })
			&lt;div class="form-group">
				@Html.LabelFor(model => model.AssignedToID, htmlAttributes: new { @class = "control-label col-md-2" })
				&lt;div class="col-md-10">
					@Html.EditorFor(model => model.AssignedToID, new { htmlAttributes = new { @class = "form-control"<mark>, @type = "hidden"</mark> } })
					@Html.ValidationMessageFor(model => model.AssignedToID, "", new { @class = "text-danger" })
				&lt;/div>
			&lt;/div>
	
			&lt;div class="form-group">
				@Html.LabelFor(model => model.AssignedToName, htmlAttributes: new { @class = "control-label col-md-2" })
				&lt;div class="col-md-10">
					@Html.EditorFor(model => model.AssignedToName, new { htmlAttributes = new { @class = "form-control" } })
					@Html.ValidationMessageFor(model => model.AssignedToName, "", new { @class = "text-danger" })
				&lt;/div>
			&lt;/div>
	
			&lt;div class="form-group">
				@Html.LabelFor(model => model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
				&lt;div class="col-md-10">
					@Html.EditorFor(model => model.Description, new { htmlAttributes = new { @class = "form-control" } })
					@Html.ValidationMessageFor(model => model.Description, "", new { @class = "text-danger" })
				&lt;/div>
			&lt;/div>
	
			&lt;div class="form-group">
				@Html.LabelFor(model => model.Status, htmlAttributes: new { @class = "control-label col-md-2" })
				&lt;div class="col-md-10">
					@Html.EnumDropDownListFor(model => model.Status, htmlAttributes: new { @class = "form-control" })
					@Html.ValidationMessageFor(model => model.Status, "", new { @class = "text-danger" })
				&lt;/div>
			&lt;/div>
	
			&lt;div class="form-group">
				&lt;div class="col-md-offset-2 col-md-10">
					&lt;input type="submit" value="Create" class="btn btn-default"<mark> id="submit-button"</mark> />
				&lt;/div>
			&lt;/div>
		&lt;/div>
	}
	
	&lt;div>
		@Html.ActionLink("Back to List", "Index")
	&lt;/div>
	
	@section Scripts {
		@Scripts.Render("~/bundles/jqueryval")
		<mark>&lt;script>
			// People/Group Picker Code
			var maxResultsPerPage = 14;
			var input = document.getElementById("AssignedToName");
	
			// Jeton d’accès de l’en-tête de requête et ID de client de l’identité de revendication
			var token = "@Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]";
			var tenant ="@(System.Security.Claims.ClaimsPrincipal.Current.Claims
							.Where(c => c.Type == "http://schemas.microsoft.com/identity/claims/tenantid")
							.Select(c => c.Value).SingleOrDefault())";
	
			var picker = new AadPicker(maxResultsPerPage, input, token, tenant);
	
			// Submit the selected user/group to be asssigned.
			$("#submit-button").click({ picker: picker }, function () {
				if (!picker.Selected())
					return;
				$("#main-form").get()[0].elements["AssignedToID"].value = picker.Selected().objectId;
			});
		&lt;/script></mark>
	}
	</pre>
	
	Notez que `token` et `tenant` sont utilisés par l’objet `AadPicker` pour effectuer des appels API Graph Azure Active Directory. Vous allez ajouter `AadPicker` ultérieurement.
	
	>[AZURE.NOTE] Vous pouvez également obtenir `token` et `tenant` à partir du client avec `~/.auth/me`, mais ce serait un appel de serveur supplémentaire. Par exemple :
	>  
    >     $.ajax({
    >         dataType: "json",
    >         url: "/.auth/me",
    >         success: function (data) {
    >             var token = data[0].access_token;
    >             var tenant = data[0].user_claims
    >                             .find(c => c.typ === 'http://schemas.microsoft.com/identity/claims/tenantid')
    >                             .val;
    >         }
    >     });
	
15. Apportez les mêmes modifications avec ~\\Views\\WorkItems\\Edit.cshtml.

15. L’objet `AadPicker` est défini dans un script que vous souhaitez ajouter à votre projet. Cliquez avec le bouton droit sur le dossier ~\\Scripts, pointez sur **Ajouter**, et cliquez sur **Fichier JavaScript**. Tapez `AadPickerLibrary` pour le nom de fichier et cliquez sur **OK**.

16. Copiez le contenu à partir d’[ici](https://raw.githubusercontent.com/cephalin/active-directory-dotnet-webapp-roleclaims/master/WebApp-RoleClaims-DotNet/Scripts/AadPickerLibrary.js) dans ~\\Scripts\\AadPickerLibrary.js.

	Dans le script, l’objet `AadPicker` appelle l’[API Graph Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) pour rechercher des utilisateurs et des groupes qui correspondent à l’entrée.

17. ~\\Scripts\\AadPickerLibrary.js utilise également le [widget de saisie semi-automatique de l’interface utilisateur jQuery](https://jqueryui.com/autocomplete/). Par conséquent, vous devez ajouter l’interface utilisateur jQuery à votre projet. Cliquez avec le bouton droit sur votre projet et cliquez sur **Gérer les packages NuGet**.

18. Dans le Gestionnaire de Package NuGet, cliquez sur Parcourir, tapez **jquery-ui** dans la barre de recherche, et cliquez sur **jQuery.UI.Combined**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/17-add-jquery-ui-nuget.png)

19. Dans le volet droit, cliquez sur **Installer**, puis sur **OK** pour continuer.

19. Ouvrez ~\\App\_Start\\BundleConfig.cs et apportez les modifications suivantes :
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
		bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
					"~/Scripts/jquery-{version}.js"<mark>,
					"~/Scripts/jquery-ui-{version}.js",
					"~/Scripts/AadPickerLibrary.js"</mark>));
	
		bundles.Add(new ScriptBundle("~/bundles/jqueryval").Include(
					"~/Scripts/jquery.validate*"));
	
		// Use the development version of Modernizr to develop with and learn from. Then, when you're
		// ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
		bundles.Add(new ScriptBundle("~/bundles/modernizr").Include(
					"~/Scripts/modernizr-*"));
	
		bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
					"~/Scripts/bootstrap.js",
					"~/Scripts/respond.js"));
	
		bundles.Add(new StyleBundle("~/Content/css").Include(
					"~/Content/bootstrap.css",
					"~/Content/site.css"<mark>,
					"~/Content/themes/base/jquery-ui.css"</mark>));
	}
	</pre>

	Il existe d’autres manières de gérer de manière performante les fichiers JavaScript et CSS dans votre application. Toutefois, par souci de simplicité vous allez juste procéder à une superposition sur les offres groupées chargées avec chaque vue.

12. Enfin, dans ~ \\Global.asax, ajoutez la ligne de code suivante à la méthode `Application_Start()`. `Ctrl`+`.` sur chaque erreur de résolution d’affectation de noms pour résoudre le problème.

		AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
	
	> [AZURE.NOTE] Vous avez besoin de cette ligne de code, car le modèle MVC par défaut utilise la décoration <code>[ValidateAntiForgeryToken]</code> sur certaines des actions. En raison du comportement décrit par [Brock Allen](https://twitter.com/BrockLAllen) dans son article intitulé [MVC 4, AntiForgeryToken and Claims](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/) (en anglais), votre HTTP POST risque d’échouer lors de la validation du jeton anti-contrefaçon pour les motifs suivants :

	> - Azure Active Directory n’envoie pas le http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, qui est requis par défaut par le jeton anti-contrefaçon.
	> - S’il y a synchronisation d’annuaire entre Azure Active Directory et AD FS, l’approbation AD FS par défaut n’envoie pas la revendication http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, même si vous pouvez configurer manuellement l’envoi de cette revendication par les services AD FS.

	> `ClaimTypes.NameIdentifies` spécifie la revendication `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`, qu’Azure Active Directory ne fournit pas.

20. Maintenant, publiez vos modifications. Cliquez avec le bouton droit sur votre projet et cliquez sur **Publier**.

21. Cliquez sur **Paramètres**, assurez-vous qu’il y a une chaîne de connexion à votre base de données SQL, sélectionnez **Mettre à jour la base de données** pour apporter les modifications de schéma pour votre modèle, puis cliquez sur **Publier**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/18-publish-crud-changes.png)

22. Dans le navigateur, accédez à https://&lt;*appname*>.azurewebsites.net/workitems, puis cliquez sur **Créer nouveau**.

23. Cliquez dans la zone **AssignedToName**. Les utilisateurs et les groupes issus de votre client Azure Active Directory s’affichent désormais dans une liste déroulante. Vous pouvez saisir du texte pour filtrer, ou utiliser la clé `Up` ou `Down` pour sélectionner l’utilisateur ou le groupe.

	![](./media/web-sites-dotnet-lob-application-azure-ad/19-use-aadpicker.png)

24. Cliquez sur **Créer** pour enregistrer les modifications. Ensuite, cliquez sur **Modifier** sur l’élément de travail créé pour observer le même comportement.

Félicitations, vous exécutez à présent une application cœur de métier dans Azure avec accès aux annuaires ! L’API Graph vous offre de nombreuses autres possibilités. Reportez-vous à la documentation de [référence sur l’API Graph Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog).

<a name="next"></a>
## Étape suivante

Si vous avez besoin d’un contrôle d’accès en fonction du rôle (RBAC) pour votre application cœur de métier dans Azure, consultez [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) pour obtenir un exemple de l’équipe Azure Active Directory. Ce dernier montre comment activer des rôles pour votre application Azure Active Directory et autoriser des utilisateurs disposant de la décoration `[Authorize]`.

Si votre application cœur de métier doit accéder à des données locales, consultez la page [Accéder à des ressources locales à l’aide de connexions hybrides dans Azure App Service](web-sites-hybrid-connection-get-started.md).

<a name="bkmk_resources"></a>
## Ressources supplémentaires

- [Authentification et autorisation dans Azure App Service](../app-service/app-service-authentication-overview.md)
- [Authentification avec Active Directory en local dans votre application Azure](web-sites-authentication-authorization.md)
- [Création d’une application cœur de métier dans Azure avec authentification AD FS](web-sites-dotnet-lob-application-adfs.md)
- [Authentification App Service et API Graph Azure AD](https://cgillum.tech/2016/03/25/app-service-auth-aad-graph-api/)
- [Documentation et exemples Microsoft Azure Active Directory](https://github.com/AzureADSamples)
- [Types de jeton et de revendication pris en charge par Azure Active Directory](http://msdn.microsoft.com/library/azure/dn195587.aspx)

[Protect the Application with SSL and the Authorize Attribute]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute

<!---HONumber=AcomDC_0914_2016-->