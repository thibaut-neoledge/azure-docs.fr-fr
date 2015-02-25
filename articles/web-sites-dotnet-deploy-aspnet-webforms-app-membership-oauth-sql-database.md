<properties 
	pageTitle="Déploiement d'une application ASP.NET Web Forms sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site Web Azure" 
	description="Ce didacticiel vous montre comment créer une application Web ASP.NET 4.5 Web Forms sécurisée qui intègre une base de données SQL et comment la déployer vers Azure." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="Erikre" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="erikre"/>


# Déploiement d'une application ASP.NET Web Forms sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site Web Azure


Ce didacticiel vous montre comment créer une application Web ASP.NET 4.5 Web Forms sécurisée qui intègre une base de données SQL et comment la déployer vers Azure. 

>[AZURE.NOTE] 
Pour obtenir une version MVC de ce didacticiel, consultez la page [Déploiement d'une application ASP.NET MVC 5 sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site Web Azure](http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/).

Vous pouvez ouvrir gratuitement un compte Azure. Si vous n'avez pas déjà Visual Studio 2013, le Kit de développement logiciel (SDK) installe automatiquement Visual Studio Express 2013 pour le Web. Vous pouvez commencer vos développements Azure gratuitement.

Ce didacticiel part du principe que vous n'avez pas d'expérience en tant qu'utilisateur de Microsoft Azure. À la fin de ce didacticiel, vous disposerez d'une application Web fonctionnelle dans le cloud qui utilise une base de données du cloud.

Vous allez découvrir comment :

- créer un projet ASP.NET 4.5 Web Forms et le publier sur un site Web Azure ;
- utiliser OAuth et l'appartenance ASP.NET pour sécuriser votre application ;
- utiliser une seule base de données pour les données d'appartenance et pour les données de l'application ;
- utiliser la génération de modèles Web Forms pour créer des pages Web qui vous permettent de modifier les données ;
- utiliser la nouvelle API d'appartenance pour ajouter des utilisateurs et des rôles ;
- utiliser une base de données SQL pour stocker des données dans Azure ;

Vous allez développer une simple application Web de liste de contacts basée sur ASP.NET 4.5 Web Forms qui utilise Entity Framework pour accéder à la base de données. L'image suivante représente la page Web Forms qui permet d'accéder en lecture et en écriture à la base de données :

![Contacts - Edit Page](./media/web-sites-dotnet-web-forms-secure/SecureWebForms00.png)

>[AZURE.NOTE] 
Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez <a href="/fr-fr/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">activer les avantages de votre abonnement MSDN</a> ou <a href="/fr-fr/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">obtenir une évaluation gratuite</a>. Si vous voulez prendre en main Sites Web Azure avant de créer un compte, accédez à <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, où vous pouvez immédiatement et gratuitement créer un site de départ ASP.NET de courte durée dans Azure Web Sites. Aucune carte de crédit n'est requise, vous ne prenez aucun engagement.

Ce didacticiel contient les sections suivantes :

- [Configuration de l'environnement de développement](#set-up-the-development-environment)
- [Configuration de l'environnement Azure](#Set-up-the-Azure-environment)
- [Création d'une application ASP.NET Web Forms](#Create-an-ASP.NET-Web-Forms-Application)
- [Ajout d'une base de données à l'application](#Add-a-Database-to-the-Application)
- [Activation du protocole SSL pour le projet](#Enable-SSL-for-the-Project)
- [Ajout d'un fournisseur OAuth 2.0](#Add-an-OAuth-2.0-Provider)
- [Utilisation de l'API d'appartenance pour limiter l'accès](#Use-the-Membership-API-to-Restrict-Access)
- [Déploiement de l'application avec la base de données vers Azure](#Deploy-the-Application-with-the-Database-to-Azure)
- [Révision de la base de données](#Review-the-Database)
- [Étapes suivantes](#Next-Steps)

##Configuration de l'environnement de développement 
Pour commencer, configurez l'environnement de développement en installant Visual Studio 2013 et le Kit de développement logiciel (SDK) Azure pour .NET.

1. Installez [Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=306566) si vous ne l'avez pas encore installé.  
2. Installez le [Kit de développement logiciel (SDK) Azure pour Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409). Ce didacticiel nécessite Visual Studio 2013 avant l'installation du Kit de développement logiciel (SDK) Azure pour Visual Studio 2013.  

	>[AZURE.NOTE]  
	En fonction du nombre de dépendances du Kit de développement logiciel (SDK) qui se trouvent déjà sur votre ordinateur, l'installation du SDK peut prendre un certain temps (de plusieurs minutes à une demi-heure, voire plus).  

3. Lorsque vous êtes invité à exécuter ou à enregistrer l'exécutable d'installation, cliquez sur **Exécuter**.
4. Dans la fenêtre **Web Platform Installer**, cliquez sur **Installer**, puis poursuivez l'installation.  
	![Web Platform Installer](./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-01.png)  

	>[AZURE.NOTE]  
	Si vous avez déjà installé le Kit de développement logiciel (SDK), il ne vous reste plus rien à installer. Le nombre d'éléments à installer est affiché en bas à gauche dans la fenêtre **Web Platform Installer**.  

5. Si vous n'avez pas encore **Visual Studio Update 2**, téléchargez et installez **[Visual Studio 2013 Update 2](http://www.microsoft.com/download/details.aspx?id=42666)** ou une version supérieure.  

	>[AZURE.NOTE]  
	Vous devez installer Visual Studio 2013 Update 2 ou une version supérieure pour utiliser Google OAuth 2.0 et pour utiliser SSL localement sans avertissement. Update 2 est également requis pour utiliser la génération de modèles automatique Web Forms.  

Une fois l'installation terminée, vous disposez de tous les éléments nécessaires pour commencer le développement.

##Configuration de l'environnement Azure
Dans cette section, vous allez configurer l'environnement Azure en créant un site Web Azure et une base de données SQL dans Azure.

###Création d'un site Web et d'une base de données SQL dans Azure 
Dans ce didacticiel, votre site Web Azure est exécuté dans un environnement d'hébergement partagé, ce qui signifie qu'il s'exécute sur des machines virtuelles partagées avec d'autres sites Web Azure. Un environnement d'hébergement partagé permet de commencer à utiliser le cloud à moindres frais. Plus tard, si votre trafic Web augmente, vous pourrez mettre votre application à l'échelle pour répondre à vos besoins, en exécutant des machines virtuelles dédiées. Si vous avez besoin d'une architecture plus complexe, vous pouvez migrer vers un service cloud Azure. Les services cloud sont exécutés sur des machines virtuelles dédiées pour vous permettre de les configurer selon vos besoins. 

La base de données SQL Azure est un service de bases de données relationnelles sur le cloud, basé sur les technologies SQL Server. Les outils et applications fonctionnant avec SQL Server fonctionnent également avec la base de données SQL.

1. Dans le [portail de gestion Azure](https://manage.windowsazure.com/), sous l'onglet gauche, cliquez sur **Sites Web**, puis sur **Nouveau**.  
	![Web Platform Installer](./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-02.png)
2. Cliquez sur **Site Web**, puis sur **Création personnalisée**.  
	![Custom Create](./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-03.png)  
	L'Assistant **Nouveau site Web - Création personnalisée** s'ouvre.  

3. À l'étape **Créer un site Web** de l'Assistant, entrez dans la zone **URL** la chaîne à utiliser en tant qu'URL unique pour votre application. L'URL complète se composera du texte entré dans cette zone, ainsi que du suffixe affiché en regard de la zone. L'illustration montre une URL qui est probablement prise ; **vous devez donc en choisir une autre**.  
	![Contacts - Create New Web Site](./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-04.png)  
4. Dans la liste Plan d'hébergement Web, sélectionnez la région la plus proche de l'endroit où vous vous trouvez. Ce paramètre indique le centre de données où votre machine virtuelle sera exécutée.
5. Dans la liste déroulante **Base de données**, choisissez **Créer une base de données SQL gratuite de 20 Mo**.
6. Dans la zone **Nom de la chaîne de connexion à la base de données**, ne changez pas la valeur par défaut  *DefaultConnection*.
7. Cliquez sur la flèche située en bas de la boîte de dialogue. 
L'Assistant passe à l'étape **Spécifier les paramètres de la base de données**.
8. Dans la zone **Nom**, entrez *`ContactDB`*.  
	![Database Settings](./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-05.png)  
9. Dans la zone **Serveur**, sélectionnez **Nouveau serveur de base de données SQL**.
Par ailleurs, si vous avez déjà créé une base de données SQL Server, vous pouvez la sélectionner dans la liste déroulante.
10. Sous **Région**, sélectionnez la même région que celle utilisée pour la création du site Web.
11. Entrez un **Nom de connexion** et un **Mot de passe** d'administrateur. 
Si vous avez sélectionné **Nouveau serveur de base de données SQL**, vous ne devez pas entrer un nom et un mot de passe existant ici, mais une nouvelle paire nom/mot de passe que vous allez choisir maintenant et utiliser plus tard quand vous accèderez à la base de données. Si vous avez sélectionné une base de données SQL Server créée auparavant, vous devez entrer le mot de passe et le nom du compte SQL Server que vous aviez créé. Pour ce didacticiel, ne cochez pas la case **Avancé**.
12. Cliquez sur la coche située dans le coin inférieur droit de la zone pour indiquer que vous avez terminé.

Le **portail de gestion Azure** revient à la page **Sites Web** et la colonne **Statut** indique que le site est en cours de création. Après un temps d'attente (généralement inférieur à une minute), la colonne **Statut** indique que le site a été créé correctement. Dans la barre de navigation de gauche, le nombre de sites de votre compte apparaît en regard de l'icône **Sites Web** et le nombre de bases de données en regard de l'icône **Bases de données SQL**.
##Création d'une application ASP.NET Web Forms 
Vous avez créé un site Web Azure, mais il est encore vide. L'étape suivante consiste à créer l'application Web Visual Studio que vous allez publier sur Azure.
###Création du projet 
1. Sélectionnez **Nouveau projet** dans le menu **Fichier** dans Visual Studio.  
	![File Menu - New Project](./media/web-sites-dotnet-web-forms-secure/SecureWebForms01.png)  
2. Sélectionnez le groupe de modèles **Modèles** -> **Visual C#** -> **Web** sur la gauche. 
3. Choisissez le modèle **Application Web ASP.NET** dans la colonne centrale.
4. Nommez le projet  *ContactManager* et cliquez sur **OK**.  
	![New Project Dialog](./media/web-sites-dotnet-web-forms-secure/SecureWebForms02.png)  

	>[AZURE.NOTE]  
	Le nom du projet dans cette série de didacticiels est **ContactManager**. Il est recommandé d'utiliser exactement ce nom de projet pour que le code fourni dans cette série de didacticiels puisse fonctionner comme prévu.  

5. Dans la boîte de dialogue **Nouveau projet ASP.NET**, sélectionnez le modèle **Web Forms**. Décochez la case **Hôte dans le cloud** si elle est activée et cliquez sur **OK**.  
	![New ASP.NET Project dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms03.png)  
	L'application Web Forms est créée.
###Mise à jour de la page maître
Dans ASP.NET Web Forms, les pages maîtres vous permettent de créer une mise en page homogène pour les pages de votre application. Une seule page maître définit la présentation et le comportement standard voulus pour toutes les pages (ou un groupe de pages) dans votre application. Vous pouvez ensuite créer des pages de contenu individuelles avec le contenu que vous souhaitez afficher. Lorsque les utilisateurs demandent les pages de contenu, ASP.NET les fusionne avec la page maître pour produire une sortie qui associe la disposition de la page maître et le contenu de la page de contenu.
Le nouveau site a besoin du nom de l'application et d'un lien mis à jour. Le lien pointera vers une page qui affichera les coordonnées des contacts. Pour effectuer ces modifications, vous devez modifier le code HTML sur la page maître. 

1. Dans l'**Explorateur de solutions**, recherchez et ouvrez la page  *Site.Master*. 
2. Si la page est en mode **Création**, passez en mode **Source**.
3. Mettez à jour la page maître en modifiant ou en ajoutant le balisage surligné en jaune :

<pre class="prettyprint">
&lt;%@ Master Language=&quot;C#&quot; AutoEventWireup=&quot;true&quot; CodeBehind=&quot;Site.master.cs&quot; Inherits=&quot;ContactManager.SiteMaster&quot; %&gt;

&lt;!DOCTYPE html&gt;

&lt;html lang=&quot;en&quot;&gt;
&lt;head runat=&quot;server&quot;&gt;
    &lt;meta charset=&quot;utf-8&quot; /&gt;
    &lt;meta name=&quot;viewport&quot; content=&quot;width=device-width, initial-scale=1.0&quot; /&gt;
    &lt;title&gt;&lt;%: Page.Title %&gt; - <mark>Contact Manager</mark>&lt;/title&gt;

    &lt;asp:PlaceHolder runat=&quot;server&quot;&gt;
        &lt;%: Scripts.Render(&quot;~/bundles/modernizr&quot;) %&gt;
    &lt;/asp:PlaceHolder&gt;
    &lt;webopt:bundlereference runat=&quot;server&quot; path=&quot;~/Content/css&quot; /&gt;
    &lt;link href=&quot;~/favicon.ico&quot; rel=&quot;shortcut icon&quot; type=&quot;image/x-icon&quot; /&gt;

&lt;/head&gt;
&lt;body&gt;
    &lt;form runat=&quot;server&quot;&gt;
        &lt;asp:ScriptManager runat=&quot;server&quot;&gt;
            &lt;Scripts&gt;
                &lt;%--To learn more about bundling scripts in ScriptManager see http://go.microsoft.com/fwlink/?LinkID=301884 --%&gt;
                &lt;%--Framework Scripts--%&gt;
                &lt;asp:ScriptReference Name=&quot;MsAjaxBundle&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;jquery&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;bootstrap&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;respond&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;WebForms.js&quot; Assembly=&quot;System.Web&quot; Path=&quot;~/Scripts/WebForms/WebForms.js&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;WebUIValidation.js&quot; Assembly=&quot;System.Web&quot; Path=&quot;~/Scripts/WebForms/WebUIValidation.js&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;MenuStandards.js&quot; Assembly=&quot;System.Web&quot; Path=&quot;~/Scripts/WebForms/MenuStandards.js&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;GridView.js&quot; Assembly=&quot;System.Web&quot; Path=&quot;~/Scripts/WebForms/GridView.js&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;DetailsView.js&quot; Assembly=&quot;System.Web&quot; Path=&quot;~/Scripts/WebForms/DetailsView.js&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;TreeView.js&quot; Assembly=&quot;System.Web&quot; Path=&quot;~/Scripts/WebForms/TreeView.js&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;WebParts.js&quot; Assembly=&quot;System.Web&quot; Path=&quot;~/Scripts/WebForms/WebParts.js&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;Focus.js&quot; Assembly=&quot;System.Web&quot; Path=&quot;~/Scripts/WebForms/Focus.js&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;WebFormsBundle&quot; /&gt;
                &lt;%--Site Scripts--%&gt;
            &lt;/Scripts&gt;
        &lt;/asp:ScriptManager&gt;

        &lt;div class=&quot;navbar navbar-inverse navbar-fixed-top&quot;&gt;
            &lt;div class=&quot;container&quot;&gt;
                &lt;div class=&quot;navbar-header&quot;&gt;
                    &lt;button type=&quot;button&quot; class=&quot;navbar-toggle&quot; data-toggle=&quot;collapse&quot; data-target=&quot;.navbar-collapse&quot;&gt;
                        &lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt;
                        &lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt;
                        &lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt;
                    &lt;/button&gt;
                    &lt;a class=&quot;navbar-brand&quot; runat=&quot;server&quot; <mark>id=&quot;ContactDemoLink&quot;</mark> href=&quot;~/<mark>Contacts/Default.aspx</mark>&quot;&gt;<mark>Contact Demo</mark>&lt;/a&gt;
                &lt;/div&gt;
                &lt;div class=&quot;navbar-collapse collapse&quot;&gt;
                    &lt;ul class=&quot;nav navbar-nav&quot;&gt;
                        &lt;li&gt;&lt;a runat=&quot;server&quot; href=&quot;~/&quot;&gt;Home&lt;/a&gt;&lt;/li&gt;
                        &lt;li&gt;&lt;a runat=&quot;server&quot; href=&quot;~/About&quot;&gt;About&lt;/a&gt;&lt;/li&gt;
                        &lt;li&gt;&lt;a runat=&quot;server&quot; href=&quot;~/Contact&quot;&gt;Contact&lt;/a&gt;&lt;/li&gt;
                    &lt;/ul&gt;
                    &lt;asp:LoginView runat=&quot;server&quot; ViewStateMode=&quot;Disabled&quot;&gt;
                        &lt;AnonymousTemplate&gt;
                            &lt;ul class=&quot;nav navbar-nav navbar-right&quot;&gt;
                                &lt;li&gt;&lt;a runat=&quot;server&quot; href=&quot;~/Account/Register&quot;&gt;Register&lt;/a&gt;&lt;/li&gt;
                                &lt;li&gt;&lt;a runat=&quot;server&quot; href=&quot;~/Account/Login&quot;&gt;Log in&lt;/a&gt;&lt;/li&gt;
                            &lt;/ul&gt;
                        &lt;/AnonymousTemplate&gt;
                        &lt;LoggedInTemplate&gt;
                            &lt;ul class=&quot;nav navbar-nav navbar-right&quot;&gt;
                                &lt;li&gt;&lt;a runat=&quot;server&quot; href=&quot;~/Account/Manage&quot; title=&quot;Manage your account&quot;&gt;Hello, &lt;%: Context.User.Identity.GetUserName()  %&gt; !&lt;/a&gt;&lt;/li&gt;
                                &lt;li&gt;
                                    &lt;asp:LoginStatus runat=&quot;server&quot; LogoutAction=&quot;Redirect&quot; LogoutText=&quot;Log off&quot; LogoutPageUrl=&quot;~/&quot; OnLoggingOut=&quot;Unnamed_LoggingOut&quot; /&gt;
                                &lt;/li&gt;
                            &lt;/ul&gt;
                        &lt;/LoggedInTemplate&gt;
                    &lt;/asp:LoginView&gt;
                &lt;/div&gt;
            &lt;/div&gt;
        &lt;/div&gt;
        &lt;div class=&quot;container body-content&quot;&gt;
            &lt;asp:ContentPlaceHolder ID=&quot;MainContent&quot; runat=&quot;server&quot;&gt;
            &lt;/asp:ContentPlaceHolder&gt;
            &lt;hr /&gt;
            &lt;footer&gt;
                &lt;p&gt;&amp;copy; &lt;%: DateTime.Now.Year %&gt; - <mark>Contact Manager</mark>&lt;/p&gt;
            &lt;/footer&gt;
        &lt;/div&gt;
    &lt;/form&gt;
&lt;/body&gt;
&lt;/html&gt;
</pre>  

Plus loin dans ce didacticiel, vous ajouterez la génération de modèles automatique Web Forms, qui créera la page à laquelle renvoie le lien " Contact Demo " ci-dessus.
###Exécution locale de l'application 
1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur la page  *Default.aspx* et sélectionnez **Définir comme page de démarrage**. 
2. Ensuite, appuyez sur **Ctrl+F5** pour exécuter l'application.  
	La page par défaut de l'application apparaît dans la fenêtre de navigateur par défaut.
	![Contacts - Create New Web Site](./media/web-sites-dotnet-web-forms-secure/SecureWebForms04.png)  

Voilà, vous avez fait tout ce qu'il fallait pour créer l'application que vous allez déployer dans Azure. Plus tard, vous allez ajouter une fonctionnalité de base de données, ainsi que les pages nécessaires pour afficher et modifier les données de contact.
###Déploiement de l'application vers Azure
Maintenant que vous avez créé et exécuté votre application localement, il est temps de la déployer vers Azure.

1. Dans Visual Studio, cliquez avec le bouton droit dans l'**Explorateur de solutions** et sélectionnez **Publier** dans le menu contextuel.  
	![Select Publish](./media/web-sites-dotnet-web-forms-secure/SecureWebForms05.png)  
	La boîte de dialogue **Publier le site Web** s'affiche.  

2. Sous l'onglet **Profil** de la boîte de dialogue **Publier le site Web**, cliquez sur **Sites Web Azure**.  
	![Publish Web dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms06.png)  
3. Si vous n'êtes pas connecté, cliquez sur le bouton **Se connecter** dans la boîte de dialogue **Sélectionner un site Web existant**. Une fois que vous vous êtes connecté, sélectionnez le site Web que vous avez créé dans la première partie de ce didacticiel. Cliquez sur **OK** pour continuer.  
	![Select Existing Web Site dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms07.png)  
Visual Studio télécharge vos paramètres de publication.
4. Dans la boîte de dialogue **Publier le site Web**, cliquez sur **Publier**.  
	![Publish Web dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms08.png)  
Vous verrez l'état global de publication dans la fenêtre **Activité de publication sur le Web** de Visual Studio :  
	![Web Publishing Activity](./media/web-sites-dotnet-web-forms-secure/SecureWebForms09.png)  

L'application créée est maintenant exécutée dans le cloud. Au prochain déploiement de l'application à partir de Visual Studio, seuls les fichiers modifiés (ou nouveaux) seront déployés.  
	![App in Browser](./media/web-sites-dotnet-web-forms-secure/SecureWebForms10.png)  

>[AZURE.NOTE] 
Si vous rencontrez une erreur lors de la publication vers un site Web déjà établi, vous pouvez effacer l'emplacement avant d'ajouter vos nouveaux fichiers.  
Republiez votre application, mais dans la boîte de dialogue **Publier le site Web**, sélectionnez l'onglet **Paramètres**. Ensuite, définissez la configuration sur **Débogage** et sélectionnez l'option **Supprimer les fichiers supplémentaires à la destination**. Sélectionnez **Publier** pour redéployer votre application.  
	![Publish Web dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms11.png)  

##Ajout d'une base de données à l'application 
﻿À présent, vous allez mettre à jour l'application Web Forms pour ajouter la possibilité d'afficher et de mettre à jour les contacts et de stocker les données dans la base de données par défaut. À la création du projet Web Forms, la base de données a également été créée par défaut. L'application va utiliser Entity Framework pour créer la base de données et pour lire et mettre à jour les données associées.
###Ajout d'une classe de modèle de données 
Commencez par créer un simple modèle de données avec du code. Ce modèle de données sera contenu dans une classe nommée  `Contacts`. Le nom de classe  `Contacts` a été choisi de manière à éviter un conflit de noms de classe avec la classe  `Contact` contenue dans le fichier Contact.aspx.cs créé par le modèle Web Forms.  

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier  *Models*, puis sélectionnez **Ajouter** -> **Classe**.  
	![Select Class](./media/web-sites-dotnet-web-forms-secure/SecureWebForms12.png)  
	La boîte de dialogue **Ajouter un nouvel élément** s'affiche.  

2. Nommez cette nouvelle classe  *Contacts.cs*.  
	![Add New Item dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms13.png)  
3. Remplacez le code par défaut par le code suivant :  
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
Vous avez créé la classe de modèle **Contacts** ci-dessus. Vous pouvez maintenant utiliser la génération de modèles automatique Web Forms pour générer les pages  *List*,  *Insert*,  *Edit* et  *Delete* utilisées lorsque vous traitez ces données. La génération de modèles automatique Web Forms utilise Entity Framework, Bootstrap et Dynamic Data. Par défaut, Web Forms Scaffolder est installé comme une extension de votre projet dans le cadre du modèle de projet lorsque vous utilisez Visual Studio 2013.

La procédure suivante vous permet d'utiliser Web Forms Scaffolder.

1. Dans Visual Studio, dans la barre de menus, sélectionnez **Outils** -> **Extensions et mises à jour**.  
	La boîte de dialogue **Extensions et mises à jour** s'affiche.
2. Dans le volet gauche de la boîte de dialogue, sélectionnez **En ligne** -> **Galerie Visual Studio** -> **Outils** -> **Génération de modèles automatique**.
3. Si  'Web Forms Scaffolding' n'apparaît pas dans liste, entrez  'Web Forms Scaffolding' dans la zone de recherche, en haut à droite de la boîte de dialogue.  
4. Si Web Forms Scaffolder n'est pas encore installé, sélectionnez **Télécharger** pour le télécharger et l'installer. Redémarrez Visual Studio si besoin est. Veillez à enregistrer les modifications apportées au projet quand vous y êtes invité.  
	![Extensions and Updates dialog box](./media/web-sites-dotnet-web-forms-secure/ExtensionsAndUpdatesDB.png)  
5. Générez le projet (**Ctrl+Maj+B**).  
	Vous devez générer le projet avant d'utiliser le mécanisme de génération de modèle automatique.  
6. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le  *project*, puis sélectionnez **Ajouter** -> **Nouvel élément structuré**.  
	La boîte de dialogue **Ajouter un élément structuré** s'affiche.
7. Sélectionnez **Web Forms** dans le volet de gauche et **Pages Web Forms utilisant Entity Framework** dans le volet central. Cliquez ensuite sur **Ajouter**.  
	![Add Scaffold dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms13a.png)  
	La boîte de dialogue **Ajouter des pages Web Forms** s'affiche.  

8. Dans la boîte de dialogue **Ajouter des pages Web Forms**, définissez la **Classe de modèle** sur  `Contact (ContactManager.Models)`. Définissez la **Classe de contexte de données** à  `ApplicationDbContext (ContactManager.Models)`. Cliquez ensuite sur **Ajouter**. 
	![Add Web Forms Pages dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms13b.png)  

Web Forms Scaffolder ajoute un nouveau dossier contenant les pages  *Default.aspx*,  *Delete.aspx*,  *Edit.aspx* et  *Insert.aspx*. Il crée également un dossier  *DynamicData* contenant un dossier  *EntityTemplates* et un dossier  *FieldTemplates*.  `ApplicationDbContext` sera utilisé à la fois pour la base de données d'appartenance et les données de contact.

###Configuration de l'application pour utiliser le modèle de données 
L'étape suivante consiste à activer la fonctionnalité Migrations Code First pour créer la base de données en fonction du modèle de données que vous avez créé. Vous ajouterez également un exemple de données et un initialiseur de données.  

1. Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** puis **Console du Gestionnaire de package**.  
	![Add Web Forms Pages dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms13c.png)  
2. Dans la fenêtre Console du Gestionnaire de package, entrez la commande suivante :  
	<pre class="prettyprint">
	enable-migrations
	</pre>  
	La commande enable-migrations crée un dossier  *Migrations* dans lequel elle place un fichier  *Configuration.cs* que vous pouvez modifier pour amorcer la base de données et configurer les migrations de données.  
3. Dans la fenêtre **Console du Gestionnaire de package**, entrez la commande suivante :  
	<pre class="prettyprint">
	add-migration Initial
	</pre>  
	La commande  `add-migration Initial` génère un fichier nommé <date_stamp>Initial dans le dossier  *Migrations* qui crée la base de données. Le premier paramètre (Initial) est arbitraire et permet de créer le nom du fichier. Les nouveaux fichiers de classe sont affichés dans l'**Explorateur de solutions**. Dans la classe  `Initial`, la méthode  `Up` crée la table  `Contact` et la méthode  `Down` (utilisée lorsque vous voulez revenir à l'état précédent) annule cette table.  
4. Ouvrez le fichier  *Migrations\Configuration.cs*. 
5. Ajoutez l'espace de noms suivant :  
	<pre class="prettyprint">
	using ContactManager.Models;
	</pre>  
6. Remplacez la méthode  `Seed` par le code suivant :  
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
Ce code initialise (amorce) la base de données avec les informations de contact. Pour plus d'informations sur l'amorçage de la base de données, consultez la page [Amorçage et débogage des bases de données Entity Framework (EF)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).  
7. Dans la **Console du Gestionnaire de package**, entrez la commande suivante :  
	<pre class="prettyprint">
	update-database
	</pre>  
La commande  `update-database` exécute la première migration qui crée la base de données. Par défaut, la base de données est créée en tant que base de données SQL Server Express LocalDB.  
	![Package Manager Console](./media/web-sites-dotnet-web-forms-secure/SecureWebForms13d.png)  

###Exécution locale de l'application et affichage des données 
Exécutez à présent l'application pour voir comment s'affichent les contacts.

1. Commencez par créer le projet (**Ctrl+Maj+B**).  
2. Appuyez sur **Ctrl+F5** pour exécuter l'application.  
	Le navigateur s'ouvre et affiche la page  *Default.aspx*.
3. Sélectionnez le lien **Contact Demo** en haut de la page pour afficher la page  *Contact List*.  
	![Contacts List Page](./media/web-sites-dotnet-web-forms-secure/SecureWebForms17.png)  

##Activation du protocole SSL pour le projet 
SSL (Secure Sockets Layer) est un protocole défini pour autoriser les serveurs et clients Web à communiquer de manière plus sécurisée grâce au chiffrement. Lorsque SSL n'est pas utilisé, les données envoyées entre le client et le serveur peuvent être soumises à la détection des paquets par quiconque dispose d'un accès physique au réseau. En outre, plusieurs schémas d'authentification ne sont pas sécurisés sur le HTTP standard. En particulier, l'authentification de base et l'authentification par formulaires envoient des informations d'identification non chiffrées. Pour être sécurisés, ces schémas d'authentification doivent utiliser SSL.

1. Dans l'**Explorateur de solutions**, cliquez sur le projet **ContactManager**, puis appuyez sur **F4** pour afficher la fenêtre **Propriétés**. 
2. Définissez **SSL activé** sur  `true`. 
3. Copiez l'**URL SSL** pour pouvoir l'utiliser ultérieurement.  
	L'URL SSL est `https://localhost:44300/` , sauf si vous avez créé des sites Web SSL auparavant (comme indiqué ci-dessous).
	![Project Properties](./media/web-sites-dotnet-web-forms-secure/SecureWebForms18.png)  
4. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **Contact Manager**, puis sur **Propriétés**.
5. Sous l'onglet de gauche, cliquez sur **Web**.
6. Modifiez l'**URL du projet** pour utiliser l'**URL SSL** que vous avez enregistrée précédemment.  
	![Project Web Properties](./media/web-sites-dotnet-web-forms-secure/SecureWebForms19.png)  
7. Enregistrez la page en appuyant sur **Ctrl+S**.
8. Appuyez sur **Ctrl+F5** pour exécuter l'application.  
Visual Studio affiche une option qui vous permet d'éviter l'affichage des avertissements SSL.  
9. Cliquez sur **Oui** pour approuver le certificat SSL IIS Express et continuer.  
	![IIS Express SSL certificate details](./media/web-sites-dotnet-web-forms-secure/SecureWebForms20.png)  
	Un avertissement de sécurité s'affiche.  

10. Cliquez sur **Oui** pour installer le certificat sur votre localhost.  
	![Security Warning dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21.png)  
	La fenêtre du navigateur s'affiche.  

Vous pouvez tester votre application Web localement sans difficulté avec SSL.



##Ajout d'un fournisseur OAuth 2.0 
ASP.NET Web Forms propose des options améliorées pour l'appartenance et l'authentification. Ces améliorations incluent OAuth, OAuth est un protocole ouvert permettant de mettre en place une authentification sécurisée de manière simple et standardisée à partir d'applications Web, mobiles ou de bureau. Le modèle Internet d'ASP.NET MVC utilise OAuth pour exposer Facebook, Twitter, Google et Microsoft comme fournisseurs d'authentification. Même si ce didacticiel utilise uniquement Google comme fournisseur d'authentification, vous pouvez facilement modifier le code pour utiliser n'importe quel autre fournisseur. La procédure à suivre pour implémenter d'autres fournisseurs ressemble assez à celle présentée dans ce didacticiel. 

En plus de l'authentification, ce didacticiel va également utiliser des rôles pour l'implémentation d'autorisations. Seuls les utilisateurs que vous ajoutez au rôle  `canEdit` pourront créer, modifier ou supprimer des contacts. 

Les étapes suivantes vous permettent d'ajouter un fournisseur d'authentification Google.

1. Ouvrez le fichier  *App_Start\Startup.Auth.cs*. 
2. Supprimez les caractères de commentaire de la méthode  `app.UseGoogleAuthentication()` pour qu'elle ait l'aspect suivant :  
	<pre class="prettyprint">
	            app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions()
	            {
	                ClientId = "",
	                ClientSecret = ""
	            });
	</pre>
3. Accédez à la [Console développeur de Google](https://console.developers.google.com/). Vous devez également vous connecter avec votre compte de messagerie de développeur Google (gmail.com). Si vous n'avez pas de compte Google, sélectionnez le lien **Créer un compte**.  
	Ensuite, la **Console développeur de Google** s'affiche.
	![Google Developers Console](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21a.png)  

4. Cliquez sur le bouton **Créer un projet** et entrez un nom et un ID de projet (vous pouvez utiliser les valeurs par défaut). Ensuite, cochez la case **d'accord du contrat** et cliquez sur le bouton **Créer**.  
	![Google - New Project](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21b.png)  
	En quelques secondes, le nouveau projet est créé et votre navigateur affiche la nouvelle page de projets.
5. Sous l'onglet de gauche, cliquez sur **API et authentification**, puis cliquez sur **Identifiants**.
6. Cliquez sur **Créer un identifiant client** sous **OAuth**.  
	La boîte de dialogue **Créer un identifiant client** s'affiche.
	![Google - Create Client ID](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21c.png)  
7. Dans la boîte de dialogue **Créer un identifiant client**, conservez le type d'application par défaut **Application Web**.  
8. Définissez les **Origines JavaScript autorisées** sur l'URL SSL que vous avez utilisée précédemment dans ce didacticiel (**https://localhost:44300/** , à moins que vous n'ayez créé d'autres projets SSL).  
	Cette URL est l'origine de votre application. Pour cet exemple, vous entrerez uniquement l'URL de test de l'hôte local (localhost). Vous pouvez cependant entrer plusieurs URL pour l'hôte local (localhost) et la production.  

9. Définissez **URI de redirection autorisés ** comme suit :  
	<pre class="prettyprint">  
	https://localhost:44300/signin-google  
	</pre>  
	Cette valeur est l'URI que le protocole OAuth ASP.NET utilise pour communiquer avec le serveur OAuth Google. Souvenez-vous de l'URL SSL que vous avez utilisée précédemment (**https://localhost:44300/** , sauf si vous avez créé d'autres projets SSL).  
10. Cliquez sur le bouton **Créer un identifiant client**.
11. Dans Visual Studio, mettez à jour la méthode  `UseGoogleAuthentication` de la page  *Startup.Auth.cs* en copiant et collant les valeurs **AppId** et **App Secret** dans la méthode. Les valeurs **AppId** et **App Secret** indiquées ci-après sont de simples exemples et ne fonctionneront pas.  
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
	
	        // For more information on configuring authentication, please visit http://go.microsoft.com/fwlink/?LinkId=301883
	        public void ConfigureAuth(IAppBuilder app)
	        {
	            // Configure the db context and user manager to use a single instance per request
	            app.CreatePerOwinContext(ApplicationDbContext.Create);
	            app.CreatePerOwinContext<ApplicationUserManager>(ApplicationUserManager.Create);
	
	            // Enable the application to use a cookie to store information for the signed in user
	            // and to use a cookie to temporarily store information about a user logging in with a third party login provider
	            // Configure the sign in cookie
	            app.UseCookieAuthentication(new CookieAuthenticationOptions
	            {
	                AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
	                LoginPath = new PathString("/Account/Login"),
	                Provider = new CookieAuthenticationProvider
	                {
	                    OnValidateIdentity = SecurityStampValidator.OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
	                        validateInterval: TimeSpan.FromMinutes(20),
	                        regenerateIdentity: (manager, user) => user.GenerateUserIdentityAsync(manager))
	                }
	            });
	            // Use a cookie to temporarily store information about a user logging in with a third party login provider
	            app.UseExternalSignInCookie(DefaultAuthenticationTypes.ExternalCookie);
	
	            // Uncomment the following lines to enable logging in with third party login providers
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
12. Appuyez sur **Ctrl+F5** pour générer et exécuter l'application. Cliquez sur le lien **Ouvrir une session**.
13. Sous **Utiliser un autre service pour ouvrir une session**, cliquez sur **Google**.  
	![Log in](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21d.png)  
14. Si vous devez entrer vos informations d'identification, vous serez redirigé vers le site Google approprié.  
	![Google - Sign in](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21e.png)  
15. Après avoir entré vos informations d'identification, vous êtes invité à accorder des autorisations pour l'application Web que vous venez de créer :  
	![Project Default Service Account](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21f.png)  
16. Cliquez sur **Accept**. Vous allez maintenant être redirigé vers la page **Register** de l'application **ContactManager**, où vous pouvez inscrire votre compte Google.
	![Register with your Google Account](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21g.png)  
17. Vous avez la possibilité de changer le nom d'inscription local utilisé pour votre compte Gmail, mais, en règle générale, les utilisateurs préfèrent conserver l'alias de messagerie par défaut (c'est-à-dire celui que vous avez utilisé pour l'authentification). Cliquez sur **Ouvrir une session**.

##Utilisation de l'API d'appartenance pour limiter l'accès 
ASP.NET Identity est le système d'appartenance utilisé pour l'authentification lors de la création d'une application Web ASP.NET. Il facilite l'intégration des données de profil propres à l'utilisateur aux données de l'application. ASP.NET Identity vous permet également de choisir le modèle de persistance pour les profils utilisateur dans votre application. Vous pouvez stocker les données dans une base de données SQL Server ou un autre magasin de données, y compris dans des magasins de données  *NoSQL* tels que des tables Azure Storage.

En utilisant le modèle ASP.NET Web Forms par défaut, vous disposez d'une fonctionnalité d'appartenance intégrée dont vous pouvez vous servir immédiatement lorsque l'application s'exécute. Vous allez utiliser ASP.NET Identity pour ajouter un rôle d'administrateur et affecter un utilisateur à ce rôle. Ensuite, vous apprendrez à limiter l'accès au dossier d'administration et aux pages de ce dossier qui sont utilisées pour modifier les données de contact.

###Ajout d'un administrateur 
ASP.NET Identity vous permet d'ajouter un rôle d'administrateur et d'affecter un utilisateur à ce rôle avec du code. 

1. Dans l'**Explorateur de solutions**, ouvrez le fichier  *Configuration.cs* dans le dossier  *Migrations*.
2. Ajoutez les instructions  `using` suivantes dans l'espace de noms  `ContactManger.Migrations` :  
	<pre class="prettyprint">
	using Microsoft.AspNet.Identity;
	using Microsoft.AspNet.Identity.EntityFramework;
	</pre>
3. Ajoutez la méthode  `AddUserAndRole` suivante dans la classe  `Configuration` après la méthode `Seed` :  
	<pre class="prettyprint">
        public void AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
        {
            IdentityResult IdRoleResult;
            IdentityResult IdUserResult;

            var roleStore = new RoleStore&lt;IdentityRole&gt;(context);
            var roleMgr = new RoleManager&lt;IdentityRole&gt;(roleStore);

            if (!roleMgr.RoleExists(&quot;canEdit&quot;))
            {
                IdRoleResult = roleMgr.Create(new IdentityRole { Name = &quot;canEdit&quot; });
            }

            //var userStore = new UserStore&lt;ApplicationUser&gt;(context);
            //var userMgr = new UserManager&lt;ApplicationUser&gt;(userStore);
            var userMgr = new UserManager&lt;ApplicationUser&gt;(new UserStore&lt;ApplicationUser&gt;(context));

            var appUser = new ApplicationUser
            {
                UserName = &quot;canEditUser@wideworldimporters.com&quot;,
                Email = &quot;canEditUser@wideworldimporters.com&quot;
            };
            IdUserResult = userMgr.Create(appUser, &quot;Pa$$word1&quot;);

            if (!userMgr.IsInRole(userMgr.FindByEmail(&quot;canEditUser@wideworldimporters.com&quot;).Id, &quot;canEdit&quot;))
            {
              //  IdUserResult = userMgr.AddToRole(appUser.Id, &quot;canEdit&quot;);
                IdUserResult = userMgr.AddToRole(userMgr.FindByEmail(&quot;canEditUser@wideworldimporters.com&quot;).Id, &quot;canEdit&quot;);
            }
        }
	</pre>
4. Ajoutez un appel à la méthode  `AddUserAndRole` au début de la méthode  `Seed`. Notez que seul le début de la méthode  `Seed` est affiché.  
	<pre class="prettyprint">
        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            <mark>AddUserAndRole(context);</mark>
	</pre>  
5. Après avoir enregistré vos modifications, dans la **Console du Gestionnaire de package**, exécutez la commande suivante :  
	<pre class="prettyprint">
	Update-Database
	</pre>
	Ce code crée un rôle nommé  `canEdit` et un utilisateur local avec l'adresse de messagerie canEditUser@wideworldimporters.com. Ensuite, le code ajoute canEditUser@wideworldimporters.com au rôle  `canEdit`. Pour plus d'informations, consultez la page de ressources d'[identité ASP.NET](http://www.asp.net/identity).  

###Limitation de l'accès au dossier d'administration 
L'exemple d'application **ContactManager** permet aux utilisateurs anonymes et aux utilisateurs connectés d'afficher les contacts. Une fois cette section terminée, cependant, les utilisateurs connectés qui sont affectés au rôle " canEdit " seront les seuls utilisateurs en mesure de modifier les contacts.

Vous allez créer un dossier nommé  *Admin* auquel seuls les utilisateurs affectés au rôle " canEdit " pourront accéder.

1. Dans l'**Explorateur de solutions**, ajoutez un sous-dossier au dossier  *Contacts* et nommez-le  *Admin*.
2. Déplacez les fichiers suivants du dossier  *Contacts* vers le dossier  *Contacts/Admin* :  
	- *Delete.aspx * et * Delete.aspx.cs*
	- *Edit.aspx * et * Edit.aspx.cs*
	- *Insert.aspx * et * Insert.aspx.cs*
3. Mettez à jour les références de lien dans  *Contacts/Default.aspx* en ajoutant " Admin/ " devant les références de pages qui renvoient à  *Insert.aspx*,  *Edit.aspx* et  *Delete.aspx* :  
	<pre class="prettyprint">
	&lt;%@ Page Title=&quot;ContactsList&quot; Language=&quot;C#&quot; MasterPageFile=&quot;~/Site.Master&quot; CodeBehind=&quot;Default.aspx.cs&quot; Inherits=&quot;ContactManager.Contacts.Default&quot; ViewStateMode=&quot;Disabled&quot; %&gt;
	&lt;%@ Register TagPrefix=&quot;FriendlyUrls&quot; Namespace=&quot;Microsoft.AspNet.FriendlyUrls&quot; %&gt;
	
	&lt;asp:Content runat=&quot;server&quot; ContentPlaceHolderID=&quot;MainContent&quot;&gt;
	    &lt;h2&gt;Contacts List&lt;/h2&gt;
	    &lt;p&gt;
	        &lt;asp:HyperLink runat=&quot;server&quot; NavigateUrl=&quot;<mark>Admin/</mark>Insert.aspx&quot; Text=&quot;Create new&quot; /&gt;
	    &lt;/p&gt;
	    &lt;div&gt;
	        &lt;asp:ListView runat=&quot;server&quot;
	            DataKeyNames=&quot;ContactId&quot; ItemType=&quot;ContactManager.Models.Contacts&quot;
	            AutoGenerateColumns=&quot;false&quot;
	            AllowPaging=&quot;true&quot; AllowSorting=&quot;true&quot;
	            SelectMethod=&quot;GetData&quot;&gt;
	            &lt;EmptyDataTemplate&gt;
	                There are no entries found for Contacts
	            &lt;/EmptyDataTemplate&gt;
	            &lt;LayoutTemplate&gt;
	                &lt;table class=&quot;table&quot;&gt;
	                    &lt;thead&gt;
	                        &lt;tr&gt;
	                            &lt;th&gt;Name&lt;/th&gt;
	                            &lt;th&gt;Address&lt;/th&gt;
	                            &lt;th&gt;City&lt;/th&gt;
	                            &lt;th&gt;State&lt;/th&gt;
	                            &lt;th&gt;Zip&lt;/th&gt;
	                            &lt;th&gt;Email&lt;/th&gt;
	                            &lt;th&gt;&amp;nbsp;&lt;/th&gt;
	                        &lt;/tr&gt;
	                    &lt;/thead&gt;
	                    &lt;tbody&gt;
	                        &lt;tr runat=&quot;server&quot; id=&quot;itemPlaceholder&quot; /&gt;
	                    &lt;/tbody&gt;
	                &lt;/table&gt;
	            &lt;/LayoutTemplate&gt;
	            &lt;ItemTemplate&gt;
	                &lt;tr&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Name&quot; ID=&quot;Name&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Address&quot; ID=&quot;Address&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;City&quot; ID=&quot;City&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;State&quot; ID=&quot;State&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Zip&quot; ID=&quot;Zip&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Email&quot; ID=&quot;Email&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;a href=&quot;<mark>Admin/</mark>Edit.aspx?ContactId=&lt;%#: Item.ContactId%&gt;&quot;&gt;Edit&lt;/a&gt; | 
	                        &lt;a href=&quot;<mark>Admin/</mark>Delete.aspx?ContactId=&lt;%#: Item.ContactId%&gt;&quot;&gt;Delete&lt;/a&gt;
	                    &lt;/td&gt;
	                &lt;/tr&gt;
	            &lt;/ItemTemplate&gt;
	        &lt;/asp:ListView&gt;
	    &lt;/div&gt;
	&lt;/asp:Content&gt;
	</pre>
4. Mettez à jour les six références du code  `Response.Redirect("Default.aspx")` sur  `Response.Redirect("~/Contacts/Default.aspx")` pour les trois fichiers suivants :  
	- *Delete.aspx.cs*
	- *Edit.aspx.cs*
	- *Insert.aspx.cs*  

	Dorénavant, les liens fonctionneront correctement lorsque vous afficherez et mettrez à jour les données de contact.
5. Pour limiter l'accès au dossier  *Admin*, dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier  *Admin* et sélectionnez **Ajouter un nouvel élément**.
6. Dans la liste des modèles Web Visual C#, sélectionnez **Fichier de configuration Web** dans la liste du milieu, acceptez le nom par défaut  *Web.config*, puis sélectionnez **Ajouter**.
7. Remplacez le contenu XML existant dans le fichier  *Web.config* par le code suivant :
	<pre class="prettyprint">
	&lt;?xml version=&quot;1.0&quot;?&gt;
	&lt;configuration&gt;
	  &lt;system.web&gt;
	    &lt;authorization&gt;
	      &lt;allow roles=&quot;canEdit&quot;/&gt;
	      &lt;deny users=&quot;*&quot;/&gt;
	    &lt;/authorization&gt;
	  &lt;/system.web&gt;
	&lt;/configuration&gt;
	</pre>
8. Enregistrez le fichier  *Web.config*. 
	Le fichier  *Web.config* spécifie que seuls les utilisateurs affectés au rôle " canEdit " ont accès aux pages contenues dans le dossier  *Admin*. 

Quand un utilisateur qui n'est pas affecté au rôle " canEdit " essaie de modifier les données, il est redirigé vers la page  *Log in*.

##Déploiement de l'application avec la base de données vers Azure 
Maintenant que l'application Web est terminée, vous pouvez la publier sur Azure.

###Publication de l'application 
1. Dans Visual Studio, générez le projet (**Ctrl+Maj+B**).
2. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.  
	![Publish menu option](./media/web-sites-dotnet-web-forms-secure/SecureWebForms22.png)  
	La boîte de dialogue **Publier le site Web** s'affiche.  
	![Publish Web dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms22a.png)  
3. Sous l'onglet **Profil**, sélectionnez **Sites Web Azure** comme cible de publication si cette option n'est pas sélectionnée.  
	![Publish Web dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms23.png)  
4. Cliquez sur **Se connecter** si vous n'êtes pas encore connecté.
5. Sélectionnez le site Web existant que vous avez créé précédemment dans ce didacticiel dans la liste déroulante **Sites Web existants**, puis cliquez sur le bouton **OK**.  
	![Select Existing Web Site dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms25.png)  
	Si vous êtes invité à enregistrer les modifications apportées au profil, sélectionnez **Oui**.
6. Cliquez sur l'onglet **Paramètres**.  
	![Select Existing Web Site dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms26.png)  
7. Dans la zone de liste déroulante **Configuration**, sélectionnez **Débogage**.
8. Cliquez sur la **flèche vers le bas** en regard de la zone **ApplicationDbContext** et sélectionnez **ContactDB**.
9. Cochez la case **Exécuter les migrations Code First**.  

	>[AZURE.NOTE]  
	Dans cet exemple, vous devez cocher cette case uniquement lors de la première publication de l'application. Ainsi, la méthode  *Seed* dans le fichier  *Configuration.cs* ne sera appelée qu'une seule fois.  

10. Ensuite, cliquez sur **Publier**.  
	Votre application sera publiée dans Azure.

>[AZURE.NOTE]  
Si vous avez fermé puis rouvert Visual Studio après avoir créé le profil de publication, il est possible que la chaîne de connexion ne figure pas dans la liste déroulante. Dans ce cas, au lieu de modifier le profil de publication créé précédemment, créez-en un autre comme auparavant, puis suivez la procédure sous l'onglet **Paramètres**.  

###Révision de l'application dans Azure 
1. Dans le navigateur, cliquez sur le lien **Contact Demo**.  
	La liste Contacts s'affiche.
	![Contacts listed in Browser](./media/web-sites-dotnet-web-forms-secure/SecureWebForms27.png)  

2. Sélectionnez **Create new** dans la page **Contacts List**.  
	![Contacts listed in Browser](./media/web-sites-dotnet-web-forms-secure/SecureWebForms29.png)  
	Vous êtes redirigé vers la page **Login** car vous ne vous êtes pas connecté avec un compte qui peut modifier des contacts.  
3. Après avoir entré l'adresse de messagerie et le mot de passe ci-dessous, cliquez sur le bouton **Log in** :  
	**Email** : `canEditUser@wideworldimporters.com` 
	**Password** : `Pa$$word1`  
	![Log in Page](./media/web-sites-dotnet-web-forms-secure/SecureWebForms28.png)  

4. Entrez de nouvelles données pour chaque champ, puis appuyez sur le bouton **Insert**.  
	![Add New Contact Page](./media/web-sites-dotnet-web-forms-secure/SecureWebForms30.png)  
	La page  *EditContactList.aspx* s'affiche avec le nouvel enregistrement.  
	![Add New Contact Page](./media/web-sites-dotnet-web-forms-secure/SecureWebForms31.png)  

5. Sélectionnez le lien **Log off**.

###Arrêt de l'application 
Pour empêcher les autres utilisateurs de s'inscrire et d'utiliser votre exemple d'application, vous allez arrêter le site Web.

1. Dans le menu **Affichage** de Visual Studio, cliquez sur **Explorateur de serveurs**. 
2. Dans l'**Explorateur de serveurs**, accédez à **Sites Web**.
3. Cliquez avec le bouton droit sur chaque instance de site Web, puis sélectionnez **Arrêter le site Web**.  
	![Stop Web Site menu item](./media/web-sites-dotnet-web-forms-secure/SecureWebForms26a.png)  

	En guise d'alternative, dans le portail de gestion Microsoft Azure, vous pouvez sélectionner le site Web, puis cliquer sur l'icône **Arrêter** en bas de la page.  
	![Add New Contact Page](./media/web-sites-dotnet-web-forms-secure/SecureWebForms26b.png)  

##Révision de la base de données 
Il est important de savoir comment afficher et modifier directement la base de données. Le fait de savoir travailler directement avec la base de données vous permet de valider les données qu'elle contient et de comprendre comment les données sont stockées dans chaque table.

###Examen de la base de données SQL Azure 
1. Dans Visual Studio, ouvrez l'**Explorateur de serveurs** et accédez à **ContactDB**.
2. Cliquez avec le bouton droit sur **ContactDB**, puis sélectionnez **Ouvrir dans l'Explorateur d'objets SQL Server**.  
	![Open in SQL Server Object Explorer menu item](./media/web-sites-dotnet-web-forms-secure/SecureWebForms32.png)  
3. Si la boîte de dialogue **Ajouter une règle de pare-feu** s'affiche, sélectionnez **Ajouter une règle de pare-feu**.  

	>[AZURE.NOTE]  
	Si vous ne pouvez pas développer **Bases de données SQL** et afficher la base de données **ContactDB** dans Visual Studio, suivez les instructions pour ouvrir un ou plusieurs ports de pare-feu. À cet effet, suivez les instructions de la rubrique **Configuration des règles de pare-feu Azure** vers la fin du [didacticiel sur MVC](http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/). Une autre méthode consiste à examiner les données de la base de données locale en créant, exécutant et ajoutant des données à l'application localement (**Ctrl+F5** dans Visual Studio).  

4. Si la boîte de dialogue **Se connecter au serveur** s'affiche, entrez le **mot de passe** que vous avez créé au début de ce didacticiel et appuyez sur le bouton **Se connecter**.  

	>[AZURE.NOTE]  
	Si vous avez oublié le mot de passe, vous pouvez le retrouver dans votre fichier de projet local. Dans l'**Explorateur de solutions**, développez le dossier  *Properties*, puis le dossier  *PublishProfiles*. Ouvrez le fichier  *contactmanager.pubxml* (votre fichier peut avoir un nom différent). Recherchez votre mot de passe de publication dans le fichier.
5. Développez la base de données **contactDB**, puis développez **Tables**.
6. Cliquez avec le bouton droit sur la table **dbo.AspNetUsers** et sélectionnez **Afficher les données**.  
	![View Data menu item](./media/web-sites-dotnet-web-forms-secure/SecureWebForms34.png)  
	Vous pouvez afficher les données associées à l'utilisateur canEditUser@contoso.com.  
	![ContactManager window](./media/web-sites-dotnet-web-forms-secure/SecureWebForms35.png)  

###Ajout d'un utilisateur au rôle admin en modifiant la base de données 
Précédemment dans ce didacticiel, vous avez utilisé du code pour ajouter des utilisateurs au rôle canEdit. Il existe une autre méthode consistant à manipuler directement les données des tables d'appartenance. La procédure suivante indique comment utiliser cette méthode pour ajouter un utilisateur à un rôle.

1. Dans l'**Explorateur d'objets SQL Server**, cliquez avec le bouton droit sur **dbo.AspNetUserRoles**, puis sélectionnez **Afficher les données**.
	![AspNetUserRoles data](./media/web-sites-dotnet-web-forms-secure/SecureWebForms36.png)  
2. Copiez la valeur  *RoleId* et collez-la dans la (nouvelle) ligne vide.  
	![AspNetUserRoles data](./media/web-sites-dotnet-web-forms-secure/SecureWebForms37.png)  
3. Dans la table **dbo.AspNetUsers**, recherchez l'utilisateur que vous voulez affecter au rôle et copiez l' *Id* associé.
4. Collez l' *Id* copié dans le champ **UserId** du nouveau rôle dans la table **AspNetUserRoles**.  

>[AZURE.NOTE]  
Nous développons actuellement un outil qui facilitera la gestion des utilisateurs et des rôles.  

##Étapes suivantes
Pour plus d'informations sur ASP.NET Web Forms, consultez la page [Présentation d'ASP.NET Web Forms](http://www.asp.net/web-forms) sur le site Web ASP.NET et la page [Didacticiels et guides de Microsoft Azure](http://azure.microsoft.com/fr-fr/documentation/services/web-sites/#net).

Ce didacticiel a été réalisé à partir du didacticiel MVC [Déploiement d'une application ASP.NET MVC 5 sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site Web Azure](http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/) rédigé par Rick Anderson (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)), assisté de Tom Dykstra et Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)). 

N'hésitez pas à nous transmettre vos commentaires sur ce qui vous a plu et ce qui pourrait être amélioré... pas seulement à propos de ce didacticiel, mais aussi en ce qui concerne les produits présentés ici. Vos commentaires nous aideront à orienter nos améliorations. Vous pouvez aussi demander de nouvelles rubriques et noter les rubriques existantes sur [Leçons de code](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).




<!--HONumber=42-->
