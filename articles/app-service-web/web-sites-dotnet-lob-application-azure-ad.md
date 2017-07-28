---
title: "Créer une application Azure cœur de métier avec authentification Azure Active Directory | Microsoft Docs"
description: "Apprenez à créer une application cœur de métier ASP.NET MVC dans Azure App Service qui s’authentifie avec Azure Active Directory."
services: app-service\web, active-directory
documentationcenter: .net
author: cephalin
manager: erikre
editor: 
ms.assetid: ad947bdb-4463-43ff-a5e3-91d9b2169b60
ms.service: app-service-web
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 09/01/2016
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 2576b658eaf1df95aa9700e06559edf6066cc534
ms.contentlocale: fr-fr
ms.lasthandoff: 06/01/2017


---
# <a name="create-a-line-of-business-azure-app-with-azure-active-directory-authentication"></a>Créer une application Azure cœur de métier avec authentification Azure Active Directory
Cet article vous montre comment créer une application Azure cœur de métier dans [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) à l’aide de la fonctionnalité d’[authentification/autorisation](../app-service/app-service-authentication-overview.md). Il indique également comment utiliser l’ [API Graph Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) pour interroger les données d’annuaire dans l’application.

Le client Azure Active Directory que vous utilisez peut être un annuaire Azure uniquement. Il peut également être [synchronisé avec votre Active Directory local](../active-directory/active-directory-aadconnect.md) pour créer une expérience d’authentification unique pour les employés locaux et distants. Cet article utilise le répertoire par défaut pour votre compte Azure.

<a name="bkmk_build"></a>

## <a name="what-you-will-build"></a>Ce que vous allez créer
Vous allez créer une application CRUD (Create-Read-Update-Delete) métier simple dans App Service Web Apps qui assure le suivi des éléments de travail et qui présente les caractéristiques suivantes :

* Authentification des utilisateurs à l’aide d’Azure Active Directory
* Interrogation des utilisateurs et des groupes de répertoires à l’aide de l’ [API Graph Azure Active Directory](http://msdn.microsoft.com/library/azure/hh974476.aspx)
* Utilisation du modèle ASP.NET MVC *Aucune authentification*

Si vous avez besoin de contrôle d’accès en fonction du rôle (RBAC) pour votre application cœur de métier dans Azure, consultez l’ [étape suivante](#next).

<a name="bkmk_need"></a>

## <a name="what-you-need"></a>Ce dont vous avez besoin
[!INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Vous devez disposer des éléments suivants pour suivre ce didacticiel :

* Un locataire Azure Active Directory avec les utilisateurs de différents groupes
* Les autorisations permettant de créer des applications sur le locataire AAD
* Visual Studio 2013 Update 4 (ou version ultérieure)
* [Kit de développement logiciel (SDK) Azure 2.8.1 ou version ultérieure](https://azure.microsoft.com/downloads/)

<a name="bkmk_deploy"></a>

## <a name="create-and-deploy-a-web-app-to-azure"></a>Créer et déployer une application web sur Azure
1. Dans Visual Studio, cliquez sur **Fichier** > **Nouveau** > **Projet**.
2. Sélectionnez **Application web ASP.NET**, nommez votre projet, puis cliquez sur **OK**.
3. Sélectionnez le modèle **MVC**, puis définissez l’authentification sur **Aucune authentification**. Vérifiez que **Héberger dans le cloud** est sélectionné et cliquez sur **OK**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/1-create-mvc-no-authentication.png)
4. Dans la boîte de dialogue **Créer App Service**, cliquez sur **Ajouter un compte** (puis sur **Ajouter un compte** dans la liste déroulante) pour vous connecter à votre compte Azure.
5. Une fois connecté, configurez votre application web. Créez un groupe de ressources et un plan App Service en cliquant sur le bouton **Nouveau** correspondant. Cliquez sur **Exploration d’autres services Azure** pour continuer.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/2-create-app-service.png)
6. Dans l’onglet **Services**, cliquez sur **+** pour ajouter une base de données SQL pour votre application. 
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/3-add-sql-database.png)
7. Dans **Configurer la base de données SQL**, cliquez sur **Nouveau** pour créer une instance SQL Server.
8. Dans **Configurer SQL Server**, configurez votre instance SQL Server. Ensuite, cliquez sur **OK**, **OK** et **Créer** pour lancer la création de l’application dans Azure.
9. Dans **Activité d’Azure App Service**, vous pouvez voir lorsque la création de l’application est terminée. Cliquez sur **Publier &lt;*nom_application*> dans cette application web maintenant**, puis cliquez sur **Publier**. 
   
    Une fois que Visual Studio a terminé, il ouvre l’application de publication dans le navigateur. 
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/4-published-shown-in-browser.png)

<a name="bkmk_auth"></a>

## <a name="configure-authentication-and-directory-access"></a>Configurer l’authentification et l’accès à l’annuaire
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, cliquez sur **App Services** > **&lt;*nom_application*>** > **Authentification/Autorisation**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/5-app-service-authentication.png)
3. Activez l’authentification Azure Active Directory en cliquant sur **Activé** > **Azure Active Directory** > **Express** > **OK**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/6-authentication-express.png)
4. Cliquez sur **Enregistrer** dans la barre de commandes.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/7-authentication-save.png)
   
    Une fois les paramètres d’authentification enregistrés correctement, essayez de nouveau d’accéder à votre application dans le navigateur. Les paramètres par défaut assurent une authentification sur l’ensemble de l’application. Si vous n’êtes pas encore connecté, vous êtes redirigé vers un écran de connexion. Une fois connecté, vous constatez que votre application est sécurisée via HTTPS. Ensuite, vous devez activer l’accès aux données de l’annuaire. 
5. Accédez au [Portail Classic](https://manage.windowsazure.com).
6. Dans le menu de gauche, cliquez sur **Active Directory** > **Répertoire par défaut** > **Applications** > **&lt;*appname*>**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/8-find-aad-application.png)
   
    Il s’agit de l’application Azure Active Directory créée pour vous par App Service pour activer la fonctionnalité d’autorisation/authentification.
7. Cliquez sur **Utilisateurs** et **Groupes** pour vous assurer que l’annuaire contient des utilisateurs et des groupes. Dans le cas contraire, créez plusieurs utilisateurs et groupes de test.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-users-groups.png)
8. Cliquez sur **Configurer** pour configurer cette application.
9. Faites défiler jusqu’à la section **Clés** et ajoutez une clé en sélectionnant une durée. Ensuite, cliquez sur **Autorisations déléguées** et sélectionnez **Lire les données de l’annuaire**. 
   Cliquez sur **Enregistrer**.
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/10-configure-aad-application.png)
10. Une fois que vos paramètres sont enregistrés, revenez à la section **Clés** et cliquez sur le bouton **Copier** pour copier la clé du client. 
    
     ![](./media/web-sites-dotnet-lob-application-azure-ad/11-get-app-key.png)
    
    > [!IMPORTANT]
    > Si vous quittez cette page maintenant, vous ne pourrez plus accéder à cette clé de client.
    > 
    > 
11. Ensuite, vous devez configurer votre application web avec cette clé. Connectez-vous à [Azure Resource Explorer](https://resources.azure.com) avec votre compte Azure.
12. En haut de la page, cliquez sur **Lecture/écriture** pour apporter des modifications dans Azure Resource Explorer.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/12-resource-manager-writable.png)
13. Recherchez les paramètres d’authentification de votre application, situés dans subscriptions > **&lt;*nom_abonnement*>** > **resourceGroups** > **&lt;*nom_groupe_ressources*>** > **providers** > **Microsoft.Web** > **sites** > **&lt;*nom_application*>** > **config** > **authsettings**.
14. Cliquez sur **Modifier**.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/13-edit-authsettings.png)
15. Dans le volet d’édition, définissez les propriétés `clientSecret` et `additionalLoginParams` comme suit.
    
        ...
        "clientSecret": "<client key from the Azure Active Directory application>",
        ...
        "additionalLoginParams": ["response_type=code id_token", "resource=https://graph.windows.net"],
        ...
16. Cliquez sur **Put** en haut pour soumettre vos modifications.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/14-edit-parameters.png)
17. Maintenant, pour vérifier si vous disposez du jeton d’autorisation permettant d’accéder à l’API Graph Azure Active Directory, accédez à **https://&lt;*appname*>.azurewebsites.net/.auth/me** dans votre navigateur. Si vous avez configuré tous les éléments correctement, vous devez voir la propriété `access_token` dans la réponse JSON.
    
    Le chemin d’accès de l’URL `~/.auth/me` est géré par l’authentification/autorisation App Service, afin de vous donner toutes les informations relatives à votre session authentifiée. Pour plus d’informations, consultez la page [Authentification et autorisation dans Azure App Service](../app-service/app-service-authentication-overview.md).
    
    > [!NOTE]
    > Le chemin d’accès de l’URL `access_token` a un délai d’expiration. Toutefois, l’authentification/autorisation App Service fournit des fonctionnalités d’actualisation du jeton grâce à `~/.auth/refresh`. Pour plus d’informations sur son utilisation, consultez la page [App Service Token Store](https://cgillum.tech/2016/03/07/app-service-token-store/)(Boutique de jetons App Service).
    > 
    > 

Ensuite, vous ferez quelque chose d’utile avec les données d’annuaire.

<a name="bkmk_crud"></a>

## <a name="add-line-of-business-functionality-to-your-app"></a>Ajouter une fonctionnalité cœur de métier à votre application
Nous allons maintenant créer un outil de suivi simple des éléments de travail CRUD.  

1. Dans le dossier ~\Models, créez un fichier de classe intitulé WorkItem.cs et remplacez le code `public class WorkItem {...}` par le code suivant :
   
     using System.ComponentModel.DataAnnotations;
   
     public class WorkItem   {
   
         [Key]
         public int ItemID { get; set; }
         public string AssignedToID { get; set; }
         public string AssignedToName { get; set; }
         public string Description { get; set; }
         public WorkItemStatus Status { get; set; }
     }
   
     public enum WorkItemStatus   {
   
         Open,
         Investigating,
         Resolved,
         Closed
     }
2. Générez le projet pour rendre votre nouveau modèle accessible à la logique de génération de modèles automatique dans Visual Studio.
3. Ajoutez un nouvel élément généré automatiquement `WorkItemsController` au dossier ~\Controllers (cliquez avec le bouton sur **Contrôleurs**, pointez sur **Ajouter**, puis sélectionnez **Nouvel élément généré automatiquement**). 
4. Sélectionnez **Contrôleur MVC 5 avec vues, en utilisant Entity Framework**, puis cliquez sur **Ajouter**.
5. Sélectionnez le modèle que vous avez créé, puis cliquez sur **+**, puis sur **Ajouter** pour ajouter un contexte de données, puis cliquez sur **Ajouter**.
   
   ![](./media/web-sites-dotnet-lob-application-azure-ad/16-add-scaffolded-controller.png)
6. Dans ~\Views\WorkItems\Create.cshtml (élément généré automatiquement), recherchez la méthode d’assistance `Html.BeginForm` et modifiez-la comme suit :  
   
   <pre class="prettyprint">
   @model WebApplication1.Models.WorkItem
   
   @{
    ViewBag.Title = &quot;Create&quot;;
   }
   
   &lt;h2&gt;Create&lt;/h2&gt;
   
   @using (Html.BeginForm(<mark>&quot;Create&quot;, &quot;WorkItems&quot;, FormMethod.Post, new { id = &quot;main-form&quot; }</mark>)) 
   {
    @Html.AntiForgeryToken()
   
    &lt;div class=&quot;form-horizontal&quot;&gt;
        &lt;h4&gt;WorkItem&lt;/h4&gt;
        &lt;hr /&gt;
        @Html.ValidationSummary(true, &quot;&quot;, new { @class = &quot;text-danger&quot; })
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.AssignedToID, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EditorFor(model =&gt; model.AssignedToID, new { htmlAttributes = new { @class = &quot;form-control&quot;<mark>, @type = &quot;hidden&quot;</mark> } })
                @Html.ValidationMessageFor(model =&gt; model.AssignedToID, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.AssignedToName, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EditorFor(model =&gt; model.AssignedToName, new { htmlAttributes = new { @class = &quot;form-control&quot; } })
                @Html.ValidationMessageFor(model =&gt; model.AssignedToName, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.Description, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EditorFor(model =&gt; model.Description, new { htmlAttributes = new { @class = &quot;form-control&quot; } })
                @Html.ValidationMessageFor(model =&gt; model.Description, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.Status, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EnumDropDownListFor(model =&gt; model.Status, htmlAttributes: new { @class = &quot;form-control&quot; })
                @Html.ValidationMessageFor(model =&gt; model.Status, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            &lt;div class=&quot;col-md-offset-2 col-md-10&quot;&gt;
                &lt;input type=&quot;submit&quot; value=&quot;Create&quot; class=&quot;btn btn-default&quot;<mark> id=&quot;submit-button&quot;</mark> /&gt;
            &lt;/div&gt;
        &lt;/div&gt;
    &lt;/div&gt;
   }
   
   &lt;div&gt;
    @Html.ActionLink(&quot;Back to List&quot;, &quot;Index&quot;)
   &lt;/div&gt;
   
   @section Scripts {
    @Scripts.Render(&quot;~/bundles/jqueryval&quot;)
    <mark>&lt;script&gt;
        // People/Group Picker Code
        var maxResultsPerPage = 14;
        var input = document.getElementById(&quot;AssignedToName&quot;);
   
        // Access token from request header, and tenantID from claims identity
        var token = &quot;@Request.Headers[&quot;X-MS-TOKEN-AAD-ACCESS-TOKEN&quot;]&quot;;
        var tenant =&quot;@(System.Security.Claims.ClaimsPrincipal.Current.Claims
                        .Where(c => c.Type == &quot;http://schemas.microsoft.com/identity/claims/tenantid&quot;)
                        .Select(c => c.Value).SingleOrDefault())&quot;;
   
        var picker = new AadPicker(maxResultsPerPage, input, token, tenant);
   
        // Submit the selected user/group to be asssigned.
        $(&quot;#submit-button&quot;).click({ picker: picker }, function () {
            if (!picker.Selected())
                return;
            $(&quot;#main-form&quot;).get()[0].elements[&quot;AssignedToID&quot;].value = picker.Selected().objectId;
        });
    &lt;/script&gt;</mark>
   }
   </pre>
   
   Notez que `token` et `tenant` sont utilisés par l’objet `AadPicker` pour effectuer des appels API Graph Azure Active Directory. Vous allez ajouter `AadPicker` ultérieurement.     
   
   > [!NOTE]
   > Vous pouvez également obtenir `token` et `tenant` à partir du client avec `~/.auth/me`, mais ce serait un appel de serveur supplémentaire. Par exemple :
   > 
   > $.ajax({ dataType: "json", url: "/.auth/me", success: function (data) { var token = data[0].access_token; var tenant = data[0].user_claims .find(c => c.typ === 'http://schemas.microsoft.com/identity/claims/tenantid') .val; } });
   > 
   > 
7. Apportez les mêmes modifications avec ~\Views\WorkItems\Edit.cshtml.
8. L’objet `AadPicker` est défini dans un script que vous souhaitez ajouter à votre projet. Cliquez avec le bouton droit sur le dossier ~\Scripts, pointez sur **Ajouter**, et cliquez sur **Fichier JavaScript**. Tapez `AadPickerLibrary` pour le nom de fichier et cliquez sur **OK**.
9. Copiez le contenu à partir d’[ici](https://raw.githubusercontent.com/cephalin/active-directory-dotnet-webapp-roleclaims/master/WebApp-RoleClaims-DotNet/Scripts/AadPickerLibrary.js) dans ~\Scripts\AadPickerLibrary.js.
   
   Dans le script, l’objet `AadPicker` appelle l’ [API Graph Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) pour rechercher des utilisateurs et des groupes qui correspondent à l’entrée.  
10. ~\Scripts\AadPickerLibrary.js utilise également le [widget de saisie semi-automatique de l’interface utilisateur jQuery](https://jqueryui.com/autocomplete/). Par conséquent, vous devez ajouter l’interface utilisateur jQuery à votre projet. Cliquez avec le bouton droit sur votre projet et cliquez sur **Gérer les packages NuGet**.
11. Dans le Gestionnaire de Package NuGet, cliquez sur Parcourir, tapez **jquery-ui** dans la barre de recherche, et cliquez sur **jQuery.UI.Combined**.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/17-add-jquery-ui-nuget.png)
12. Dans le volet droit, cliquez sur **Installer**, puis sur **OK** pour continuer.
13. Ouvrez ~\App_Start\BundleConfig.cs et apportez les modifications suivantes :  
    
    <pre class="prettyprint">
    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;).Include(
                    &quot;~/Scripts/jquery-{version}.js&quot;<mark>,
                    &quot;~/Scripts/jquery-ui-{version}.js&quot;,
                    &quot;~/Scripts/AadPickerLibrary.js&quot;</mark>));
    
        bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;).Include(
                    &quot;~/Scripts/jquery.validate*&quot;));
    
        // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
        // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
        bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;).Include(
                    &quot;~/Scripts/modernizr-*&quot;));
    
        bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;).Include(
                    &quot;~/Scripts/bootstrap.js&quot;,
                    &quot;~/Scripts/respond.js&quot;));
    
        bundles.Add(new StyleBundle(&quot;~/Content/css&quot;).Include(
                    &quot;~/Content/bootstrap.css&quot;,
                    &quot;~/Content/site.css&quot;<mark>,
                    &quot;~/Content/themes/base/jquery-ui.css&quot;</mark>));
    }
    </pre>
    
    Il existe d’autres manières de gérer de manière performante les fichiers JavaScript et CSS dans votre application. Toutefois, par souci de simplicité vous allez juste procéder à une superposition sur les offres groupées chargées avec chaque vue.
14. Enfin, dans ~ \Global.asax, ajoutez la ligne de code suivante à la méthode `Application_Start()`. `Ctrl`+`.` sur chaque erreur de résolution d’affectation de noms pour résoudre le problème.
    
        AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
    
    > [!NOTE]
    > Vous avez besoin de cette ligne de code, car le modèle MVC par défaut utilise la décoration <code>[ValidateAntiForgeryToken]</code> sur certaines des actions. En raison du comportement décrit par [Brock Allen](https://twitter.com/BrockLAllen) dans son article intitulé [MVC 4, AntiForgeryToken and Claims](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/) (en anglais), votre HTTP POST risque d’échouer lors de la validation du jeton anti-contrefaçon pour les motifs suivants :
    > 
    > * Azure Active Directory n’envoie pas la revendication http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider qui, par défaut, est nécessaire au jeton d’anti-contrefaçon.
    > * S’il y a synchronisation d’annuaire entre Azure Active Directory et AD FS, l’approbation AD FS par défaut n’envoie pas la revendication http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, même si vous pouvez configurer manuellement l’envoi de cette revendication par les services AD FS.
    > 
    > `ClaimTypes.NameIdentifies` spécifie la revendication `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`, qu’Azure Active Directory ne fournit pas.  
    > 
    > 
15. Maintenant, publiez vos modifications. Cliquez avec le bouton droit sur votre projet et cliquez sur **Publier**.
16. Cliquez sur **Paramètres**, assurez-vous qu’il y a une chaîne de connexion à votre base de données SQL, sélectionnez **Mettre à jour la base de données** pour apporter les modifications de schéma pour votre modèle, puis cliquez sur **Publier**.
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/18-publish-crud-changes.png)
17. Dans le navigateur, accédez à https://&lt;*appname*>.azurewebsites.net/workitems, puis cliquez sur **Créer nouveau**.
18. Cliquez dans la zone **AssignedToName** . Les utilisateurs et les groupes issus de votre client Azure Active Directory s’affichent désormais dans une liste déroulante. Vous pouvez saisir du texte pour filtrer, ou utiliser la clé `Up` ou `Down` pour sélectionner l’utilisateur ou le groupe. 
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/19-use-aadpicker.png)
19. Cliquez sur **Créer** pour enregistrer les modifications. Ensuite, cliquez sur **Modifier** sur l’élément de travail créé pour observer le même comportement.

Félicitations, vous exécutez à présent une application cœur de métier dans Azure avec accès aux annuaires ! L’API Graph vous offre de nombreuses autres possibilités. Reportez-vous à la documentation de [référence sur l’API Graph Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog).

<a name="next"></a>

## <a name="next-step"></a>étape suivante
Si vous avez besoin d’un contrôle d’accès en fonction du rôle (RBAC) pour votre application cœur de métier dans Azure, consultez [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) pour obtenir un exemple de l’équipe Azure Active Directory. Ce dernier montre comment activer des rôles pour votre application Azure Active Directory et autoriser des utilisateurs disposant de la décoration `[Authorize]` .

Si votre application cœur de métier doit accéder à des données locales, consultez la page [Accéder à des ressources locales à l’aide de connexions hybrides dans Azure App Service](web-sites-hybrid-connection-get-started.md).

<a name="bkmk_resources"></a>

## <a name="further-resources"></a>Ressources supplémentaires
* [Authentification et autorisation dans Azure App Service](../app-service/app-service-authentication-overview.md)
* [Authentification avec Active Directory en local dans votre application Azure](web-sites-authentication-authorization.md)
* [Création d’une application cœur de métier dans Azure avec authentification AD FS](web-sites-dotnet-lob-application-adfs.md)
* [Authentification App Service et API Graph Azure AD](https://cgillum.tech/2016/03/25/app-service-auth-aad-graph-api/)
* [Documentation et exemples Microsoft Azure Active Directory](https://github.com/AzureADSamples)
* [Types de jeton et de revendication pris en charge par Azure Active Directory](http://msdn.microsoft.com/library/azure/dn195587.aspx)

