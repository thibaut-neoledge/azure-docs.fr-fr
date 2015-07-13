<properties 
	pageTitle="Création et déploiement d’une application ASP.NET Web Forms sécurisée avec appartenance, OAuth et Base de données SQL sur Azure App Service" 
	description="Ce didacticiel vous montre comment créer une application web ASP.NET 4.5 Web Forms sécurisée qui intègre une base de données SQL et comment la déployer vers Azure." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="Erikre" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/09/2015" 
	ms.author="erikre"/>


# Création et déploiement d’une application ASP.NET Web Forms sécurisée avec appartenance, OAuth et Base de données SQL sur Azure App Service

##Vue d'ensemble
Ce didacticiel vous montre comment créer une application web ASP.NET 4.5 Web Forms sécurisée qui intègre une base de données SQL et comment la déployer vers Azure.

>[AZURE.NOTE]Pour obtenir une version MVC de ce didacticiel, consultez [Créer une application ASP.NET MVC avec authentification et base de données SQL et la déployer dans Azure App Service](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).

Vous pouvez ouvrir gratuitement un compte Azure. Si vous n'avez pas déjà Visual Studio 2013, le Kit de développement logiciel (SDK) installe automatiquement Visual Studio Express 2013 pour le Web. Vous pouvez commencer vos développement Azure gratuitement.

Ce didacticiel part du principe que vous n'avez pas d'expérience en tant qu'utilisateur de Microsoft Azure. À la fin de ce didacticiel, vous disposerez d’une application web fonctionnelle dans le cloud qui utilise une base de données du cloud.

Vous apprendrez ce qui suit :

- créer un projet ASP.NET 4.5 Web Forms et le publier sur Azure App Service ;
- utiliser OAuth et l'appartenance ASP.NET pour sécuriser votre application ;
- utiliser une seule base de données pour les données d'appartenance et pour les données de l'application ;
- utiliser la génération de modèles Web Forms pour créer des pages web qui vous permettent de modifier les données ;
- utilisation de la nouvelle API d’appartenance pour ajouter des utilisateurs et des rôles ;
- utiliser une base de données SQL pour stocker des données dans Azure ;

Vous allez développer une simple application web de liste de contacts basée sur ASP.NET 4.5 Web Forms qui utilise Entity Framework pour accéder à la base de données. L'image suivante représente la page Web Forms qui permet d'accéder en lecture et en écriture à la base de données :

![Contacts - Edit Page](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms00.png)

>[AZURE.NOTE]Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Si vous n’avez pas de compte, vous pouvez <a href="/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">activer les avantages de votre abonnement MSDN</a> ou <a href="/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">obtenir une évaluation gratuite</a>. Si vous souhaitez commencer à utiliser Azure avant d’ouvrir un compte Azure, accédez au site <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, où vous pourrez créer immédiatement un site de départ ASP.NET de courte durée gratuit dans Azure. Aucune carte de crédit n’est requise, vous ne prenez aucun engagement.

##Configuration de l'environnement de développement 
Pour commencer, configurez l'environnement de développement en installant Visual Studio 2013 et le Kit de développement logiciel (SDK) Azure pour .NET.

1. Installez [Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=306566) si vous ne l’avez pas encore fait.  
2. Installez le [Kit de développement logiciel (SDK) Azure pour Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409). Ce didacticiel nécessite Visual Studio 2013 avant l'installation du Kit de développement logiciel (SDK) Azure pour Visual Studio 2013.  

	>[AZURE.NOTE]en fonction du nombre de dépendances du Kit de développement logiciel (SDK) qui se trouvent déjà sur votre ordinateur, l'installation du SDK peut prendre un certain temps (de plusieurs minutes à une demi-heure, voire plus).

3. Lorsque vous êtes invité à exécuter ou à enregistrer l’exécutable d’installation, cliquez sur **Exécuter**.
4. Dans la fenêtre **Web Platform Installer**, cliquez sur **Installer**, puis poursuivez l’installation. ![Web Platform Installer](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-01.png)  

	>[AZURE.NOTE]Si vous avez déjà installé le Kit de développement logiciel (SDK), il ne vous reste plus rien à installer. Le nombre d'éléments à installer est affiché en bas à gauche dans la fenêtre **Web Platform Installer**.

5. Si vous n'avez pas encore **Visual Studio Update 2**, téléchargez et installez **[Visual Studio 2013 Update 2](http://www.microsoft.com/download/details.aspx?id=42666)** ou une version supérieure.

	>[AZURE.NOTE]Vous devez installer Visual Studio 2013 Update 2 ou une version supérieure pour utiliser Google OAuth 2.0 et pour utiliser SSL localement sans avertissement. Update 2 est également requis pour utiliser la génération de modèles automatique Web Forms.

Une fois l’installation terminée, vous disposez de tous les éléments nécessaires pour commencer le développement.

##Configuration de l'environnement Azure
Dans cette section, vous allez configurer l’environnement Azure en créant une application Web et une base de données SQL dans Azure.

###Création d’une application Web et d’une base de données SQL dans Azure 
Dans ce didacticiel, votre application Web s’exécute au sein d’un environnement d’hébergement partagé : elle s’exécute donc sur des machines virtuelles partagées avec d’autres applications Web d’Azure App Service. Un environnement d'hébergement partagé permet de commencer à utiliser le cloud à moindre frais. Plus tard, si votre trafic Web augmente, vous pourrez mettre votre application à l'échelle pour répondre à vos besoins, en exécutant des machines virtuelles dédiées. Si vous avez besoin d'une architecture plus complexe, vous pouvez migrer vers un service cloud Azure. Les services cloud sont exécutés sur des machines virtuelles dédiées pour vous permettre de les configurer selon vos besoins.

La base de données SQL Azure est un service de bases de données relationnelles sur le cloud, basé sur les technologies SQL Server. Les outils et applications fonctionnant avec SQL Server fonctionnent également avec la base de données SQL.

1. Dans l’onglet gauche du [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Applications Web**, puis sur **Nouveau**. ![Web Platform Installer](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-02.png)
2. Cliquez sur **Application Web**, puis sur **Création personnalisée**. ![Création personnalisée](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-03.png) L’Assistant **Nouvelle application Web - Création personnalisée** s’ouvre.  

3. Lors de l’étape **Créer une application Web** de l’Assistant, entrez une chaîne dans la zone **URL** afin de l’utiliser en tant qu’URL unique pour votre application. L’URL complète se composera du texte entré dans cette zone, ainsi que du suffixe affiché en regard de la zone. L’illustration indique une URL probablement déjà utilisée (**il faut donc en choisir une autre**). ![Contacts - Créer un site Web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-04.png)
4. Dans la liste Plan d'hébergement web, sélectionnez la région la plus proche de l'endroit où vous vous trouvez. Ce paramètre indique le centre de données où votre machine virtuelle sera exécutée.
5. Dans la liste déroulante **Base de données**, sélectionnez **Create a free 20 MB SQL database**.
6. Dans la zone **DB Connection String Name**, ne changez pas la valeur par défaut *DefaultConnection*.
7. Cliquez sur la flèche située en bas de la boîte de dialogue. L’Assistant passe à l’étape **Specify database settings**.
8. Dans la zone **Nom**, entrez *`ContactDB`*. ![Paramètres de base de données](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-05.png)  
9. Dans la zone **Serveur**, sélectionnez **Nouveau serveur de base de données SQL**. Par ailleurs, si vous avez déjà créé une base de données SQL Server, vous pouvez la sélectionner dans la liste déroulante.
10. Définissez la **région** sur la même zone que celle utilisée pour la création de l’application Web.
11. Entrez un **Nom de connexion** et un **Mot de passe** d’administrateur. Si vous avez sélectionné **New SQL Database server**, vous ne devez pas entrer un nom et un mot de passe existant ici, mais une nouvelle paire nom/mot de passe que vous allez choisir maintenant et utiliser ultérieurement lorsque vous accèderez à la base de données. Si vous avez sélectionné une base de données SQL Server créée auparavant, vous devez entrer le mot de passe et le nom du compte SQL Server que vous aviez créé. Pour ce didacticiel, ne cochez pas la case **Avancé**.
12. Cliquez sur la coche située dans le coin inférieur droit de la zone pour indiquer que vous avez terminé.

Le **portail de gestion Azure** revient à la page **Applications Web** et la colonne **Statut** indique que le site est en cours de création. Après un temps d'attente (généralement inférieur à une minute), la colonne **Statut** indique que le site a été créé correctement. Dans la barre de navigation de gauche, le nombre de sites de votre compte apparaît en regard de l’icône **Application Web**, tandis que le nombre de bases de données apparaît en regard de l’icône **Bases de données SQL**.
##Création d'une application ASP.NET Web Forms 
Vous avez créé une application Web, mais elle est encore vide. La prochaine étape consiste à créer l’application Web Visual Studio que vous allez publier sur Azure.
###Création du projet 
1. Sélectionnez **Nouveau projet** dans le menu **Fichier** de Visual Studio. ![Menu fichier - Nouveau projet](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms01.png)  
2. Sélectionnez le groupe de modèles **Modèles** -> **Visual C#** -> **Web** sur la gauche. 
3. Choisissez le modèle **Application Web ASP.NET** dans la colonne centrale.
4. Nommez votre projet *ContactManager* et cliquez sur **OK**. ![Boîte de dialogue Nouveau projet](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms02.png)  

	>[AZURE.NOTE]Le nom du projet dans cette série de didacticiels est **ContactManager**. Il est recommandé d'utiliser exactement ce nom de projet pour que le code fourni dans cette série de didacticiels puisse fonctionner comme prévu.

5. Dans la boîte de dialogue **Nouveau projet ASP.NET**, sélectionnez le modèle **Web Forms**. Décochez la case **Héberger dans le cloud** le cas échéant, puis cliquez sur **OK**. ![Boîte de dialogue New ASP.NET Project](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms03.png) L’application Web Forms est créée.
###Mise à jour de la page maître
Dans ASP.NET Web Forms, les pages maîtres vous permettent de créer une mise en page homogène pour les pages de votre application. Une seule page maître définit la présentation et le comportement standard voulus pour toutes les pages (ou un groupe de pages) dans votre application. Vous pouvez ensuite créer des pages de contenu individuelles avec le contenu que vous souhaitez afficher. Lorsque les utilisateurs demandent les pages de contenu, ASP.NET les fusionne avec la page maître pour produire une sortie qui associe la disposition de la page maître et le contenu de la page de contenu. Le nouveau site a besoin du nom de l'application et d'un lien mis à jour. Le lien pointera vers une page qui affichera les coordonnées des contacts. Pour effectuer ces modifications, vous devez modifier le code HTML sur la page maître.

1. Dans l'**Explorateur de solutions**, recherchez et ouvrez la page *Site.Master*.
2. Si la page est en mode **Création**, passez en mode **Source**.
3. Mettez à jour la page maître en modifiant ou en ajoutant le balisage surligné en jaune :

<pre class="prettyprint">
&lt;%@ Master Language="C#" AutoEventWireup="true" CodeBehind="Site.master.cs" Inherits="ContactManager.SiteMaster" %>

&lt;!DOCTYPE html>

&lt;html lang="en">
&lt;head runat="server">
    &lt;meta charset="utf-8" />
    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0" />
    &lt;title>&lt;%: Page.Title %> - <mark>Contact Manager</mark>&lt;/title>

    &lt;asp:PlaceHolder runat="server">
        &lt;%: Scripts.Render("~/bundles/modernizr") %>
    &lt;/asp:PlaceHolder>
    &lt;webopt:bundlereference runat="server" path="~/Content/css" />
    &lt;link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />

&lt;/head>
&lt;body>
    &lt;form runat="server">
        &lt;asp:ScriptManager runat="server">
            &lt;Scripts>
                &lt;%--To learn more about bundling scripts in ScriptManager see http://go.microsoft.com/fwlink/?LinkID=301884 --%>
                &lt;%--Framework Scripts--%>
                &lt;asp:ScriptReference Name="MsAjaxBundle" />
                &lt;asp:ScriptReference Name="jquery" />
                &lt;asp:ScriptReference Name="bootstrap" />
                &lt;asp:ScriptReference Name="respond" />
                &lt;asp:ScriptReference Name="WebForms.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebForms.js" />
                &lt;asp:ScriptReference Name="WebUIValidation.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebUIValidation.js" />
                &lt;asp:ScriptReference Name="MenuStandards.js" Assembly="System.Web" Path="~/Scripts/WebForms/MenuStandards.js" />
                &lt;asp:ScriptReference Name="GridView.js" Assembly="System.Web" Path="~/Scripts/WebForms/GridView.js" />
                &lt;asp:ScriptReference Name="DetailsView.js" Assembly="System.Web" Path="~/Scripts/WebForms/DetailsView.js" />
                &lt;asp:ScriptReference Name="TreeView.js" Assembly="System.Web" Path="~/Scripts/WebForms/TreeView.js" />
                &lt;asp:ScriptReference Name="WebParts.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebParts.js" />
                &lt;asp:ScriptReference Name="Focus.js" Assembly="System.Web" Path="~/Scripts/WebForms/Focus.js" />
                &lt;asp:ScriptReference Name="WebFormsBundle" />
                &lt;%--Site Scripts--%>
            &lt;/Scripts>
        &lt;/asp:ScriptManager>

        &lt;div class="navbar navbar-inverse navbar-fixed-top">
            &lt;div class="container">
                &lt;div class="navbar-header">
                    &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                        &lt;span class="icon-bar">&lt;/span>
                        &lt;span class="icon-bar">&lt;/span>
                        &lt;span class="icon-bar">&lt;/span>
                    &lt;/button>
                    &lt;a class="navbar-brand" runat="server" <mark>id="ContactDemoLink"</mark> href="~/<mark>Contacts/Default.aspx</mark>"><mark>Contact Demo</mark>&lt;/a>
                &lt;/div>
                &lt;div class="navbar-collapse collapse">
                    &lt;ul class="nav navbar-nav">
                        &lt;li>&lt;a runat="server" href="~/">Home&lt;/a>&lt;/li>
                        &lt;li>&lt;a runat="server" href="~/About">About&lt;/a>&lt;/li>
                        &lt;li>&lt;a runat="server" href="~/Contact">Contact&lt;/a>&lt;/li>
                    &lt;/ul>
                    &lt;asp:LoginView runat="server" ViewStateMode="Disabled">
                        &lt;AnonymousTemplate>
                            &lt;ul class="nav navbar-nav navbar-right">
                                &lt;li>&lt;a runat="server" href="~/Account/Register">Register&lt;/a>&lt;/li>
                                &lt;li>&lt;a runat="server" href="~/Account/Login">Log in&lt;/a>&lt;/li>
                            &lt;/ul>
                        &lt;/AnonymousTemplate>
                        &lt;LoggedInTemplate>
                            &lt;ul class="nav navbar-nav navbar-right">
                                &lt;li>&lt;a runat="server" href="~/Account/Manage" title="Manage your account">Hello, &lt;%: Context.User.Identity.GetUserName()  %> !&lt;/a>&lt;/li>
                                &lt;li>
                                    &lt;asp:LoginStatus runat="server" LogoutAction="Redirect" LogoutText="Log off" LogoutPageUrl="~/" OnLoggingOut="Unnamed_LoggingOut" />
                                &lt;/li>
                            &lt;/ul>
                        &lt;/LoggedInTemplate>
                    &lt;/asp:LoginView>
                &lt;/div>
            &lt;/div>
        &lt;/div>
        &lt;div class="container body-content">
            &lt;asp:ContentPlaceHolder ID="MainContent" runat="server">
            &lt;/asp:ContentPlaceHolder>
            &lt;hr />
            &lt;footer>
                &lt;p>&amp;copy; &lt;%: DateTime.Now.Year %> - <mark>Contact Manager</mark>&lt;/p>
            &lt;/footer>
        &lt;/div>
    &lt;/form>
&lt;/body>
&lt;/html>
</pre>

Plus loin dans ce didacticiel, vous ajouterez la génération de modèles automatique Web Forms, qui créera la page à laquelle renvoie le lien « Contact Demo » ci-dessus.
###Exécution locale de l'application 
1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur la page *Default.aspx* et sélectionnez **Définir comme page de démarrage**. 
2. Appuyez ensuite sur **Ctrl+F5** pour exécuter l’application. La page d’accueil de l’application apparaît dans la fenêtre de navigateur par défaut. ![Contacts - Créer un site Web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms04.png)  

Voilà, vous avez fait tout ce qu'il fallait pour créer l'application que vous allez déployer dans Azure. Plus tard, vous allez ajouter une fonctionnalité de base de données, ainsi que les pages nécessaires pour afficher et modifier les données de contact.
###Déploiement de l'application vers Azure
Maintenant que vous avez créé et exécuté votre application localement, il est temps de la déployer vers Azure.

1. Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le projet, puis dans le menu contextuel, sélectionnez **Publier**. ![Sélectionner Publier](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms05.png) La boîte de dialogue **Publier le site Web** s’affiche.  

2. Sous l’onglet **Profil** de la boîte de dialogue **Publier le site Web**, cliquez sur **Applications Web Azure**. ![Publish Web dialog box](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms06.png)
3. Si vous n’êtes pas déjà connecté, cliquez sur le bouton **Se connecter** dans la boîte de dialogue **Sélectionner une application Web existante**. Une fois que vous êtes connecté, sélectionnez l’application Web que vous avez créée dans la première partie de ce didacticiel. Cliquez sur **OK** pour continuer. ![Boîte de dialogue Sélectionner une application Web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms07.png) Visual Studio télécharge vos paramètres de publication.
4. Dans la boîte de dialogue **Publier le site Web**, cliquez sur **Publier**. ![Publish Web dialog box](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms08.png) Vous verrez l’état global de publication dans la fenêtre **Activité de publication web** de Visual Studio : ![Activité de publication Web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms09.png)  

L'application créée est maintenant exécutée dans le cloud. Lors du prochain déploiement de l’application à partir de Visual Studio, seuls les fichiers modifiés (ou nouveaux) seront déployés. ![App in Browser](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms10.png)

>[AZURE.NOTE]Si vous rencontrez une erreur lors de la publication vers une application Web déjà établie, vous pouvez désactiver l’emplacement avant d’ajouter les nouveaux fichiers. Publiez votre application à nouveau, mais cette fois-ci, dans la boîte de dialogue **Publier le site Web**, sélectionnez l’onglet **Paramètres**. Ensuite, définissez la configuration sur **Débogage** et sélectionnez l'option **Supprimer les fichiers supplémentaires à la destination**. Sélectionnez **Publier** pour redéployer votre application. ![Publish Web dialog box](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms11.png)

##Ajout d'une base de données à l'application 
À présent, vous allez mettre à jour l'application Web Forms pour ajouter la possibilité d'afficher et de mettre à jour les contacts, et de stocker les données dans la base de données par défaut. À la création du projet Web Forms, la base de données a également été créée par défaut. L'application va utiliser Entity Framework pour créer la base de données, et pour lire et mettre à jour les données associées.
###Ajout d'une classe de modèle de données 
Commencez par créer un simple modèle de données avec du code. Ce modèle de données sera contenu dans une classe nommée `Contacts`. Le nom de classe `Contacts` a été choisi de manière à éviter un conflit de noms de classe avec la classe `Contact` contenue dans le fichier Contact.aspx.cs créé par le modèle Web Forms.

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Modèles*, puis sélectionnez **Ajouter** -> **Classe**. ![Sélectionner Classe](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms12.png) La boîte de dialogue **Ajouter un nouvel élément** s’affiche.  

2. Nommez cette nouvelle classe *Contacts.cs*. ![Boîte de dialogue Ajouter un nouvel élément](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13.png)
3. Remplacez le code par défaut par le code suivant :  
	<pre class="prettyprint">
using System.ComponentModel.DataAnnotations;
using System.Globalization;

namespace ContactManager.Models
{
    public class Contacts
    {
        [ScaffoldColumn(false)]
        [Key]
        public int ContactId { get; set; }
        public string Name { get; set; }
        public string Address { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string Zip { get; set; }
        [DataType(DataType.EmailAddress)]
        public string Email { get; set; }
    }
}
</pre>

La classe **Contacts** définit les données que vous allez stocker pour chaque contact, ainsi que la clé primaire (`ContactID`) requise par la base de données. La classe **Contacts** représente les données de contact qui seront affichées. Chaque instance d'un objet Contacts correspondra à une ligne dans une table de base de données relationnelle, et chaque propriété de la classe Contacts mappera vers une colonne de la table de base de données relationnelle. Plus loin dans ce didacticiel, vous réviserez les données de contact contenues dans la base de données.

###Génération de modèles automatique Web Forms 
Vous avez créé la classe de modèle **Contacts** ci-dessus. Vous pouvez maintenant utiliser la génération de modèles automatique Web Forms pour générer les pages *List*, *Insert*, *Edit* et *Delete* utilisées lorsque vous traitez ces données. La génération de modèles automatique Web Forms utilise Entity Framework, Bootstrap et Dynamic Data. Par défaut, Web Forms Scaffolder est installé comme une extension de votre projet dans le cadre du modèle de projet lorsque vous utilisez Visual Studio 2013.

La procédure suivante vous permet d'utiliser Web Forms Scaffolder.

1. Dans la barre de menus de Visual Studio, sélectionnez **Outils** -> **Extensions et mises à jour**. La boîte de dialogue **Extensions et mises à jour** s’affiche.
2. Dans le volet gauche de la boîte de dialogue, sélectionnez **En ligne** -> **Galerie Visual Studio** -> **Outils** -> **Génération de modèles automatique**.
3. Si « Génération de modèles automatique Web Forms » n'apparaît pas dans liste, entrez « Génération de modèles automatique Web Forms » dans la zone de recherche, en haut à droite de la boîte de dialogue.  
4. Si Web Forms Scaffolder n'est pas déjà installé, sélectionnez **Télécharger** pour le télécharger et l'installer. Redémarrez Visual Studio si besoin est. À l’invite, enregistrez vos modifications dans le projet. ![Boîte de dialogue Extensions et mises à jour](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/ExtensionsAndUpdatesDB.png)  
5. Développez le projet (**Ctrl+Maj+B**). Vous devez générer le projet avant d’utiliser le mécanisme de génération de modèle automatique.  
6. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le *projet*, puis sélectionnez **Ajouter** -> **Nouvel élément structuré**. La boîte de dialogue **Ajouter une structure** s’affiche.
7. Sélectionnez **Web Forms** dans le volet de gauche et **Pages Web Forms utilisant Entity Framework** dans le volet central. Cliquez ensuite sur **Add**. ![Boîte de dialogue Ajouter une structure](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13a.png) La boîte de dialogue **Ajouter des pages Web Forms** s’affiche.  

8. Dans la boîte de dialogue **Ajouter des pages Web Forms**, définissez la **Classe de modèle** sur `Contact (ContactManager.Models)`. Définissez la **Classe du contexte de données** sur `ApplicationDbContext (ContactManager.Models)`. Cliquez ensuite sur **Ajouter**. ![Boîte de dialogue Ajouter des pages Web Forms](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13b.png)

Web Forms Scaffolder ajoute un nouveau dossier contenant les pages *Default.aspx*, *Delete.aspx*, *Edit.aspx* et *Insert.aspx*. Il crée également un dossier *DynamicData* contenant un dossier *EntityTemplates* et un dossier *FieldTemplates*. `ApplicationDbContext` sera utilisé à la fois pour la base de données d’appartenance et les données de contact.

###Configuration de l'application pour utiliser le modèle de données 
L'étape suivante consiste à activer la fonctionnalité Migrations Code First pour créer la base de données en fonction du modèle de données que vous avez créé. Vous ajouterez également un exemple de données et un initialisateur de données.

1. Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet**, puis **Console du Gestionnaire de package**. ![Boîte de dialogue Ajouter des pages Web Forms](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13c.png)  
2. Dans la fenêtre Console du Gestionnaire de package, entrez la commande suivante :  
	<pre class="prettyprint">
enable-migrations
</pre>La commande enable-migration crée un dossier *Migrations*, dans lequel elle place un fichier *Configuration.cs* que vous pouvez modifier pour amorcer la base de données et configurer les migrations de données.  
3. Dans la fenêtre **Console du Gestionnaire de package**, entrez la commande suivante :  
	<pre class="prettyprint">
add-migration Initial
</pre>La commande `add-migration Initial` génère un fichier nommé <date_stamp>Initial dans le dossier *Migrations* qui crée la base de données. Le premier paramètre (Initial) est arbitraire et permet de créer le nom du fichier. Les nouveaux fichiers de classe sont affichés dans l’**Explorateur de solutions**. Dans la classe `Initial`, la méthode `Up` crée la table `Contact`, et la méthode `Down` (utilisée lorsque vous voulez revenir à l’état précédent) annule cette table.  
4. Ouvrez le fichier *Migrations\Configuration.cs*. 
5. Ajoutez l’espace de noms suivant :  
	<pre class="prettyprint">
using ContactManager.Models;
</pre>
6. Remplacez la méthode `Seed` par le code suivant :  
	<pre class="prettyprint">
protected override void Seed(ContactManager.Models.ApplicationDbContext context)
{
    context.Contacts.AddOrUpdate(p => p.Name,
       new Contacts
       {
           ContactId = 1,
           Name = "Ivan Irons",
           Address = "One Microsoft Way",
           City = "Redmond",
           State = "WA",
           Zip = "10999",
           Email = "ivani@wideworldimporters.com",
       },
       new Contacts
        {
            ContactId = 2,
            Name = "Brent Scholl",
            Address = "5678 1st Ave W",
            City = "Redmond",
            State = "WA",
            Zip = "10999",
            Email = "brents@wideworldimporters.com",
        },
        new Contacts
        {
            ContactId = 3,
            Name = "Terrell Bettis",
            Address = "9012 State St",
            City = "Redmond",
            State = "WA",
            Zip = "10999",
            Email = "terrellb@wideworldimporters.com",
        },
        new Contacts
        {
            ContactId = 4,
            Name = "Jo Cooper",
            Address = "3456 Maple St",
            City = "Redmond",
            State = "WA",
            Zip = "10999",
            Email = "joc@wideworldimporters.com",
        },
        new Contacts
        {
            ContactId = 5,
            Name = "Ines Burnett",
            Address = "7890 2nd Ave E",
            City = "Redmond",
            State = "WA",
            Zip = "10999",
            Email = "inesb@wideworldimporters.com",
        }
        );
}
</pre>
Ce code initialise (amorce) la base de données avec les informations de contact. Pour plus d’informations sur l’amorçage de la base de données, consultez la page [Amorçage et débogage des bases de données Entity Framework (EF)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).  
7. Dans la **Console du Gestionnaire de package**, entrez la commande suivante :  
	<pre class="prettyprint">
update-database
</pre>
La commande `update-database` exécute la première migration qui crée la base de données. Par défaut, la base de données est créée en tant que base de données SQL Server Express LocalDB. ![Console du gestionnaire de package](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13d.png)

###Exécution locale de l’application et affichage des données 
Exécutez à présent l'application pour voir comment s'affichent les contacts.

1. Commencez par créer le projet (**Ctrl+Maj+B**).  
2. Appuyez sur **Ctrl+F5** pour exécuter l’application. Le navigateur s’ouvre et affiche la page *Default.aspx*.
3. Sélectionnez le lien **Contact Demo** en haut de la page pour afficher la page *Contact List*. ![Page Liste de contacts](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms17.png)  

##Activation du protocole SSL pour le projet 
SSL (Secure Sockets Layer) est un protocole défini pour autoriser les serveurs et clients web à communiquer de manière plus sécurisée grâce au chiffrement. Lorsque SSL n'est pas utilisé, les données envoyées entre le client et le serveur peuvent être soumises à la détection des paquets par quiconque dispose d'un accès physique au réseau. En outre, plusieurs schémas d'authentification ne sont pas sécurisés sur le HTTP standard. En particulier, l'authentification de base et l'authentification par formulaires envoient des informations d'identification non chiffrées. Pour être sécurisés, ces schémas d'authentification doivent utiliser SSL.

1. Dans l'**Explorateur de solutions**, cliquez sur le projet **ContactManager**, puis appuyez sur **F4** pour afficher la fenêtre **Propriétés**. 
2. Définissez **SSL activé** sur `true`. 
3. Copiez l’**URL SSL** afin de pouvoir l’utiliser ultérieurement. L’URL SSL est `https://localhost:44300/` sauf si vous avez déjà créé des applications Web SSL (comme indiqué ci-dessous). ![Propriétés du projet](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms18.png)  
4. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **Contact Manager**, puis sur **Propriétés**.
5. Dans l’onglet de gauche, cliquez sur **Web**.
6. Modifiez l’**URL de projet** pour utiliser l’**URL SSL** que vous avez enregistrée précédemment. ![Propriétés du projet Web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms19.png)  
7. Enregistrez la page en appuyant sur **Ctrl+S**.
8. Appuyez sur **Ctrl+F5** pour exécuter l’application. Visual Studio affiche une option qui vous permet d'éviter les avertissements SSL.  
9. Cliquez sur **Oui** pour approuver le certificat IIS Express SSL et continuer. ![Détails du certificat IIS Express SSL](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms20.png) Un avertissement de sécurité s’affiche.  

10. Cliquez sur **Oui** pour installer le certificat sur votre hôte local (localhost). ![Boîte de dialogue Avertissement de sécurité](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21.png) La fenêtre du navigateur s’affiche.

Vous pouvez tester votre application web localement sans difficulté avec SSL.



##Ajout d'un fournisseur OAuth 2.0 
ASP.NET Web Forms propose des options améliorées pour l'appartenance et l'authentification. Ces améliorations incluent OAuth, un protocole ouvert permettant de mettre en place une authentification sécurisée dans une méthode simple et standardisée à partir d'applications web, mobiles ou de bureau. Le modèle Internet d'ASP.NET MVC utilise OAuth pour exposer Facebook, Twitter, Google et Microsoft comme fournisseurs d'authentification. Même si ce didacticiel utilise uniquement Google comme fournisseur d’authentification, vous pouvez facilement modifier le code pour utiliser n’importe quel autre fournisseur. La procédure à suivre pour implémenter d’autres fournisseurs ressemble assez à celle présentée dans ce didacticiel.

En plus de l’authentification, ce didacticiel va également utiliser des rôles pour l’implémentation d’autorisations. Seuls les utilisateurs que vous ajoutez au rôle `canEdit` pourront créer, modifier ou supprimer des contacts.

Les étapes suivantes vous permettent d'ajouter un fournisseur d'authentification Google.

1. Ouvrez le fichier *App_Start\Startup.Auth.cs*. 
2. Supprimez les caractères de commentaire de la méthode `app.UseGoogleAuthentication()` de telle sorte qu’elle ait l’aspect suivant :  
	<pre class="prettyprint">
            app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions()
            {
                ClientId = "",
                ClientSecret = ""
            });
</pre>
3. Accédez à la [Console développeur de Google](https://console.developers.google.com/). Vous devez également vous connecter avec votre compte de messagerie de développeur Google (gmail.com). Si vous n’avez pas de compte Google, sélectionnez le lien **Créer un compte**. Vous accédez alors à **Google Developers Console**. ![Console développeur de Google](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21a.png)  

4. Cliquez sur le bouton **Create Project (Créer un projet)** et entrez un nom et un ID de projet (vous pouvez utiliser les valeurs par défaut). Cochez ensuite **case du contrat** et cliquez sur le bouton **Créer**. ![Google - Nouveau projet](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21b.png) En quelques secondes, le projet est créé et votre navigateur affiche la nouvelle page de projets.
5. Sous l'onglet de gauche, cliquez sur **APIs & auth (API et authentification)**, puis cliquez sur **Credentials (Informations d'identification)**.
6. Cliquez sur **Créer un identifiant client** sous **OAuth**. La boîte de dialogue **Créer un identifiant client** s’affiche. ![Google - Créer un identifiant client](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21c.png)  
7. Dans la boîte de dialogue **Créer un identifiant client**, conservez le type d’application par défaut **Application Web**.  
8. Pour **Origines JavaScript autorisées**, entrez l’URL SSL que vous avez utilisée précédemment dans ce didacticiel (**https://localhost:44300/**, sauf si vous avez créé d’autres projets SSL). Cette URL est l'origine de votre application. Pour cet exemple, vous entrerez uniquement l'URL de test de l'hôte local (localhost). Vous pouvez cependant entrer plusieurs URL pour l’hôte local (localhost) et la production.  

9. Définissez les **URI de redirection autorisés** comme suit :
	<pre class="prettyprint">  
https://localhost:44300/signin-google  
</pre>Cette valeur est l’URI que le protocole OAuth ASP.NET utilise pour communiquer avec le serveur OAuth Google. Souvenez-vous de l’URL SSL que vous avez utilisée précédemment (**https://localhost:44300/**, sauf si vous avez créé d’autres projets SSL).  
10. Cliquez sur le bouton **Create Client ID (Créer un ID de client)**.
11. Dans Visual Studio, mettez à jour la méthode `UseGoogleAuthentication` de la page *Startup.Auth.cs* en copiant et collant les valeurs **AppId** et **App Secret** dans la méthode. Les valeurs **AppId** et **App Secret** indiquées ci-après sont de simples exemples et ne fonctionneront pas.  
	<pre class="prettyprint">  
using System;
using Microsoft.AspNet.Identity;
using Microsoft.AspNet.Identity.EntityFramework;
using Microsoft.AspNet.Identity.Owin;
using Microsoft.Owin;
using Microsoft.Owin.Security.Cookies;
using Microsoft.Owin.Security.DataProtection;
using Microsoft.Owin.Security.Google;
using Owin;
using ContactManager.Models;

namespace ContactManager
{
    public partial class Startup {

        // Pour plus d’informations sur la configuration de l’authentification, visitez la page http://go.microsoft.com/fwlink/?LinkId=301883
        public void ConfigureAuth(IAppBuilder app)
        {
            // Configurer le gestionnaire de contexte et d’utilisateur de base de données pour utiliser une seule instance par demande
            app.CreatePerOwinContext(ApplicationDbContext.Create);
            app.CreatePerOwinContext<ApplicationUserManager>(ApplicationUserManager.Create);

            // Autoriser l’application à utiliser un cookie pour stocker les informations de l’utilisateur connecté
            // et à utiliser un cookie pour stocker temporairement des informations sur la connexion d’un utilisateur par le biais d’un fournisseur de connexion tiers
            // Configurer le cookie de connexion
            app.UseCookieAuthentication(new CookieAuthenticationOptions
            {
                AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
                LoginPath = new PathString("/Account/Login"),
                Provider = new CookieAuthenticationProvider
                {
                    OnValidateIdentity = SecurityStampValidator.OnValidateIdentity&lt;ApplicationUserManager, ApplicationUser>(
                        validateInterval: TimeSpan.FromMinutes(20),
                        regenerateIdentity: (manager, user) => user.GenerateUserIdentityAsync(manager))
                }
            });
            // Use a cookie to temporarily store information about a user logging in with a third party login provider
            app.UseExternalSignInCookie(DefaultAuthenticationTypes.ExternalCookie);

            // Supprimer les lignes suivantes pour activer la connexion avec des fournisseurs de connexion tiers
            //app.UseMicrosoftAccountAuthentication(
            //    clientId: "",
            //    clientSecret: "");

            //app.UseTwitterAuthentication(
            //   consumerKey: "",
            //   consumerSecret: "");

            //app.UseFacebookAuthentication(
            //   appId: "",
            //   appSecret: "");

            app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions()
            {
                ClientId = "<mark>000000000000.apps.googleusercontent.com</mark>",
                ClientSecret = "<mark>00000000000</mark>"
            });
        }
    }
}
</pre>
12. Appuyez sur **Ctrl+F5** pour générer et exécuter l’application. Cliquez sur le lien **Ouvrir une session**.
13. Sous **Utiliser un autre service pour ouvrir une session**, cliquez sur le bouton **Google**. ![Connexion](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21d.png)  
14. Si vous devez entrer vos informations d’identification, vous êtes redirigé vers le site Google approprié. ![Google - Se connecter](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21e.png)  
15. Après avoir entré vos informations d’identification, vous êtes invité à accorder des autorisations pour l’application Web que vous venez de créer : ![Compte de service de projet par défaut](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21f.png)  
16. Cliquez sur **Accepter**. Vous êtes à présent redirigé vers la page **Enregistrer** de l’application **ContactManager** qui vous permet d’inscrire votre compte Google. ![S’inscrire avec un compte Google](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21g.png)  
17. Vous avez la possibilité de changer le nom d'inscription local utilisé pour votre compte Gmail, mais, en règle générale, les utilisateurs préfèrent conserver l'alias de messagerie par défaut (c'est-à-dire celui que vous avez utilisé pour l'authentification). Cliquez sur **Ouvrir une session**.

##Utilisation de l'API d'appartenance pour limiter l'accès 
ASP.NET Identity est le système d'appartenance utilisé pour l'authentification lors de la création d'une application web ASP.NET. Il facilite l'intégration des données de profil propres à l'utilisateur aux données de l'application. ASP.NET Identity vous permet également de choisir le modèle de persistance pour les profils utilisateur dans votre application. Vous pouvez stocker les données dans une base de données SQL Server ou un autre magasin de données, y compris dans des magasins de données *NoSQL* tels que des tables Azure Storage.

En utilisant le modèle ASP.NET Web Forms par défaut, vous disposez d'une fonctionnalité d'appartenance intégrée dont vous pouvez vous servir immédiatement lorsque l'application s'exécute. Vous allez utiliser ASP.NET Identity pour ajouter un rôle d'administrateur et affecter un utilisateur à ce rôle. Ensuite, vous apprendrez à limiter l'accès au dossier d'administration et aux pages de ce dossier qui sont utilisées pour modifier les données de contact.

###Ajout d'un administrateur 
ASP.NET Identity vous permet d'ajouter un rôle d'administrateur et d'affecter un utilisateur à ce rôle avec du code.

1. Dans l’**Explorateur de solutions**, ouvrez le fichier *Configuration.cs* dans le dossier *Migrations*.
2. Ajoutez les instructions `using` suivantes dans l’espace de noms `ContactManger.Migrations` :  
	<pre class="prettyprint">
using Microsoft.AspNet.Identity;
using Microsoft.AspNet.Identity.EntityFramework;
</pre>
3. Ajoutez la méthode `AddUserAndRole` suivante dans la classe `Configuration` après la méthode `Seed` :  
	<pre class="prettyprint">
    public void AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
    {
        IdentityResult IdRoleResult;
        IdentityResult IdUserResult;

        var roleStore = new RoleStore&lt;IdentityRole>(context);
        var roleMgr = new RoleManager&lt;IdentityRole>(roleStore);

        if (!roleMgr.RoleExists("canEdit"))
        {
            IdRoleResult = roleMgr.Create(new IdentityRole { Name = "canEdit" });
        }

        //var userStore = new UserStore&lt;ApplicationUser>(context);
        //var userMgr = new UserManager&lt;ApplicationUser>(userStore);
        var userMgr = new UserManager&lt;ApplicationUser>(new UserStore&lt;ApplicationUser>(context));

        var appUser = new ApplicationUser
        {
            UserName = "canEditUser@wideworldimporters.com",
            Email = "canEditUser@wideworldimporters.com"
        };
        IdUserResult = userMgr.Create(appUser, "Pa$$word1");

        if (!userMgr.IsInRole(userMgr.FindByEmail("canEditUser@wideworldimporters.com").Id, "canEdit"))
        {
          //  IdUserResult = userMgr.AddToRole(appUser.Id, "canEdit");
            IdUserResult = userMgr.AddToRole(userMgr.FindByEmail("canEditUser@wideworldimporters.com").Id, "canEdit");
        }
    }
</pre>
4. Ajoutez un appel à la méthode `AddUserAndRole` au début de la méthode `Seed`. Notez que seul le début de la méthode `Seed` est affiché.  
	<pre class="prettyprint">
    protected override void Seed(ContactManager.Models.ApplicationDbContext context)
    {
        <mark>AddUserAndRole(context);</mark>
</pre>
5. Après avoir enregistré vos modifications, dans la **Console du Gestionnaire de package**, exécutez la commande suivante :  
	<pre class="prettyprint">
Update-Database
</pre>Ce code crée un rôle nommé `canEdit` et un utilisateur local grâce à l’e-mail de canEditUser@wideworldimporters.com. Ensuite, le code ajoute canEditUser@wideworldimporters.com au rôle `canEdit`. Pour plus d’informations, consultez la page de ressources [ASP.NET Identity](http://www.asp.net/identity).

###Limitation de l’accès au dossier d’administration 
L'exemple d'application **ContactManager** permet aux utilisateurs anonymes et aux utilisateurs connectés d'afficher les contacts. Une fois cette section terminée, cependant, les utilisateurs connectés qui sont affectés au rôle « canEdit » seront les seuls utilisateurs en mesure de modifier les contacts.

Vous allez créer un dossier nommé *Admin* auquel seuls les utilisateurs affectés au rôle « canEdit » pourront accéder.

1. Dans l'**Explorateur de solutions**, ajoutez un sous-dossier au dossier *Contacts* et nommez-le *Admin*.
2. Déplacez les fichiers suivants du dossier *Contacts* vers le dossier *Contacts/Admin* :  
	- *Delete.aspx *et* Delete.aspx.cs*
	- *Edit.aspx *et* Edit.aspx.cs*
	- *Insert.aspx *et* Insert.aspx.cs*
3. Mettez à jour les références de lien dans *Contacts/Default.aspx* en ajoutant « Admin/ » devant les références de pages qui renvoient à *Insert.aspx*, *Edit.aspx* et *Delete.aspx* :  
	<pre class="prettyprint">
&lt;%@ Page Title="ContactsList" Language="C#" MasterPageFile="~/Site.Master" CodeBehind="Default.aspx.cs" Inherits="ContactManager.Contacts.Default" ViewStateMode="Disabled" %>
&lt;%@ Register TagPrefix="FriendlyUrls" Namespace="Microsoft.AspNet.FriendlyUrls" %>

&lt;asp:Content runat="server" ContentPlaceHolderID="MainContent">
    &lt;h2>Contacts List&lt;/h2>
    &lt;p>
        &lt;asp:HyperLink runat="server" NavigateUrl="<mark>Admin/</mark>Insert.aspx" Text="Create new" />
    &lt;/p>
    &lt;div>
        &lt;asp:ListView runat="server"
            DataKeyNames="ContactId" ItemType="ContactManager.Models.Contacts"
            AutoGenerateColumns="false"
            AllowPaging="true" AllowSorting="true"
            SelectMethod="GetData">
            &lt;EmptyDataTemplate>
                Il n’y a aucune entrée pour Contacts
            &lt;/EmptyDataTemplate>
            &lt;LayoutTemplate>
                &lt;table class="table">
                    &lt;thead>
                        &lt;tr>
                            &lt;th>Nom&lt;/th>
                            &lt;th>Adresse&lt;/th>
                            &lt;th>Ville&lt;/th>
                            &lt;th>État&lt;/th>
                            &lt;th>Code postal&lt;/th>
                            &lt;th>E-mail&lt;/th>
                            &lt;th>&amp;nbsp;&lt;/th>
                        &lt;/tr>
                    &lt;/thead>
                    &lt;tbody>
                        &lt;tr runat="server" id="itemPlaceholder" />
                    &lt;/tbody>
                &lt;/table>
            &lt;/LayoutTemplate>
            &lt;ItemTemplate>
                &lt;tr>
                    &lt;td>
                        &lt;asp:DynamicControl runat="server" DataField="Name" ID="Name" Mode="ReadOnly" />
                    &lt;/td>
                    &lt;td>
                        &lt;asp:DynamicControl runat="server" DataField="Address" ID="Address" Mode="ReadOnly" />
                    &lt;/td>
                    &lt;td>
                        &lt;asp:DynamicControl runat="server" DataField="City" ID="City" Mode="ReadOnly" />
                    &lt;/td>
                    &lt;td>
                        &lt;asp:DynamicControl runat="server" DataField="State" ID="State" Mode="ReadOnly" />
                    &lt;/td>
                    &lt;td>
                        &lt;asp:DynamicControl runat="server" DataField="Zip" ID="Zip" Mode="ReadOnly" />
                    &lt;/td>
                    &lt;td>
                        &lt;asp:DynamicControl runat="server" DataField="Email" ID="Email" Mode="ReadOnly" />
                    &lt;/td>
                    &lt;td>
                        &lt;a href="<mark>Admin/</mark>Edit.aspx?ContactId=&lt;%#: Item.ContactId%>">Edit&lt;/a> | 
                        &lt;a href="<mark>Admin/</mark>Delete.aspx?ContactId=&lt;%#: Item.ContactId%>">Delete&lt;/a>
                    &lt;/td>
                &lt;/tr>
            &lt;/ItemTemplate>
        &lt;/asp:ListView>
    &lt;/div>
&lt;/asp:Content>
</pre>
4. Mettez à jour les six références du code `Response.Redirect("Default.aspx")` vers `Response.Redirect("~/Contacts/Default.aspx")` pour les trois fichiers suivants :  
	- *Delete.aspx.cs*
	- *Edit.aspx.cs*
	- *Insert.aspx.cs*  

	Dorénavant, les liens fonctionneront correctement lorsque vous afficherez et mettrez à jour les données de contact.
5. Pour limiter l'accès au dossier *Admin*, dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Admin* et sélectionnez **Ajouter un nouvel élément**.
6. Dans la liste des modèles web Visual C#, sélectionnez **Fichier de configuration Web** dans la liste du milieu, acceptez le nom par défaut *Web.config*, puis sélectionnez **Ajouter**.
7. Remplacez le contenu XML existant dans le fichier *Web.config* par le code suivant :
	<pre class="prettyprint">
&lt;?xml version="1.0"?>
&lt;configuration>
  &lt;system.web>
    &lt;authorization>
      &lt;allow roles="canEdit"/>
      &lt;deny users="*"/>
    &lt;/authorization>
  &lt;/system.web>
&lt;/configuration>
</pre>
8. Enregistrez le fichier *Web.config*. Le fichier *Web.config* spécifie que seuls les utilisateurs affectés au rôle « canEdit » peuvent accéder aux pages du dossier *Admin*. 

Lorsqu'un utilisateur qui n'est pas affecté au rôle « canEdit » essaie de modifier les données, il est redirigé vers la page *Ouvrir une session*.

##Déploiement de l'application avec la base de données vers Azure 
Maintenant que l'application web est terminée, vous pouvez la publier sur Azure.

###Publication de l'application 
1. Dans Visual Studio, créez le projet (**Ctrl+Maj+B**).
2. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**. ![Option du menu Publier](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms22.png) La boîte de dialogue **Publier le site Web** s’affiche. ![Publish Web dialog box](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms22a.png)  
3. Sous l’onglet **Profil**, sélectionnez **Application web Azure** comme cible de publication, si cette option n’est pas déjà sélectionnée. ![Publish Web dialog box](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms23.png)  
4. Cliquez sur **Se connecter** si vous n'êtes pas encore connecté.
5. Sélectionnez l’application Web que vous avez créée précédemment dans ce didacticiel à partir de la zone de liste déroulante **Applications web existantes**, puis cliquez sur le bouton **OK**. ![Boîte de dialogue Sélectionner une application Web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms25.png) Si vous êtes invité à enregistrer les modifications apportées au profil, sélectionnez **Oui**.
6. Cliquez sur l’onglet **Paramètres**. ![Boîte de dialogue Sélectionner une application Web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms26.png)  
7. Dans la zone de liste déroulante **Configuration**, sélectionnez **Débogage**.
8. Cliquez sur la **flèche vers le bas** en regard de la zone **ApplicationDbContext** et sélectionnez **ContactDB**.
9. Activez la case à cocher **Exécuter les premières migrations du code**.  

	>[AZURE.NOTE]Dans cet exemple, vous devez cocher cette case uniquement lors de la première publication de l'application. Ainsi, la méthode *Seed* dans le fichier *Configuration.cs* ne sera appelée qu'une seule fois.

10. Cliquez ensuite sur **Publier**. Votre application est publiée dans Azure.

>[AZURE.NOTE]Si vous avez fermé puis rouvert Visual Studio après avoir créé le profil de publication, il est possible que la chaîne de connexion ne figure pas dans la liste déroulante. Dans ce cas, au lieu de modifier le profil de publication créé précédemment, créez-en un autre comme auparavant, puis suivez la procédure sous l’onglet **Paramètres**.

###Révision de l'application dans Azure 
1. Dans le navigateur, cliquez sur le lien **Contact Demo**. La liste de contacts s’affiche. ![Contacts figurant dans le navigateur](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms27.png)  

2. Sélectionnez **Créer** sur la page **Liste de contacts**. ![Contacts figurant dans le navigateur](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms29.png) Vous êtes redirigé vers la page **Connexion**, car vous n’êtes pas connecté avec un compte qui permet de modifier les contacts.
3. Après avoir entré l’adresse de messagerie et le mot de passe ci-dessous, cliquez sur le bouton **Ouvrir une session**. **Adresse de messagerie** : `canEditUser@wideworldimporters.com` **Mot de passe** : `Pa$$word1` ![Log in Page](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms28.png)  

4. Entrez de nouvelles données pour chaque champ et appuyez sur le bouton **Insérer**. ![Page Ajouter un contact](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms30.png) La page *EditContactList.aspx* s’affiche avec le nouvel enregistrement. ![Page Ajouter un contact](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms31.png)

5. Cliquez sur le lien **Fermer la session**.

###Arrêt de l'application 
Pour empêcher les autres utilisateurs de s’inscrire et d’utiliser votre exemple d’application, vous allez arrêter l’application Web.

1. Dans le menu **Affichage** de Visual Studio, cliquez sur **Explorateur de serveurs**. 
2. Dans l’**Explorateur de serveurs**, accédez à **Application Web**.
3. Cliquez avec le bouton droit sur chaque instance d’application Web, puis sélectionnez **Arrêter l’application Web**. ![Élément de menu Arrêter le site web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms26a.png)  

	Vous pouvez aussi sélectionner l’application Web à partir du portail de gestion Microsoft Azure, puis cliquer sur l’icône **arrêter** en bas de la page. ![Page Ajouter un contact](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms26b.png)

##Révision de la base de données 
Il est important de savoir comment afficher et modifier directement la base de données. Le fait de savoir travailler directement avec la base de données vous permet de valider les données qu'elle contient et de comprendre comment les données sont stockées dans chaque table.

###Examen de la base de données SQL Azure 
1. Dans Visual Studio, ouvrez l'**Explorateur de serveurs** et accédez à **ContactDB**.
2. Cliquez avec le bouton droit sur **ContactDB**, puis sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**. ![Élément de menu Ouvrir dans l’Explorateur d’objets SQL Server](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms32.png)  
3. Si la boîte de dialogue **Ajouter une règle de pare-feu** est affichée, sélectionnez **Ajouter une règle de pare-feu**.  

	>[AZURE.NOTE]Si vous ne pouvez pas développer **Bases de données SQL** et afficher la base de données **ContactDB** dans Visual Studio, suivez les instructions pour ouvrir un ou plusieurs ports de pare-feu. À cet effet, suivez les instructions de la rubrique **Configuration des règles de pare-feu Azure** vers la fin du [didacticiel sur MVC](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Une autre méthode consiste à réviser les données de la base de données locale en créant, exécutant et ajoutant des données à l’application localement (**Ctrl+F5** dans Visual Studio).

4. Si la boîte de dialogue **Se connecter au serveur** est affichée, entrez le **mot de passe** que vous avez créé au début de ce didacticiel et appuyez sur le bouton **Se connecter**.

	>[AZURE.NOTE]**Explorateur de solutions***Propriétés**PublishProfiles**ContactManager.pubxml*
5. Développez la base de données **contactDB**, puis développez **Tables**.
6. Cliquez avec le bouton droit sur la table **dbo.AspNetUsers**, puis sélectionnez **Afficher les données**. ![Élément de menu Afficher les données](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms34.png) Vous pouvez afficher les données associées à l’utilisateur canEditUser@contoso.com. ![Fenêtre ContactManager](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms35.png)  

###Ajout d'un utilisateur au rôle admin en modifiant la base de données 
Précédemment dans ce didacticiel, vous avez utilisé du code pour ajouter des utilisateurs au rôle « peutModifier ». Il existe une autre méthode consistant à manipuler directement les données des tables d’appartenance. La procédure suivante indique comment utiliser cette méthode pour ajouter un utilisateur à un rôle.

1. Dans l’**Explorateur d’objets SQL Server**, cliquez avec le bouton droit sur **dbo.AspNetUserRoles**, puis sélectionnez **Afficher les données**. ![Données AspNetUserRoles](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms36.png)  
2. Copiez la valeur *RoleId* et collez-la dans la (nouvelle) ligne vide. ![Données AspNetUserRoles](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms37.png)  
3. Dans la table **dbo.AspNetUsers**, recherchez l'utilisateur que vous voulez affecter au rôle et copiez l'*Id* associé.
4. Collez l'*Id* copié dans le champ **UserId** du nouveau rôle dans la table **AspNetUserRoles**.  

>[AZURE.NOTE]Nous développons actuellement un outil qui facilitera la gestion des utilisateurs et des rôles.

##Étapes suivantes
Pour plus d’informations sur ASP.NET Web Forms, consultez la page [Présentation d’ASP.NET Web Forms](http://www.asp.net/web-forms) (en anglais) sur l’application Web ASP.NET et la page dédiée aux [didacticiels et guides de Microsoft Azure](http://azure.microsoft.com/documentation/services/web-sites/#net).

Ce didacticiel a été réalisé à partir du didacticiel MVC [Créer une application ASP.NET MVC avec authentification et base de données SQL et la déployer dans Azure App Service](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md) rédigé par Rick Anderson (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)), assisté de Tom Dykstra et Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)).

N'hésitez pas à nous transmettre vos commentaires sur ce qui vous a plu et ce qui pourrait être amélioré... pas seulement à propos de ce didacticiel, mais aussi en ce qui concerne les produits présentés ici. Vos commentaires nous aideront à orienter nos améliorations. Vous pouvez aussi demander de nouvelles rubriques et noter les rubriques existantes sur [Leçons de code](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

 

<!---HONumber=62-->