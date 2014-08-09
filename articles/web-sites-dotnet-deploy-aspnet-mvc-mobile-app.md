<properties linkid="develop-dotnet-aspnet-mvc-4-mobile-website" urlDisplayName="ASP.NET MVC 4 mobile website" pageTitle=".NET ASP.NET MVC 4 mobile web site - Azure tutorials" metaKeywords="Azure tutorial, Azure web app tutorial, Azure mobile app, Azure ASP.NET MVC 4,,ASP.NET MVC" description="A tutorial that teaches you how to deploy a web application to an Azure web site using mobile features in ASP.NET MVC 4 web application." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Deploy an ASP.NET MVC Mobile Web Application on Azure Web Sites" authors="tdykstra" solutions="" manager="" editor="" />

Déploiement d'une application Web mobile ASP.NET MVC dans des sites Web Azure
=============================================================================

***Par [Rick Anderson](https://twitter.com/RickAndMSFT) Mis à jour le 26 juin 2013.***

Ce didacticiel vous montre comment déployer une application Web dans un site Web Azure. Pour les besoins de ce didacticiel, nous allons utiliser des fonctionnalités mobiles dans une application Web ASP.NET MVC 4. Pour effectuer les étapes de ce didacticiel, vous pouvez utiliser Microsoft Visual Studio 2012. Vous avez également la possibilité d'utiliser [Visual Studio Express 2012](http://www.microsoft.com/visualstudio/eng/products/visual-studio-express-products), qui est une version gratuite de Microsoft Visual Studio.

Vous apprendrez :
-----------------

-   comment les modèles ASP.NET MVC 4 utilisent l'attribut de fenêtre d'affichage HTML5 et le rendu adaptatif pour améliorer l'affichage sur les appareils mobiles ;
-   à créer des affichages mobiles ;
-   à créer un sélecteur de vues qui permet aux utilisateurs de passer d'un affichage mobile à une vue bureau de l'application et inversement ;
-   à déployer l'application Web sur Azure.

Pour ce didacticiel, vous devrez ajouter des fonctionnalités mobiles à l'application simple de listes de conférence fournie dans le projet de départ. La capture d'écran suivante représente la page principale d'une application terminée telle qu'elle s'affiche dans l'émulateur Windows Phone 7.

![Page principale de l'application de conférence MVC4](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/FinishedAPPMainScreen.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Configuration de l'environnement de développement
-------------------------------------------------

Configurez l'environnement de développement en installant le Kit de développement logiciel (SDK) Azure pour .NET Framework.

1.  Pour installer le Kit de développement logiciel (SDK) Azure pour .NET, cliquez sur le lien ci-dessous. Si Visual Studio 2012 n'est pas encore installé, il le sera par l'intermédiaire de ce lien. Ce didacticiel requiert Visual Studio 2012. [Kit de développement logiciel (SDK) Azure pour Visual Studio 2012](http://go.microsoft.com/fwlink/?LinkId=254364)
2.  Lorsque vous êtes invité à exécuter ou enregistrer l’exécutable d’installation, cliquez sur **Exécuter**.
3.  Dans la fenêtre Web Platform Installer, cliquez sur **Installer**, puis poursuivez l’installation.

![Web Platform Installer - Kit de développement logiciel (SDK) Azure pour .NET](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk20NetVS12.png)

Vous aurez également besoin d'un émulateur de navigateur mobile. Vous pouvez utiliser l'un des émulateurs suivants :

-   [Émulateur Windows Phone 7](http://msdn.microsoft.com/fr-fr/library/ff402530(VS.92).aspx) (émulateur utilisé dans la plupart des captures d'écran de ce didacticiel).
-   Modifiez la chaîne de l'agent utilisateur pour émuler un iPhone. Consultez [ce billet de blog](http://www.howtogeek.com/113439/how-to-change-your-browsers-user-agent-without-installing-any-extensions/) sur How-To Geek.
-   [Émulateur mobile Opera](http://www.opera.com/developer/tools/mobile/).
-   [Apple Safari](http://www.apple.com/safari/download/) avec l'agent utilisateur défini sur iPhone. Pour obtenir les instructions de définition de l'agent utilisateur sur « iPhone » dans Safari, consultez l'article [Comment permettre à Safari de se faire passer pour IE](http://www.davidalison.com/2008/05/how-to-let-safari-pretend-its-ie.html) sur le blog de David Alison.
-   [FireFox](http://www.bing.com/search?q=firefox+download) avec le [sélecteur d'agents utilisateur FireFox](https://addons.mozilla.org/en-US/firefox/addon/user-agent-switcher/).

Ce didacticiel affiche les codes en C\#. Toutefois, le projet de départ et le projet terminé sont disponibles en Visual Basic. Les projets Visual Studio avec code source Visual Basic et C\# sont disponibles pour cette rubrique :

-   [Téléchargement du projet de départ](http://go.microsoft.com/fwlink/?LinkId=228307)
-   [Téléchargement du projet terminé](http://go.microsoft.com/fwlink/?LinkId=228306)

Étapes de ce didacticiel
------------------------

-   [Création d'un site Web Azure](#bkmk_CreateWebSite)
-   [Configuration du projet de départ](#bkmk_setupstarterproject)
-   [Remplacement des vues, des dispositions et des vues partielles](#bkmk_overrideviews)
-   [Utilisation de jQuery Mobile pour définir l'interface du navigateur mobile](#bkmk_usejquerymobile)
-   [Amélioration de la liste Intervenants](#bkmk_Improvespeakerslis)
-   [Création d'un affichage mobile des intervenants](#bkmk_mobilespeakersview)
-   [Amélioration de la liste Balises](#bkmk_improvetags)
-   [Amélioration de la liste Dates](#bkmk_improvedates)
-   [Amélioration de la vue SessionsTable](#bkmk_improvesessionstable)
-   [Amélioration de la vue SessionByCode](#bkmk_improvesessionbycode)
-   [Déploiement de l'application sur le site Web Azure](#bkmk_deployapplciation)

### Création d'un site Web dans Azure

Votre site Web Azure est exécuté dans un environnement d'hébergement partagé, ce qui veut dire qu'il s'exécute sur des machines virtuelles partagées avec d'autres clients Azure. Un environnement d'hébergement partagé permet de commencer à utiliser le cloud à moindre frais. Plus tard, si votre trafic Web augmente, vous pourrez mettre votre application à l'échelle pour répondre à vos besoins, en exécutant des machines virtuelles dédiées. Si vous avez besoin d'une architecture plus complexe, vous pouvez migrer vers un service cloud Azure. Les services cloud sont exécutés sur des machines virtuelles dédiées pour vous permettre de les configurer selon vos besoins.

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com). Dans le portail de gestion, cliquez sur **New**.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_1.png)

2.  Cliquez sur **Web Site**, puis sur **Création rapide**.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_2.png)

3.  Dans l'écran **Create a New Web Site**, dans la zone **URL**, entrez une chaîne à utiliser en tant qu’URL unique pour votre application.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_3.png)

    L'URL complète se composera du texte entré ici, ainsi que du suffixe affiché sous la zone de texte. L'illustration indique « MyMobileMVC4WebSite », mais si cette URL est déjà prise, vous devrez en choisir une autre. Sélectionnez la **RÉGION** dans laquelle vous vous trouvez.

4.  Cliquez sur la coche située en bas de la zone pour indiquer que vous avez terminé.

Le portail de gestion renvoie vers la page Sites Web, tandis que la colonne Status indique que le site est en cours de création. Après un temps d'attente (généralement inférieur à une minute), la colonne Status indique que la création du site a réussi. Dans la barre de navigation de gauche, le nombre de sites de votre compte apparaît en regard de l'icône Sites Web, tandis que le nombre de bases de données apparaît en regard de l'icône Bases de données SQL.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_4.png)

### Configuration du projet de départ

1.  Téléchargez le [projet de départ de l'application de listes de conférence](http://go.microsoft.com/fwlink/?LinkId=228307).

2.  Dans l'Explorateur Windows, cliquez ensuite avec le bouton droit sur le fichier MvcMobileStarterBeta.zip et sélectionnez *Propriétés*.

3.  Dans la boîte de dialogue Propriétés de MvcMobileRTMStarter.zip, cliquez sur le bouton Débloquer. Le déblocage empêche l'affichage d'un avertissement de sécurité, qui s'affiche normalement lorsque vous essayez d'utiliser un fichier .zip que vous avez téléchargé à partir d'Internet.

    ![Boîte de dialogue Propriétés](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/propertiespopup.png)

4.  Cliquez avec le bouton droit sur le fichier MvcMobile.zip, puis sélectionnez Extraire tout...

5.  Dans Visual Studio, ouvrez le fichier MvcMobile.sln.

### Pour exécuter le projet de départ

1.  Appuyez sur les touches Ctrl+F5 pour exécuter l'application, qui s'affiche dans le navigateur de votre ordinateur.
2.  Lancez l'émulateur de navigateur mobile, copiez l'URL de l'application de conférence dans l'émulateur, puis cliquez sur le lien Browse by tag.

    -   Si vous utilisez l'émulateur Windows Phone, cliquez sur la barre de l'URL et appuyez sur la touche Pause pour obtenir l'accès clavier. L'image suivante représente la vue AllTags (une fois que l'option Browse by tag a été sélectionnée).

    ![Page Browse by tag](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/BrowseByTagWithCallout.png)

L'affichage est tout à fait lisible sur un appareil mobile. Sélectionnez le lien ASP.NET.

![Parcourir les sessions ASP.NET](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ASPNetPage.png)

La vue avec balises ASP.NET est très encombrée. Par exemple, la colonne Date est à peine lisible. Plus loin dans ce didacticiel, vous allez créer une version de la vue AllTags prévue spécialement pour les navigateurs mobiles et permettant de rendre l'affichage lisible.

Remplacement des vues, des dispositions et des vues partielles
--------------------------------------------------------------

Dans cette section, vous allez créer un fichier de disposition mobile.

L'une des nouvelles fonctionnalités significatives d'ASP.NET MVC 4 est le mécanisme simple qui vous permet de remplacer toutes les vues (y compris les dispositions et les vues partielles) des navigateurs mobiles en général, mais aussi d'un navigateur mobile particulier ou encore d'un navigateur spécifique. Pour fournir un affichage mobile, vous pouvez copier un fichier de vue et ajouter .Mobile au nom du fichier. Par exemple, pour créer un affichage mobile Index, copiez *Views\\Home\\Index.cshtml* vers *Views\\Home\\Index.Mobile.cshtml*.

Tout d'abord, copiez *Views\\Shared\\\_Layout.cshtml* vers *Views\\Shared\\\_Layout.Mobile.cshtml*. Ouvrez *\_Layout.Mobile.cshtml* et remplacez le titre **MVC4 Conference** par **Conference (Mobile)**.

Dans chaque appel **Html.ActionLink**, supprimez « Browse by » de chaque lien ActionLink. Le code suivant affiche la section de corps terminée d'un fichier de disposition mobile.

     <body>
        <div class="page">
            <div id="header">
                <div id="logindisplay"></div>
                <div id="title">
                    <h1> Conference (Mobile)</h1>
                </div>
                <div id="menucontainer">
                    <ul id="menu">
                        <li>@Html.ActionLink("Home", "Index", "Home")</li>
                        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
                        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
                        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
                    </ul>
                </div>
            </div>
            <div id="main">
                @RenderBody()
            </div>
            <div id="footer">
            </div>
        </div>
    </body>

Copiez le fichier *Views\\Home\\AllTags.cshtml* vers *Views\\Home\\AllTags.Mobile.cshtml*. Ouvrez le nouveau fichier et, pour l'élément &lt;h2\>, remplacez « Tags » par « Tags (M) » :

     <h2>Tags (M)</h2>

Accédez à la page des balises à l'aide d'un navigateur de Bureau et de l'émulateur de navigateur mobile. L'émulateur de navigateur mobile indique les deux modifications que vous avez effectuées.

![Afficher les modifications sur la page de balises](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p2m_layouttags_mobile_thumb.png)

Par contre, l'affichage du Bureau n'a pas changé.

![Affichage de la vue Bureau des balises](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Windows-Live-Writer_ASP_NET-MVC-4-Mobile-Features_D2FF_p2_layoutTagsDesktop_thumb.png)

Utilisation de jQuery Mobile pour définir l'interface du navigateur mobile
--------------------------------------------------------------------------

Dans cette section, vous allez installer le package NuGet jQuery.Mobile.MVC, qui installe jQuery Mobile et un widget de basculement de vues.

La bibliothèque [jQuery Mobile](http://jquerymobile.com/demos/1.0b3/#/demos/1.0b3/docs/about/intro.html) fournit une infrastructure d'interface utilisateur compatible avec les navigateurs mobiles les plus populaires. jQuery Mobile applique une amélioration progressive aux navigateurs mobiles qui prennent CSS et JavaScript en charge. L'amélioration progressive permet à tous les navigateurs d'afficher le contenu de base d'une page Web, tout en donnant la possibilité aux navigateurs et appareils plus puissants d'afficher un contenu plus riche. Les fichiers JavaScript et CSS inclus dans jQuery Mobile définissent le style de nombreux éléments afin qu'ils puissent être affichés dans les navigateurs mobiles sans changement de balisage.

1.  Supprimez le fichier *Shared\\\_Layout.Mobile.cshtml* que vous avez créé précédemment.

2.  Renommez le fichier *Views\\Home\\AllTags.Mobile.cshtml* en lui attribuant le nom *Views\\Home\\AllTags.Mobile.cshtml.hide* (vous réutiliserez ce fichier plus tard). Comme le fichier n'a plus d'extension .cshtml, il ne sera plus utilisé par le runtime ASP.NET MVC pour afficher la vue *AllTags*.

3.  Installez le package NuGet jQuery.Mobile.MVC en procédant comme suit :

    a. Dans le menu **Tools**, sélectionnez **Console du Gestionnaire de package**, puis **Library Package Manager**.

         ![Library package manager][jquery1]

    b. Dans la **Console du Gestionnaire de package**, entrez *Install-Package jQuery.Mobile.MVC -version 1.0.0*

         ![Package manager console][jquery2]

Le package NuGet jQuery.Mobile.MVC installe les éléments suivants :

-   Le fichier *App\_Start\\BundleMobileConfig.cs*, dont vous avez besoin pour référencer les fichiers jQuery JavaScript et CSS ajoutés. Vous devez suivre les instructions ci-dessous pour référencer l'offre groupée définie dans ce fichier.
-   Des fichiers CSS jQuery Mobile.
-   Un widget de contrôle ViewSwitcher (*Controllers\\ViewSwitcherController.cs)*.
-   Les fichiers JavaScript jQuery Mobile.
-   Un fichier de disposition jQuery Mobile (*Views\\Shared\_Layout.Mobile.cshtml*).
-   Une vue partielle de basculement de vues (*MvcMobile\\Views\\Shared\_ViewSwitcher.cshtml*), qui fournit en haut de chaque page un lien permettant de basculer de la vue Bureau à l'affichage mobile et vice-versa.
-   Plusieurs fichiers image .png et .gif dans le dossier Content/images.

Ouvrez le fichier *Global.asax* et ajoutez le code suivant comme dernière ligne de la méthode Application\_Start.

BundleMobileConfig.RegisterBundles(BundleTable.Bundles);

Le code suivant affiche le fichier Global.asax complet.

    using System; 
    using System.Web.Http; 
    using System.Web.Mvc; 
    using System.Web.Optimization; 
    using System.Web.Routing; 
    using System.Web.WebPages; 
     
    namespace MvcMobile 
    { 
     
        public class MvcApplication : System.Web.HttpApplication 
        { 
            protected void Application_Start() 
            { 
                DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone") 
                { 
                    ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf 
                        ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0) 
                }); 
                AreaRegistration.RegisterAllAreas(); 
     
                WebApiConfig.Register(GlobalConfiguration.Configuration); 
                FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters); 
                RouteConfig.RegisterRoutes(RouteTable.Routes); 
                BundleConfig.RegisterBundles(BundleTable.Bundles); 
                BundleMobileConfig.RegisterBundles(BundleTable.Bundles); 
            } 
        } 
    }

Ouvrez le fichier *MvcMobile\\Views\\Shared\\\_Layout.Mobile.cshtml* et ajoutez le balisage suivant après l'appel *Html.Partial* :

    <div data-role="header" align="center">
        @Html.ActionLink("Home", "Index", "Home")
        @Html.ActionLink("Date", "AllDates")
        @Html.ActionLink("Speaker", "AllSpeakers")
        @Html.ActionLink("Tag", "AllTags")
    </div>

La section de corps terminée ressemble à ceci :

     <body>
        <div data-role="page" data-theme="a">
            @Html.Partial("_ViewSwitcher")
            <div data-role="header" align="center">
                @Html.ActionLink("Home", "Index", "Home")
                @Html.ActionLink("Date", "AllDates")
                @Html.ActionLink("Speaker", "AllSpeakers")
                @Html.ActionLink("Tag", "AllTags")
            </div>
            <div data-role="header">
                <h1>@ViewBag.Title</h1>
            </div>
            <div data-role="content">
                @RenderSection("featured", false)
                @RenderBody()
            </div>
        </div>
    </body>

Générez l'application, puis dans l'émulateur de navigateur mobile, accédez à la vue AllTags. Vous voyez l'affichage suivant :

![Après installation de jquery via nuget](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_afternuget_thumb.png)

**Remarque**

Vous pouvez déboguer le code mobile en définissant la chaîne d'agent utilisateur pour IE ou Chrome sur iPhone, puis en vous servant des outils de développement F-12. Si le navigateur mobile n'affiche pas les liens **Accueil**, **Intervenant**, **Balise** et **Date** en tant que boutons, les références aux scripts jQuery Mobile et aux fichiers CSS sont probablement incorrectes.

Outre les changements de style, vous pouvez voir la mention **Displaying mobile view** et un lien qui vous permet de basculer de l'affichage mobile à la vue Bureau. Cliquez sur le **lien Vue Bureau** pour afficher la vue Bureau.

La vue Bureau ne vous donne pas la possibilité de retourner directement à l'affichage mobile. Vous allez corriger cela maintenant. Ouvrez le fichier *Views\\Shared\\\_Layout.cshtml*. Directement sous l'élément &lt;body\>, ajoutez le code suivant, qui affiche le widget de basculement de vues :

    @Html.Partial("_ViewSwitcher")

Voici le code terminé :

     <body>
        @Html.Partial("_ViewSwitcher")

        <div id="title">
            <h1> MVC4 Conference </h1>
        </div>

        @* Code supprimé à des fins de clarté.@*
    </body>

Actualisez la vue **AllTags** dans le navigateur mobile. Vous pouvez maintenant naviguer de la vue Bureau à l'affichage mobile et vice-versa.

![Accéder aux affichages mobiles](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_desktopviewwithmobilelink_thumb.png)

**Remarque**

Vous pouvez afficher le code suivant à la fin du fichier Views\\Shared\\\_ViewSwitcher.cshtml afin de déboguer les vues lors de l'utilisation d'un navigateur que l'agent utilisateur a défini sur un appareil mobile.

``` {}
    else 
    { 
         @:Not Mobile/Get 
    } 
```

et ajouter le titre suivant au fichier Views\\Shared\\\_Layout.cshtml.

``` {}
    <h1>Non Mobile Layout MVC4 Conference</h1>
```

Dans un navigateur de Bureau, accédez à la page AllTags. Le widget de basculement de vues n'est pas affiché dans un navigateur de Bureau parce qu'il est ajouté uniquement à la page de disposition mobile. Plus tard dans ce didacticiel, nous vous montrerons comment ajouter le widget de basculement de vues à la vue Bureau.

![Afficher la vue Bureau](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Windows-Live-Writer_ASP_NET-MVC-4-Mobile-Features_D2FF_p3_desktopBrowser_thumb.png)

Amélioration de la liste Intervenants
-------------------------------------

Dans le navigateur mobile, sélectionnez le lien **Intervenants**. Étant donné qu'il n'y a pas d'affichage mobile (*AllSpeakers.Mobile.cshtml*), la vue par défaut des intervenants (*AllSpeakers.cshtml*) est affichée à l'aide du mode de disposition mobile (*\_Layout.Mobile.cshtml*).

![Afficher la liste mobile des intervenants](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_speakersdesktop_thumb.png)

Vous pouvez globalement désactiver l'affichage d'une vue par défaut (non mobile) au sein d'une disposition mobile en définissant RequireConsistentDisplayMode sur true dans le fichier *Views\_ViewStart.cshtml*, comme indiqué ci-dessous :

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_speakersconsistent_thumb.png)

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModes.RequireConsistentDisplayMode = true;
    }

Lorsque *RequireConsistentDisplayMode* est défini sur true, la disposition mobile (*\_Layout.Mobile.cshtml*) est utilisée uniquement pour les affichages mobiles (c'est-à-dire que le fichier de vue est du type ViewName.Mobile.cshtml). Vous pouvez définir *RequireConsistentDisplayMode* sur true si votre disposition mobile ne fonctionne pas bien avec les affichages non mobiles. La capture d'écran suivante montre comment la page Intervenants s'affiche lorsque *RequireConsistentDisplayMode* est défini sur true.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_thumb.png)

Vous pouvez désactiver le mode d'affichage cohérent dans une vue en définissant *RequireConsistentDisplayMode* sur false dans le fichier de vue. Le balisage suivant du fichier *Views\\Home\\AllSpeakers.cshtml* définit *RequireConsistentDisplayMode* sur false :

    @model IEnumerable<string>
    @{
        ViewBag.Title = "All speakers";
        DisplayModes.RequireConsistentDisplayMode = false;
    }

Création d'un affichage mobile des intervenants
-----------------------------------------------

Comme vous venez de le voir, la vue Intervenants est lisible, mais les liens sont petits et il est difficile de les sélectionner sur un appareil mobile. Dans cette section, vous allez créer un affichage mobile des intervenants qui ressemble à une application mobile moderne, c'est-à-dire qui contient des liens de grande taille faciles à sélectionner et un champ de recherche pour trouver rapidement les intervenants.

1.  Copiez *AllSpeakers.cshtml* vers *AllSpeakers.Mobile.cshtml.* Ouvrez le fichier *AllSpeakers.Mobile.cshtml* et supprimez l'élément de titre &lt;h2\>.
2.  Dans la balise **&lt;ul\>**, ajoutez l'attribut data-role et définissez-le sur *listview*. À l'instar des autres *attributs data-**, \*data-role="listview"* rend les grands éléments de liste plus faciles à sélectionner. Le balisage terminé ressemble à ceci :

         @model IEnumerable<string>
         @{
             ViewBag.Title = "All speakers";
         }
         <ul data-role="listview">
             @foreach(var speaker in Model) {
                 <li>@Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker })</li>
             }
         </ul>

3.  Actualisez le navigateur mobile. L'affichage actualisé ressemble à ceci :

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_updatedspeakerview1_thumb.png)

4.  Dans la balise **&lt;ul\>**, ajoutez l'attribut data-filter et définissez-le sur true. Le code ci-dessous affiche le balisage ul :

        <ul data-role="listview" data-filter="true">

L'image suivante représente le champ de filtrage de recherche du haut de la page généré par l'attribut data-filter.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_thumb.png)

Au fur et à mesure que vous renseignez le champ de recherche, jQuery Mobile filtre la liste affichée, comme indiqué sur l'image ci-dessous.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb.png)

Amélioration de la liste Balises
--------------------------------

À l'instar de la vue par défaut Intervenants, la vue Balises est lisible, mais les liens sont petits et il est difficile de les sélectionner sur un appareil mobile. Dans cette section, vous allez améliorer la vue Balises de la même manière que vous avez amélioré la vue Intervenants.

1.  Renommez le fichier *Views\\Home\\AllTags.Mobile.cshtml.hide* en *Views\\Home\\AllTags.Mobile.cshtml*. Ouvrez le fichier renommé et supprimez l'élément **&lt;h2\>**.

2.  Ajoutez les attributs data-role et data-filter à la balise **&lt;ul\>**, comme indiqué ci-après :

         <ul data-role="listview" data-filter="true">

    L'image suivante représente la page de balises filtrée selon la lettre J.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_tags_j_thumb.png)

Amélioration de la liste Dates
------------------------------

Vous pouvez améliorer la vue Dates de la même manière que les vues **Intervenants** et **Balises** afin d'en faciliter l'utilisation sur un appareil mobile.

1.  Copiez le fichier *Views\\Home\\AllDates.Mobile.cshtml* vers *Views\\Home\\AllDates.Mobile.cshtml*.
2.  Ouvrez le nouveau fichier et supprimez l'élément **&lt;h2\>**.
3.  Ajoutez *data-role="listview"* à la balise &lt;ul\>, comme indiqué ci-dessous :

         <ul data-role="listview">

L'image ci-dessous montre à quoi la page **Date** ressemble avec l'attribut data-role en place.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_dates1_thumb.png)

Remplacez le contenu du fichier *Views\\Home\\AllDates.Mobile.cshtml* par le code suivant :

    @model IEnumerable<DateTime>
    @{
        ViewBag.Title = "All dates";
        DateTime lastDay = default(DateTime);
    }
    <ul data-role="listview">
        @foreach(var date in Model) {
            if (date.Date != lastDay) {
                lastDay = date.Date;
                <li data-role="list-divider">@date.Date.ToString("ddd, MMM dd")</li>
            }
            <li>@Html.ActionLink(date.ToString("h:mm tt"), "SessionsByDate", new { date })</li>
        }
    </ul>

Ce code regroupe toutes les sessions par jour. Il crée un séparateur de liste pour chaque nouveau jour et affiche toutes les sessions de chaque jour sous un séparateur. Voici à quoi cela ressemble lorsque ce code est exécuté :

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_dates2_thumb.png)

Amélioration de la vue SessionsTable
------------------------------------

Dans cette section, vous allez créer un affichage mobile des sessions. Les modifications que nous allons effectuer sont plus importantes que celles réalisées dans les autres vues que nous avons créées.

Dans le navigateur mobile, appuyez sur le bouton **Intervenant**, puis saisissez Sc dans le champ de recherche.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb_1.png)

Appuyez sur le lien **Scott Hanselman**.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_scottha_thumb.png)

Comme vous pouvez le voir, l'affichage est difficile à lire dans un navigateur mobile. La colonne de dates est difficile à lire et la colonne de balises n'est pas visible. Pour remédier à cela, copiez Views\*Home\\SessionsTable.cshtml\* vers *Views\\Home\\SessionsTable.Mobile.cshtml*, puis remplacez le contenu du fichier par le code suivant :

    @using MvcMobile.Models
    @model IEnumerable<Session>

    <ul data-role="listview">
        @foreach(var session in Model) {
            <li>
                <a href="@Url.Action("SessionByCode", new { session.Code })">
                    <h3>@session.Title</h3>
                    <p><strong>@string.Join(", ", session.Speakers)</strong></p>
                    <p>@session.DateText</p>
                </a>
            </li>
        }
    </ul>

Le code supprime l'espace et les colonnes de balises, et formate le titre, l'intervenant et la date verticalement afin que toutes les informations soient lisibles dans un navigateur mobile. L'image qui suit reflète les changements réalisés à l'aide du code.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_sessionsbyscottha_thumb.png)

Amélioration de la vue SessionByCode
------------------------------------

Pour finir, vous allez créer un affichage mobile de la vue **SessionByCode**. Dans le navigateur mobile, appuyez sur le bouton **Intervenant**, puis saisissez Sc dans le champ de recherche.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb_2.png)

Appuyez sur le lien **Scott Hanselman**. Les sessions de Scott Hanselman s'affichent.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_scottha_thumb.png)

Sélectionnez le lien **An Overview of the MS Web Stack of Love**.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_love_thumb.png)

La vue Bureau par défaut ne présente aucun problème particulier, mais vous pouvez l'améliorer.

Copiez le fichier *Views\\Home\\SessionByCode.cshtml* vers *Views\\Home\\SessionByCode.Mobile.cshtml* et remplacez le contenu du fichier *Views\\Home\\SessionByCode.Mobile.cshtml* par le balisage suivant :

    @model MvcMobile.Models.Session

    @{
        ViewBag.Title = "Session details";
    }
    <h2>@Model.Title</h2>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>

    <ul data-role="listview" data-inset="true">
        <li data-role="list-divider">Speakers</li>
        @foreach (var speaker in Model.Speakers) {
            <li>@Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker })</li>
        }
    </ul>

    <p>@Model.Description</p>
    <h4>Code: @Model.Code</h4>

    <ul data-role="listview" data-inset="true">
        <li data-role="list-divider">Tags</li>
        @foreach (var tag in Model.Tags) {
            <li>@Html.ActionLink(tag, "SessionsByTag", new { tag })</li>
        }
    </ul>

Le nouveau balisage utilise l'attribut **data-role** pour améliorer la disposition de la vue.

Actualisez le navigateur mobile. L'image suivante reflète les changements que vous venez de réaliser à l'aide du code :

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_love2_thumb.png)

Déploiement de l'application sur le site Web Azure
--------------------------------------------------

1.  Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le projet, puis dans le menu contextuel, sélectionnez **Publier**.

    ![Publier dans le menu contextuel du projet](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/PublishVSSolution.png)

    L'Assistant **Publier le site Web** s'ouvre.

2.  Sous l'onglet **Profil** de l'Assistant **Publier le site Web**, cliquez sur **Importer**.

    ![Importer les paramètres de publication](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ImportPublishSettings.png)

    La boîte de dialogue **Importer le profil de publication** apparaît.

3.  Si vous n'avez pas encore ajouté votre abonnement Azure dans Visual Studio, procédez comme suit. Au cours de ces étapes, vous allez ajouter votre abonnement de telle sorte que votre site Web figure dans la liste déroulante située sous **Import from an Azure web site**.

    1.  Dans la boîte de dialogue **Importer le profil de publication**, cliquez sur **Add Azure subscription**.

    ![ajouter un abonnement azure](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzAddWAsub.png)

    1.  Dans la boîte de dialogue **Import Azure Subscriptions**, cliquez sur **Télécharger le fichier d'abonnement**.

    ![télécharger un abonnement](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDownLoad.png)

    1.  Dans la fenêtre de votre navigateur, enregistrez le fichier *.publishsettings*.

    ![télécharger un fichier de publication](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDown2.png)

    [WACOM.INCLUDE [publishsettingsfilewarningchunk](../includes/publishsettingsfilewarningchunk.md)] &lt;/br\>

    1.  Dans la boîte de dialogue **Import Azure Subscriptions**, cliquez sur **Parcourir** et accédez au fichier *.publishsettings*.

    ![télécharger un abonnement](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDownLoad.png)

    1.  Cliquez sur **Importer**.

    ![importer](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzImp.png)

    1.  Dans la boîte de dialogue **Importer le profil de publication**, sélectionnez **Import from an Azure web site**, sélectionnez votre site Web dans la liste déroulante, puis cliquez sur **OK**.

    ![Importer le profil de publication](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ImportPublishProfile.png)

    1.  Sous l'onglet **Connexion**, cliquez sur **Validate Connection** pour vous assurer que les paramètres sont corrects.

    ![Valider la connexion](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ValidateConnection.png)

    1.  Une fois la connexion validée, une coche verte est affichée en regard du bouton **Validate Connection**. 
    ![icône de connexion réussie et bouton Suivant sous l'onglet Connexion](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/dntutmobile-deploy1-publish-005.png)

4.  Vous pouvez accepter les paramètres par défaut sur cette page. Vous déployez une configuration de build Release et il n'est pas nécessaire de supprimer les fichiers du serveur de destination. L'entrée **UsersContext (DefaultConnection)** sous **Bases de données** provient de la classe *UsersContext:DbContext*, qui utilise la chaîne DefaultConnection. Cliquez sur **Suivant**.

    ![icône de connexion réussie et bouton Suivant sous l'onglet Connexion](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rxPWS.png)

    1.  Sous l'onglet **Aperçu**, cliquez sur **Démarrer l'aperçu**. L'onglet affiche la liste des fichiers qui seront copiés sur le serveur. L'affichage de l'aperçu n'est pas requis pour publier l'application. Cependant, il s'agit d'une fonction utile à ne pas négliger. Dans ce cas, vous n'avez rien à faire avec la liste des fichiers affichée. À la prochaine publication, seuls les fichiers modifiés seront affichés dans la liste d'aperçu.

    ![Bouton Démarrer l'aperçu sous l'onglet Aperçu](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/dntutmobile-deploy1-publish-007.png)

    1.  Cliquez sur **Publier**.

    Visual Studio démarre le processus de copie des fichiers vers le serveur Azure. La fenêtre **Sortie** indique les actions de déploiement entreprises et signale la réussite du déploiement.

    1.  Le navigateur par défaut ouvre automatiquement l'URL du site déployé. L'application créée est maintenant exécutée dans le cloud.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_15.png)

Vous pouvez tester votre site Web à l'aide de l'émulateur de téléphone en accédant à l'URL du site dans le navigateur mobile.

