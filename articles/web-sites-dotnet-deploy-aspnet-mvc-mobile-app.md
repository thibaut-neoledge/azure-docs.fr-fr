<properties 
	pageTitle="Site Web mobile .NET ASP.NET MVC 5 - Didacticiels Azure" 
	description="Un didacticiel qui explique comment déployer une application Web sur un site Web Azure à l'aide des fonctionnalités mobiles de l'application Web ASP.NET MVC 5." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/19/2014" 
	ms.author="cephalin,riande"/>


# Déploiement d'une application Web mobile ASP.NET MVC 5 sur les sites Web Azure

Dans ce didacticiel, vous allez apprendre les principes fondamentaux de la création d'une application Web ASP.NET MVC 5
adaptée aux appareils mobiles et son déploiement dans Azure. Pour ce didacticiel, vous devez disposer de 
[Visual Studio Express 2013 pour le Web][Visual Studio Express 2013] ou de l'édition professionnelle de Visual Studio si vous l'avez déjà.

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

### Contenu

Pour ce didacticiel, vous devrez ajouter des fonctionnalités mobiles à l'application simple de listes de conférence fournie dans le [projet de départ][StarterProject]. La capture d'écran suivante illustre les sessions ASP.NET dans l'application terminée, telles qu'elles s'affichent dans l'émulateur de navigateur des outils de développement F12 d'Internet Explorer 11.

![][FixedSessionsByTag]

Vous pouvez utiliser les outils destinés aux développeurs F12 d'Internet Explorer 11 et [l'outil Fiddler][Fiddler] pour vous aider à déboguer votre application. 

### Compétences

Contenu :

-	Comment utiliser Visual Studio 2013 pour publier directement votre application Web sur un site Web Microsoft Azure.
-   Comment les modèles ASP.NET MVC 5 utilisent l'infrastructure Bootstrap CSS pour optimiser l'affichage sur les appareils mobiles
-   Comment créer des affichages adaptés aux appareils mobiles qui ciblent des navigateurs mobiles spécifiques, comme ceux de l'iPhone ou des téléphones Android
-   Comment créer des affichages réactifs (qui fonctionnent dans différents navigateurs et appareils)

<h2>Configuration de l'environnement de développement</h2>

Configurez l'environnement de développement en installant le Kit de développement logiciel (SDK) Azure pour .NET Framework. 

1. Pour installer le Kit de développement logiciel (SDK) Azure pour .NET, cliquez sur le lien ci-dessous. Si Visual Studio 2013 n'est pas encore installé, il le sera par l'intermédiaire de ce lien. Ce didacticiel nécessite Visual Studio 2013. [Kit de développement logiciel (SDK) Azure pour Visual Studio 2013][AzureSDKVs2013]
1. Dans la fenêtre Web Platform Installer, cliquez sur **Installer**, puis poursuivez l'installation.

	![Web Platform Installer - Azure SDK for .NET][WebPIAzureSdk23NetVS13]

Vous aurez également besoin d'un émulateur de navigateur mobile. Vous pouvez utiliser l'un des émulateurs suivants :

-   Émulateur de navigateur dans les [outils destinés aux développeurs F12 d'Internet Explorer 11][EmulatorIE11] (utilisé dans toutes les captures d'écran de navigateur mobile). Il dispose de présélections de chaîne d'agent utilisateur pour Windows Phone 8, Windows Phone 7 et l'iPad d'Apple.
-	Émulateur de navigateur de [Google Chrome DevTools][EmulatorChrome]. Il inclut des présélections pour de nombreux périphériques Android, ainsi que pour l'iPhone d'Apple, l'iPad d'Apple et le Kindle Fire d'Amazon. Il émule également des événements tactiles.
-   [Émulateur mobile Opera][EmulatorOpera]

Les projets Visual Studio avec code source C# sont disponibles pour cette rubrique :

-   [Téléchargement du projet de départ][StarterProject]
-   [Téléchargement du projet terminé][CompletedProject]

<h2>Étapes de ce didacticiel</h2>

- [Déploiement du projet de départ sur un site Web Microsoft Azure][]
- [Infrastructure CSS Bootstrap][]
- [Remplacement des vues, des dispositions et des vues partielles][]
- [Amélioration de la liste Intervenants][]
- [Amélioration de la liste Balises][]
- [Amélioration de la liste Dates][]
- [Amélioration de la vue SessionsTable][]
- [Amélioration de la vue SessionByCode][]

<h3><a name="bkmk_DeployStarterProject"></a>Déploiement du projet de départ sur un site Web Microsoft Azure</h3>

1.	Téléchargez le [projet de départ][StarterProject] de l'application de listes de conférence.

2. 	Dans l'Explorateur Windows, cliquez ensuite avec le bouton droit sur le fichier Mvc5Mobile.zip et sélectionnez *Properties*.

3. 	Dans la boîte de dialogue **Propriétés de Mvc5Mobile.zip**,cliquez sur le bouton **Débloquer**. (Le déblocage empêche l'affichage d'un avertissement de sécurité, qui s'affiche normalement lorsque vous essayez d'utiliser un fichier *.zip* que vous avez téléchargé à partir d'Internet.)

4.	Cliquez avec le bouton droit sur le fichier *Mvc5Mobile.zip*, puis sélectionnez **Extraire tout** pour décompresser l'archive. 

5. 	Dans Visual Studio, ouvrez le fichier *Mvc5Mobile.sln*.

6.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis cliquez sur **Publier**.

	![][DeployClickPublish]

7.	Dans Publier le site Web, cliquez sur **Sites Web Microsoft Azure**.

	![][DeployClickWebSites]

8.	Cliquez sur **Se connecter**.

	![][DeploySignIn]

9.	Saisissez votre nom d'utilisateur Microsoft Azure et cliquez sur **Continuer**.

	![][DeployUsername]

10.	Saisissez votre mot de passe et cliquez sur **Se connecter**.

	![][DeployPassword]

11. La boîte de dialogue Sélectionner un site Web existant devrait à présent indiquer que vous êtes connecté. Cliquez sur **Nouveau**.

	![][DeployNewWebsite]  

12. Dans le champ **Nom du site**, spécifiez un préfixe unique pour le nom du site. Le nom complet de votre site sera *&lt;prefix>*.azurewebsites.net. Par ailleurs, dans le champ **Région**, sélectionnez une région. Ensuite, cliquez sur **Créer**.

	![][DeploySiteSettings]

13.	La boîte de dialogue Publier le site Web est renseignée avec les paramètres de site correspondant à votre nouveau site Web. Cliquez sur **Publier**.

	![][DeployPublishSite]

	Lorsque Visual Studio termine la publication du projet de départ sur le site Web Microsoft Azure, le navigateur de bureau s'ouvre pour afficher le site Web actif.

14.	Lancez l'émulateur de navigateur mobile, copiez l'URL de l'application de conférence (*<prefix>*.azurewebsites.net) dans l'émulateur, puis cliquez sur le bouton en haut à droite et sélectionnez **Parcourir par balise**. Si vous utilisez Internet
Explorer 11 comme navigateur par défaut, vous devez simplement appuyer sur `F12`, sur
`CTRL + 8`, puis modifier le profil de navigateur à **Windows Phone**. L'image suivante illustre la vue *AllTags* en mode portrait (une fois que l'option
**Parcourir par balise** a été sélectionnée).

	![][AllTags]

>[AZURE.NOTE] De même que vous pouvez déboguer votre application MVC 5 dans Visual Studio, vous pouvez republier votre site Web sur Microsoft Azure pour vérifier le site en ligne directement à partir de votre navigateur mobile ou d'un émulateur de navigateur.

L'affichage est tout à fait lisible sur un appareil mobile. Vous pouvez également déjà voir certains effets visuels appliqués par l'infrastructure CSS Bootstrap.
Cliquez sur le lien **ASP.NET**.

![][SessionsByTagASP.NET]

La vue avec balises ASP.NET s'adapte à l'écran, ce que Bootstrap effectue pour vous automatiquement. Cependant, vous pouvez optimiser cette vue pour qu'elle s'adapte mieux au navigateur mobile. Par exemple, la colonne **Date** n'est pas très lisible. Plus loin dans ce didacticiel, vous allez modifier la vue *AllTags* pour l'adapter aux appareils mobiles.

<h2><a name="bkmk_bootstrap"></a>Infrastructure CSS Bootstrap</h2>

La prise en charge de Bootstrap intégrée est une nouveauté du modèle MVC 5. Vous avez déjà constaté dans quelle mesure cette prise en charge améliore les différentes vues dans votre application. Par exemple, la barre de navigateur dans la partie supérieure se réduit automatiquement lorsque la largeur du navigateur est inférieure. Dans le navigateur de bureau,essayez de redimensionner la fenêtre du navigateur et observez le comportement de la barre de navigation. Il s'agit de la conception de sites Web réactive intégrée à
Bootstrap.

Pour voir l'aspect de l'application Web sans Bootstrap, ouvrez
*App\_Start\\BundleConfig.cs* et placez en commentaires les lignes qui contiennent
*bootstrap.js* et *bootstrap.css*. Le code suivant indique les deux dernières instructions de la méthode `RegisterBundles` après la modification :

     bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
              //"~/Scripts/bootstrap.js",
              "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css").Include(
              //"~/Content/bootstrap.css",
              "~/Content/site.css"));

Appuyez sur `Ctrl+F5` pour exécuter l'application.

La barre de navigation réductible a maintenant la forme d'une simple liste non triée classique. Recliquez sur **Parcourir par balise**, puis cliquez sur **ASP.NET**.
Dans la vue de l'émulateur mobile, vous pouvez à présent constater que l'affichage n'est plus adapté à l'écran et que vous devez faire défiler la page horizontalement pour voir le côté droit du tableau.

![][SessionsByTagASP.NETNoBootstrap]

Annulez vos modifications et actualisez le navigateur mobile pour vérifier que l'affichage adapté aux appareils mobiles a été rétabli.

Bootstrap n'est pas propre à ASP.NET MVC 5 et vous pouvez tirer parti de ces fonctionnalités dans n'importe quelle application Web. Désormais, Bootstrap est intégré au modèle de projet
ASP.NET MVC 5. Votre application Web MVC 5 peut donc tirer parti de Bootstrap par défaut.

Pour plus d'informations sur Bootstrap, accédez au
[site Bootstrap][BootstrapSite].

La section suivante vous explique comment fournir des affichages adaptés à des navigateurs spécifiques d'appareil mobile.

<h2><a name="bkmk_overrideviews"></a>Remplacement des vues, des dispositions et des vues partielles</h2>

Vous pouvez remplacer toutes les vues (y compris les dispositions et les vues partielles) des navigateurs mobiles en général, mais aussi d'un navigateur mobile en particulier ou encore d'un navigateur spécifique. Pour fournir un affichage mobile, vous pouvez copier un fichier de vue et ajouter *.Mobile* au nom du fichier. Par exemple, pour créer un affichage
*Index* pour mobile, vous pouvez copier *Views\\Home\\Index.cshtml* sur 
*Views\\Home\\Index.Mobile.cshtml*.

Dans cette section, vous allez créer un fichier de disposition mobile.

Pour commencer, copiez *Views\\Shared\\\_Layout.cshtml* sur
*Views\\Shared\\\_Layout.Mobile.cshtml*. Ouvrez *\_Layout.Mobile.cshtml* et remplacez le titre **MVC5 Application** par **MVC5 Application (Mobile)**.

Dans chaque appel `Html.ActionLink` pour la barre de navigation, supprimez "Parcourir par" de chaque lien.
*ActionLink*. Le code suivant affiche la balise `<ul class="nav navbar-nav">` terminée d'un fichier de disposition mobile.

    <ul class="nav navbar-nav">
        <li>@Html.ActionLink("Home", "Index", "Home")</li>
        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
    </ul>

Copiez le fichier *Views\\Home\\AllTags.cshtml* sur
*Views\\Home\\AllTags.Mobile.cshtml*. Ouvrez le nouveau fichier et remplacez l'élément
`<h2>`"Tags" par "Tags (M)":

    <h2>Tags (M)</h2>

Accédez à la page des balises à l'aide d'un navigateur de bureau et de l'émulateur de navigateur mobile. L'émulateur de navigateur mobile indique les deux modifications que vous avez effectuées :
(le titre de *\_Layout.Mobile.cshtml* et le titre de
*AllTags.Mobile.cshtml*).

![][AllTagsMobile_LayoutMobile]

En revanche, l'affichage de bureau n'a pas changé (avec les titres de *\_Layout.cshtml* et 
*AllTags.cshtml*).

![][AllTagsMobile_LayoutMobileDesktop]

<h2><a name="bkmk_browserviews"></a>Création d'affichages mobiles</h2>

Outre les affichages mobiles et de bureau, vous pouvez créer des affichages pour un navigateur en particulier. Par exemple, vous pouvez créer des affichages spécialement adaptés aux navigateurs de l'iPhone et d'Android. Dans cette section,vous allez créer une disposition pour le navigateur de l'iPhone et une version pour iPhone de la vue *AllTags*.

Ouvrez le fichier *Global.asax* et ajoutez le code suivant comme dernière ligne de la méthode
`Application_Start`.

    DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone")
    {
        ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf
            ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0)
    });

Ce code définit un nouveau mode d'affichage appelé "iPhone", qui répondra à chaque demande entrante. Si la demande entrante correspond à la condition que vous avez définie (autrement dit, si l'agent utilisateur contient la chaîne
"iPhone"), ASP.NET MVC recherchera les vues dont le nom contient le
suffixe "iPhone".

>[AZURE.NOTE] Quand vous ajoutez des modes d'affichage de navigateur mobile, pour iPhone et Android par exemple, assurez-vous de définir le premier argument sur `0` (insérez-le en haut de la liste) pour que le mode navigateur ait la priorité sur le modèle mobile (*.Mobile.cshtml). Si le modèle mobile est en haut de la liste, il est sélectionné à la place du mode d'affichage souhaité (le premier résultat est prioritaire et le modèle mobile correspond à tous les navigateurs mobiles). 

Dans le code, cliquez avec le bouton droit sur `DefaultDisplayMode`, choisissez **Resolve**, puis `using System.Web.WebPages;`. Cela ajoute une référence à l'
espace de noms `System.Web.WebPages`, qui est l'emplacement où les types
`DisplayModeProvider` et `DefaultDisplayMode` sont définis.

![][ResolveDefaultDisplayMode]

Vous pouvez également ajouter simplement manuellement la ligne suivante à la
section `using` du fichier.

    using System.Web.WebPages;

Enregistrez les modifications. Copiez
*Views\\Shared\\\_Layout.Mobile.cshtml* file to
*Views\\Shared\\\_Layout.iPhone.cshtml*. Open the new file
and then change the title from `MVC5 Application (Mobile)` to
`MVC5 Application (iPhone)`.

Copiez le fichier *Views\\Home\\AllTags.Mobile.cshtml* sur
*Views\\Home\\AllTags.iPhone.cshtml*. In the new file, change
the `<h2>` element from "Tags (M)" to "Tags (iPhone)".

Exécutez l'application. Lancez un émulateur de navigateur mobile, vérifiez que son agent utilisateur est défini sur "iPhone" et accédez à la vue *AllTags*. Si vous utilisez l'émulateur dans les outils destinés aux développeurs F12 d'Internet Explorer 11, configurez l'émulation comme suit :

-   Profil du navigateur = **Windows Phone**
-   Chaîne d'agent utilisateur =  **Custom**
-   Chaîne personnalisée = **Apple-iPhone5C1/1001.525**

La capture d'écran qui suit présente la vue *AllTags* dans l'émulateur des outils de développement F12 d'Internet Explorer 11, avec la chaîne d'agent utilisateur personnalisée (il s'agit de la chaîne d'agent utilisateur d'un iPhone 5C).

![][AllTagsIPhone_LayoutIPhone]

Dans le navigateur mobile, sélectionnez le lien **Intervenants**. Comme il n'y a pas de vue mobile (*AllSpeakers.Mobile.cshtml*), la vue par défaut Intervenants
(*AllSpeakers.cshtml*) est affichée à l'aide de la vue de disposition mobile
(*\_Layout.Mobile.cshtml*). Comme illustré ci-dessous, le titre **MVC5 Application
(Mobile)** est défini dans *\_Layout.Mobile.cshtml*.

![][AllSpeakers_LayoutMobile]

Vous pouvez désactiver globalement l'affichage d'une disposition mobile par une vue par défaut (non-mobile) en définissant `RequireConsistentDisplayMode` sur
`true` dans le fichier *Views\\\_ViewStart.cshtml*, comme suit :

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = true;
    }

Lorsque `RequireConsistentDisplayMode` est défini sur `true`, la disposition mobile
(*\_Layout.Mobile.cshtml*) est utilisée uniquement pour les affichages mobiles (par exemple, lorsque le fichier de vue est sous la forme ***ViewName**.Mobile.cshtml*). Vous pouvez définir `RequireConsistentDisplayMode` sur `true` si votre disposition mobile ne fonctionne pas correctement avec les affichages non mobiles. La capture d'écran suivante indique le mode d'affichage de la page *Speakers* quand `RequireConsistentDisplayMode` est défini sur `true` (sans la chaîne "(Mobile)" dans la barre de navigation, en haut).

![][AllSpeakers_LayoutMobileOverridden]

Vous pouvez désactiver le mode d'affichage cohérent dans une vue spécifique en définissant
`RequireConsistentDisplayMode` sur `false` dans le fichier de vue. Le balisage suivant dans le fichier *Views\\Home\\AllSpeakers.cshtml* définit
`RequireConsistentDisplayMode` sur `false`:

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All speakers";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = false;
    }

Dans cette section, nous avons vu comment créer des dispositions mobiles et des vues, ainsi que des dispositions et des vues pour des appareils spécifiques tels que l'iPhone.
Toutefois, le grand avantage de l'infrastructure Bootstrap CSS est la disposition réactive, c'est-à-dire qu'une simple feuille de style peut être appliquée aux navigateurs de bureau, de téléphone et de tablette pour créer une apparence homogène. Dans la section qui suit, vous allez apprendre à tirer parti de Bootstrap pour créer des vues adaptées aux appareils mobiles.

<h2><a name="bkmk_Improvespeakerslist"></a>Amélioration de la liste Intervenants</h2>

Comme vous venez de le voir, la vue *Speakers* est lisible, mais les liens sont petits et il est difficile de les sélectionner sur un appareil mobile. Dans cette section,vous allez adapter la vue *AllSpeakers* aux appareils mobiles, en affichant des liens avec une police plus grande, faciles à sélectionner et contenant une zone de recherche permettant de trouver rapidement des intervenants.

Vous pouvez utiliser le style [linked list group][] Bootstrap pour optimiser la vue *Speakers*. Dans *Views\\Home\\AllSpeakers.cshtml*,remplacez le contenu du fichier Razor par le code ci-dessous.

     @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker }, new { @class = "list-group-item" })
        }
    </div>

L'attribut `class="list-group"` dans la balise `<div>` applique le
style de liste Bootstrap et l'attribut `class="input-group-item"` applique le style d'élément de liste Bootstrap à chaque lien.

Actualisez le navigateur mobile. La vue actualisée ressemble à ceci :

![][AllSpeakersFixed]

Avec le style [linked list group][] Bootstrap, toute la zone de chaque lien est cliquable, pour une meilleure expérience utilisateur. Basculez vers l'affichage de bureau et observez l'homogénéité de l'apparence.

![][AllSpeakersFixedDesktop]

Bien que l'affichage du navigateur mobile ait été amélioré, il reste difficile de parcourir la longue liste d'intervenants. Bootstrap ne possède pas de fonction de filtre de recherche en natif, mais vous pouvez l'ajouter à l'aide de quelques lignes de code. Tout d'abord, vous devez ajouter une zone de recherche à l'affichage, puis du code JavaScript pour la fonction de filtre. Dans
*Views\\Home\\AllSpeakers.cshtml*, ajoutez une balise \<form\> juste après la balise \<h2\>, comme indiqué ci-dessous :

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <form class="input-group">
        <span class="input-group-addon"><span class="glyphicon glyphicon-search"></span></span>
        <input type="text" class="form-control" placeholder="Search speaker">
    </form>
    <br />
    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class = "list-group-item" })
        }
    </div>

Notez que les styles Bootstrap sont appliqués aux deux balises `<form>` et `<input>`. L'élément `<span>` ajoute un
[glyphicon][] Bootstrap à la zone de recherche.

Dans le dossier *Scripts*, ajoutez un fichier JavaScript intitulé *filter.js*. Ouvrez le fichier et collez-y le code suivant :

    $(function () {

        // reset the search form when the page loads
        $("form").each(function () {
            this.reset();
        });

        // wire up the events to the <input> element for search/filter
        $("input").bind("keyup change", function () {
            var searchtxt = this.value.toLowerCase();
            var items = $(".list-group-item");

            // show all speakers that begin with the typed text and hide others
            for (var i = 0; i < items.length; i++) {
                var val = items[i].text.toLowerCase();
                val = val.substring(0, searchtxt.length);
                if (val == searchtxt) {
                    $(items[i]).show();
                }
                else {
                    $(items[i]).hide();
                }
            }
        });
    });

Vous devez également inclure le fichier filter.js à vos lots enregistrés. Ouvrez le fichier
*App\_Start\\BundleConfig.cs* et modifiez les premiers lots. Modifiez la première instruction `bundles.Add` (pour le lot **jquery**) pour inclure
*Scripts\\filter.js*, comme suit :

     bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                "~/Scripts/jquery-{version}.js",
                "~/Scripts/filter.js"));

Le lot **jquery** est déjà rendu par l'affichage par défaut *\_Layout*. Plus tard, vous pourrez utiliser le même code JavaScript pour appliquer la fonction de filtre aux autres vues de liste.

Actualisez le navigateur mobile et accédez à la vue *AllSpeakers*. Dans la zone de recherche, entrez "sc". La liste des intervenants doit à présent être filtrée selon votre chaîne de recherche.

![][AllSpeakersFixedSearchBySC]

<h2><a name="bkmk_improvetags"></a>Amélioration de la liste Balises</h2>

À l'instar de la vue *Speakers*, la vue *Tags* est lisible, mais les liens sont affichés en petit et il est difficile de les sélectionner sur un appareil mobile. Vous pouvez optimiser la vue *Tags* de la même façon que vous l'avez fait avec la vue *Speakers*. Vous devez utiliser les modifications de code décrites plus haut mais en suivant la syntaxe de méthode `Html.ActionLink` dans *Views\\Home\\AllTags.cshtml*:

    @Html.ActionLink(tag, 
                     "SessionsByTag", 
                     new { tag }, 
                     new { @class = "list-group-item" })

Le navigateur de bureau actualisé se présente désormais ainsi :

![][AllTagsFixedDesktop]

Quant au navigateur mobile actualisé, il se présente ainsi : 

![][AllTagsFixed]

>[AZURE.NOTE] Si vous notez que le format de liste d'origine apparaît toujours dans le navigateur mobile, et si vous vous demandez ce que votre style Bootstrap est devenu, il s'agit d'un artefact de votre précédente action visant à créer des affichages mobiles spécifiques. Toutefois, maintenant que vous utilisez l'infrastructure CSS Bootstrap pour créer une conception Web réactive, passez à l'étape suivante et supprimez les vues et les dispositions spécifiques aux navigateurs mobiles. Ensuite, le navigateur mobile actualisé affichera le style Bootstrap.

<h2><a name="bkmk_improvedates"></a>Amélioration de la liste Dates</h2>

Vous pouvez améliorer la vue *Dates* de la même façon que vous avez amélioré les vues *Speakers* et
*Tags* si vous utilisez les modifications de code décrites précédemment, mais avec la syntaxe de méthode `Html.ActionLink` suivante dans  *Views\\Home\\AllDates.cshtml*:

    @Html.ActionLink(date.ToString("ddd, MMM dd, h:mm tt"), 
                     "SessionsByDate", 
                     new { date }, 
                     new { @class = "list-group-item" })

La vue du navigateur mobile actualisé se présente ainsi :

![][AllDatesFixed]

Vous pouvez encore optimiser la vue *Dates* en classant les valeurs date-heure par date. Pour ce faire, vous devez utiliser le style
[panels][] Bootstrap. Remplacez le contenu du fichier *Views\\Home\\AllDates.cshtml* par le code suivant :

    @model IEnumerable<DateTime>

    @{
        ViewBag.Title = "All Dates";
    }

    <h2>Dates</h2>

    @foreach (var dategroup in Model.GroupBy(x=>x.Date))
    {
        <div class="panel panel-primary">
            <div class="panel-heading">
                @dategroup.Key.ToString("ddd, MMM dd")
            </div>
            <div class="panel-body list-group">
                @foreach (var date in dategroup)
                {
                    @Html.ActionLink(date.ToString("h:mm tt"), 
                                     "SessionsByDate", 
                                     new { date }, 
                                     new { @class = "list-group-item" })
                }
            </div>
        </div>
    }

Ce code crée une balise `<div class="panel panel-primary">` distincte pour chaque date de la liste et utilise le style [linked list group][] pour les liens respectifs, comme précédemment. Voici à quoi le navigateur mobile ressemble lorsque ce code est exécuté :

![][AllDatesFixed2]

Basculez vers le navigateur de bureau. Remarquez l'aspect cohérent.

![][AllDatesFixed2Desktop]

<h2><a name="bkmk_improvesessionstable"></a>Amélioration de la vue SessionsTable</h2>

Dans cette section, vous allez adapter la vue *SessionsTable* aux appareils mobiles. Cette modification est plus importante que les précédentes.

Dans le navigateur mobile, appuyez sur le bouton **Balise**, puis saisissez `asp` dans le champ de recherche.

![][AllTagsFixedSearchByASP]

Appuyez sur le lien **ASP.NET**.

![][SessionsTableTagASP.NET]

Comme vous pouvez le constater, l'affichage se fait sous forme de tableau, actuellement conçu pour être affiché sur le navigateur de bureau. Toutefois, ce type d'affichage n'est pas adapté à un navigateur mobile. Pour remédier au problème, ouvrez le fichier
*Views\\Home\\SessionsTable.cshtml* et remplacez son contenu par le code suivant :

    @model IEnumerable<Mvc5Mobile.Models.Session>

    <h2>@ViewBag.Title</h2>

    <div class="container">
        <div class="row">
            @foreach (var session in Model)
            {
                <div class="col-md-4">
                    <div class="list-group">
                        @Html.ActionLink(session.Title, 
                                         "SessionByCode", 
                                         new { session.Code }, 
                                         new { @class="list-group-item active" })
                        <div class="list-group-item">
                            <div class="list-group-item-text">
                                @Html.Partial("_SpeakersLinks", session)
                            </div>
                            <div class="list-group-item-info">
                                @session.DateText
                            </div>
                            <div class="list-group-item-info small hidden-xs">
                                @Html.Partial("_TagsLinks", session)
                            </div>
                        </div>
                    </div>
                </div>
            }
        </div>
    </div>

Le code se compose de trois étapes :

-   il utilise le style [custom linked list group][] Boostrap pour mettre en forme les informations de session de façon verticale. Ainsi, toutes ces informations sont lisibles dans un navigateur mobile (à l'aide de classes telles que list-group-item-text)
-   il applique le [système de grille][] à la disposition, afin que les éléments de session s'affichent à l'horizontale dans le navigateur de bureau et à la verticale dans le navigateur mobile (à l'aide de la classe col-md-4)
-   il utilise les [utilitaires réactifs][] pour masquer les balises de session quand elles s'affichent dans le navigateur mobile (à l'aide de la classe hidden-xs)

Vous pouvez aussi appuyer sur le lien d'un titre pour accéder à la session associée. L'image qui suit reflète les changements réalisés à l'aide du code.

![][FixedSessionsByTag]

Le système de grille Bootstrap que vous avez automatiquement appliqué permet d'organiser les sessions à la verticale dans le navigateur mobile. En outre, notez que les balises ne s'affichent pas. Basculez vers le navigateur de bureau.

![][SessionsTableFixedTagASP.NETDesktop]

Dans le navigateur de bureau, vous remarquez que les balises s'affichent désormais. De même, vous pouvez constater que le système de grille Bootstrap que vous avez appliqué classe les éléments de session en deux colonnes. Si vous agrandissez le navigateur, l'organisation s'étend sur trois colonnes.

<h2><a name="bkmk_improvesessionbycode"></a>Amélioration de la vue SessionByCode</h2>

Enfin, vous allez optimiser la vue *SessionByCode* pour l'adapter aux appareils mobiles.

Dans le navigateur mobile, appuyez sur le bouton **Balise**, puis saisissez `asp` dans le champ de recherche.

![][AllTagsFixedSearchByASP]

Appuyez sur le lien **ASP.NET**. Les sessions de balise ASP.NET s'affichent.

![][FixedSessionsByTag]

Choisissez le lien **Conception d'une application à page unique avec ASP.NET et
AngularJS**.

![][SessionByCode3-644]

Si la vue bureau par défaut convient tout à fait, vous pouvez facilement améliorer son aspect à l'aide des composants d'interface graphique Bootstrap.

Ouvrez le fichier *Views\\Home\\SessionByCode.cshtml* et remplacez le contenu parle balisage suivant :

    @model Mvc5Mobile.Models.Session

    @{
        ViewBag.Title = "Session details";
    }
    <h3>@Model.Title (@Model.Code)</h3>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Speakers
        </div>
        @foreach (var speaker in Model.Speakers)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class="panel-body" })
        }
    </div>

    <p>@Model.Abstract</p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Tags
        </div>
        @foreach (var tag in Model.Tags)
        {
            @Html.ActionLink(tag, 
                             "SessionsByTag", 
                             new { tag }, 
                             new { @class = "panel-body" })
        }
    </div>

Le nouveau balisage utilise le style de volets Bootstrap pour optimiser la vue mobile. 

Actualisez le navigateur mobile. L'image suivante reflète les changements que vous venez de réaliser à l'aide du code :

![][SessionByCodeFixed3-644]

Conclusion
------------------

Ce didacticiel vous a guidé dans l'utilisation d'ASP.NET MVC 5 pour développer des applications Web adaptées aux appareils mobiles. Par exemple :

-	Déployer une application ASP.NET MVC 5 dans un site Web Microsoft Azure
-   Utiliser Bootstrap pour créer une disposition Web réactive dans votre application MVC 5
-   Remplacer la disposition, les vues, les vues partielles, de façon globale ou pour une vue spécifique
-   Contrôler la disposition et l'application de remplacement partiel à l'aide de la propriété `RequireConsistentDisplayMode`
-   Créer des vues spécialement adaptées aux navigateurs, comme le navigateur de l'iPhone
-   Appliquer le style Bootstrap dans du code Razor

Voir aussi
--------

-   [9 principes de base de la conception Web réactive](http://blog.froont.com/9-basic-principles-of-responsive-web-design/)
-   [Bootstrap][BootstrapSite]
-   [Blog Bootstrap officiel][]
-   [Tutoriel Bootstrap Twitter de Tutorial Republic][]
-   [The Bootstrap Playground][]
-   [Bonnes pratiques pour les applications Web mobiles des recommandations W3C][]
-   [W3C Candidate Recommendation des requêtes Média][]

<!-- Internal Links -->
[Déploiement du projet de départ sur un site Web Microsoft Azure]: #bkmk_DeployStarterProject
[Infrastructure CSS Bootstrap]: #bkmk_bootstrap
[Remplacement des vues, des dispositions et des vues partielles]: #bkmk_overrideviews
[Création d'affichages mobiles]:#bkmk_browserviews
[Amélioration de la liste Intervenants]: #bkmk_Improvespeakerslist
[Amélioration de la liste Balises]: #bkmk_improvetags
[Amélioration de la liste Dates]: #bkmk_improvedates
[Amélioration de la vue SessionsTable]: #bkmk_improvesessionstable
[Amélioration de la vue SessionByCode]: #bkmk_improvesessionbycode

<!-- External Links -->
[Visual Studio Express 2013]: http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-web
[AzureSDKVs2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
[Fiddler]: http://www.fiddler2.com/fiddler2/
[EmulatorIE11]: http://msdn.microsoft.com/fr-fr/library/ie/dn255001.aspx
[EmulatorChrome]: https://developers.google.com/chrome-developer-tools/docs/mobile-emulation
[EmulatorOpera]: http://www.opera.com/developer/tools/mobile/
[StarterProject]: http://go.microsoft.com/fwlink/?LinkID=398780&clcid=0x409
[CompletedProject]: http://go.microsoft.com/fwlink/?LinkID=398781&clcid=0x409
[BootstrapSite]: http://getbootstrap.com/
[WebPIAzureSdk23NetVS13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk23NetVS13.png
[linked list group]: http://getbootstrap.com/components/#list-group-linked
[glyphicon]: http://getbootstrap.com/components/#glyphicons
[panels]: http://getbootstrap.com/components/#panels
[custom linked list group]: http://getbootstrap.com/components/#list-group-custom-content
[système de grille]: http://getbootstrap.com/css/#grid
[utilitaires réactifs]: http://getbootstrap.com/css/#responsive-utilities
[Blog Bootstrap officiel]: http://blog.getbootstrap.com/
[Tutoriel Bootstrap Twitter de Tutorial Republic]: http://www.tutorialrepublic.com/twitter-bootstrap-tutorial/
[The Bootstrap Playground]: http://www.bootply.com/
[Bonnes pratiques pour les applications Web mobiles des recommandations W3C]: http://www.w3.org/TR/mwabp/
[W3C Candidate Recommendation des requêtes Média]: http://www.w3.org/TR/css3-mediaqueries/

<!-- Images -->
[DeployClickPublish]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-1.png
[DeployClickWebSites]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-2.png
[DeploySignIn]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-3.png
[DeployUsername]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-4.png
[DeployPassword]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-5.png
[DeployNewWebsite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-6.png
[DeploySiteSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7.png
[DeployPublishSite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-8.png
[MobileHomePage]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/mobile-home-page.png
[FixedSessionsByTag]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-Fixed.png
[AllTags]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags.png
[SessionsByTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET.png
[SessionsByTagASP.NETNoBootstrap]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-NoBootstrap.png
[AllTagsMobile_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile.png
[AllTagsMobile_LayoutMobileDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile-Desktop.png
[ResolveDefaultDisplayMode]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Resolve-DefaultDisplayMode.png
[AllTagsIPhone_LayoutIPhone]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsIPhone-_LayoutIPhone.png
[AllSpeakers_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile.png
[AllSpeakers_LayoutMobileOverridden]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile-Overridden.png
[AllSpeakersFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed.png
[AllSpeakersFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-Desktop.png
[AllSpeakersFixedSearchBySC]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-SearchBySC.png
[AllTagsFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-Desktop.png 
[AllTagsFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed.png
[AllDatesFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed.png
[AllDatesFixed2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2.png
[AllDatesFixed2Desktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2-Desktop.png
[AllTagsFixedSearchByASP]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-SearchByASP.png
[SessionsTableTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Tag-ASP.NET.png
[SessionsTableFixedTagASP.NETDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Fixed-Tag-ASP.NET-Desktop.png
[SessionByCode3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-3-644.png
[SessionByCodeFixed3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-Fixed-3-644.png


<!--HONumber=42-->
