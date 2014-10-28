<properties linkid="develop-dotnet-aspnet-mvc-4-mobile-website" urlDisplayName="ASP.NET MVC 5 mobile website" pageTitle=".NET ASP.NET MVC 5 mobile website - Azure tutorials" metaKeywords="Azure tutorial, Azure web app tutorial, Azure mobile app, Azure ASP.NET MVC 5,,ASP.NET MVC" description="A tutorial that teaches you how to deploy a web application to an Azure website using mobile features in ASP.NET MVC 5 web application." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Deploy an ASP.NET MVC Mobile Web Application on Azure Websites" authors="cephalin,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="cephalin,riande"></tags>

# Déploiement d'une application Web mobile ASP.NET MVC 5 sur les sites Web Azure

Ce didacticiel aborde les bases de la conception d'une application Web
ASP.NET MVC 5 adaptée aux périphériques mobiles et son déploiement au sein de Microsoft Azure. Pour ce didacticiel, vous avez besoin de
[Visual Studio Express 2013 pour le Web][]
ou de l'édition professionnelle de
Visual Studio si vous l'avez déjà.

[WACOM.INCLUDE [create-account-and-websites-note][]]

### Contenu

Dans ce didacticiel, vous ajouterez des fonctionnalités
mobiles à l'application simple de listes de conférence fournie dans le [projet de départ][]. La capture d'écran suivante illustre les sessions ASP.NET dans l'application
terminée telles qu'elles s'affichent dans l'émulateur de navigateur
des outils destinés aux développeur F12 d'Internet Explorer 11.

![][]

Vous pouvez utiliser les outils destinés aux développeurs F12 d'Internet Explorer 11 et l'[outil
Fiddler][]
pour vous aider à déboguer votre application. 

### Compétences

Vous apprendrez les compétences suivantes :

-   Comment utiliser Visual Studio 2013 pour publier directement votre application Web sur un site Web Microsoft Azure.
-   Comment les modèles ASP.NET MVC 5 utilisent l'infrastructure Bootstrap CSS
    pour optimiser l'affichage sur les périphériques mobiles
-   Comment créer des affichages adaptés aux périphériques mobiles et personnalisés selon le navigateur
    mobile, comme ceux de l'iPhone ou des téléphones Android
-   Comment créer des affichages réactifs (qui fonctionnent sous différents
    navigateurs et périphériques)

## Configuration de l’environnement de développement

Configurez l'environnement de développement en installant le Kit de développement logiciel (SDK) Azure pour .NET Framework.

1.  Pour installer le Kit de développement logiciel (SDK) Azure pour .NET, cliquez sur le lien ci-dessous. Si Visual Studio 2013 n'est pas encore installé, il le sera par l'intermédiaire de ce lien. Ce didacticiel nécessite Visual Studio 2013. [Kit de développement logiciel (SDK) Azure pour Visual Studio 2013][]
2.  Dans la fenêtre Web Platform Installer, cliquez sur **Installer**, puis poursuivez l’installation.

    ![Web Platform Installer - Kit de développement logiciel (SDK) Azure pour .NET][]

Vous aurez également besoin d'un émulateur de navigateur mobile. Vous pouvez utiliser l'un
des émulateurs suivants :

-   Émulateur de navigateur dans les [outils destinés aux développeur F12 d'Internet Explorer 11][]
    (utilisé dans toutes les captures d'écran de navigateur mobile). Il dispose de présélections de chaîne d'agent utilisateur pour Windows Phone 8, Windows Phone 7 et l'iPad d'Apple.
-   Émulateur de navigateur de [Google Chrome DevTools][]. Il inclut des présélections pour de nombreux périphériques Android, ainsi que pour l'iPhone d'Apple, l'iPad d'Apple et le Kindle Fire d'Amazon. Il émule également des événements tactiles.
-   [Émulateur mobile Opera][]

Les projets Visual Studio avec code source C# sont disponibles pour cette
rubrique :

-   [Téléchargement du projet de départ][projet de départ]
-   [Téléchargement du projet terminé][]

## Étapes de ce didacticiel

-   [Déploiement du projet de départ sur un site Web Microsoft Azure][]
-   [Infrastructure CSS Bootstrap][]
-   [Remplacement des vues, des dispositions et des vues partielles][]
-   [Amélioration de la liste Intervenants][]
-   [Amélioration de la liste Balises][]
-   [Amélioration de la liste Dates][]
-   [Amélioration de la vue SessionsTable][]
-   [Amélioration de la vue SessionByCode][]

### <a name="bkmk_DeployStarterProject"></a>Déploiement du projet de départ sur un site Web Microsoft Azure

1.  Téléchargez le [projet de départ][] de l'application de listes de conférence.

2.  Dans l'Explorateur Windows, cliquez ensuite avec le bouton droit sur le fichier Mvc5Mobile.zip et sélectionnez *Propriétés*.

3.  Dans la boîte de dialogue **Propriétés de Mvc5Mobile.zip**,
    cliquez sur le bouton **Débloquer**. Le déblocage empêche l'affichage d'un avertissement
    de sécurité, qui apparaît lorsque vous essayez d'utiliser
    un fichier *.zip* que vous avez téléchargé à partir d'Internet.

4.  Cliquez avec le bouton droit sur le fichier *Mvc5Mobile.zip* puis sélectionnez **Extraire tout** pour
    décompresser l'archive.

5.  Dans Visual Studio, ouvrez le fichier *Mvc5Mobile.sln*.

6.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis cliquez sur **Publier**.

    ![][1]

7.  Dans Publier le site Web, cliquez sur **Sites Web Microsoft Azure**.

    ![][2]

8.  Cliquez sur **Se connecter**.

    ![][3]

9.  Saisissez votre nom d'utilisateur Microsoft Azure et cliquez sur **Continuer**.

    ![][4]

10. Saisissez votre mot de passe et cliquez sur **Se connecter**.

    ![][5]

11. La boîte de dialogue Sélectionner un site Web existant devrait à présent indiquer que vous êtes connecté. Cliquez sur **Nouveau**.

    ![][6]

12. Dans le champ **Nom du site**, indiquez le préfixe d'un nom de site unique. Le nom complet de votre site sera sous la forme *\<préfixe\>*.azurewebsites.net. De plus, dans le champ **Région**, sélectionnez une région. Cliquez sur **Créer**.

    ![][7]

13. La boîte de dialogue Publier le site Web est renseignée avec les paramètres de site correspondant à votre nouveau site Web. Cliquez sur **Publier**.

    ![][8]

    Lorsque Visual Studio termine la publication du projet de départ sur le site Web Microsoft Azure, le navigateur de bureau s'ouvre pour afficher le site Web actif.

14. Lancez l'émulateur de navigateur mobile, copiez l'URL de
    l'application de conférence (*<prefix>*.azurewebsites.net) dans l'émulateur, puis cliquez sur
    le bouton en haut à droite et sélectionnez **Parcourir par balise**. Si Internet
    Explorer 11 est votre navigateur par défaut, il vous suffit d'appuyer sur la touche `F12` puis sur les touches
    `Ctrl+8`, et de basculer le profil du navigateur sur **Windows Phone**. L'image
    suivante illustre la vue *AllTags* en mode portrait (une fois que l'option
    **Parcourir par balise** a été sélectionnée).

    ![][9]

> [WACOM.NOTE] De même que vous pouvez déboguer votre application MVC 5 au sein de Visual Studio, vous pouvez republier votre site Web sur Microsoft Azure pour consulter le site actif directement à partir de votre navigateur mobile ou d'un émulateur de navigateur.

L'affichage est tout à fait lisible sur un appareil mobile. Vous pouvez également
déjà voir certains effets visuels appliqués par l'infrastructure CSS Bootstrap.
Cliquez sur le lien **ASP.NET**.

![][10]

La vue avec balises ASP.NET s'adapte à l'écran, ce que Bootstrap effectue
pour vous automatiquement. Cependant, vous pouvez optimiser cette vue pour
une meilleure adaptation au navigateur mobile. Par exemple, la colonne **Date** est n'est pas très
lisible. Plus loin dans ce didacticiel, vous modifierez la vue *AllTags* pour l'adapter
aux périphériques mobiles.

## <a name="bkmk_bootstrap"></a>Infrastructure CSS Bootstrap

La prise en charge de Bootstrap intégrée est une nouveauté du modèle MVC 5. Vous avez
déjà vu comment cette prise en charge améliore les différentes vues
dans votre application. Par exemple, la barre de navigateur dans la partie supérieure
se réduit automatiquement lorsque la largeur du navigateur est inférieure. Sur le navigateur de bureau,
essayez de redimensionner la fenêtre du navigateur et regardez le comportement
de la barre de navigation. Il s'agit de la conception de sites Web réactive intégrée à
Bootstrap.

Pour voir quelle serait l'apparence de l'application Web sans Bootstrap,
ouvrez le fichier *App\_Start\\BundleConfig.cs* et placez les
lignes qui contiennent *bootstrap.js* et *bootstrap.css* en commentaires. Le code suivant indique les deux
dernières instructions de la méthode `RegisterBundles` après la modification :

     bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
              //"~/Scripts/bootstrap.js",
              "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css").Include(
              //"~/Content/bootstrap.css",
              "~/Content/site.css"));

Appuyez sur `Ctrl+F5` pour exécuter l'application.

Vous pouvez remarquer que la barre de navigateur réductible n'est
maintenant qu'une simple liste non triée classique. Cliquez à nouveau sur **Parcourir par balise**. Cliquez ensuite sur **ASP.NET**.
Dans la vue de l'émulateur mobile, vous pouvez
à présent constater qu'elle n'est plus adaptée à l'écran et que vous
devez faire défiler la page horizontalement afin de voir le côté droit du tableau.

![][11]

Annulez vos modifications et actualisez le navigateur mobile
pour vérifier que l'affichage adapté aux périphériques mobiles a été rétabli.

Bootstrap n'est pas spécifique à ASP.NET MVC 5 et vous pouvez tirer parti
de ces fonctionnalités dans n'importe quelle application Web. Désormais, Bootstrap
est intégré au modèle de projet ASP.NET MVC 5. Votre application
Web MVC 5 peut donc tirer parti de Bootstrap par défaut.

Pour plus d'informations sur Bootstrap, rendez-vous sur le
site de [Bootstrap][].

Cette section vous présente comment fournir des affichages spécialement adaptés aux
périphériques mobiles.

## <a name="bkmk_overrideviews"></a>Remplacement des vues, des dispositions et des vues partielles

Vous pouvez remplacer toutes les vues (y compris les dispositions et
les vues partielles) des navigateurs mobiles en général, mais aussi d'un
navigateur mobile particulier ou encore d'un navigateur spécifique. Pour fournir un affichage mobile, vous pouvez copier un fichier
de vue et ajouter *.Mobile* au nom du fichier. Par exemple, pour créer un affichage
mobile *Index*, vous pouvez copier *Views\\Home\\Index.cshtml* vers
*Views\\Home\\Index.Mobile.cshtml*.

Dans cette section, vous allez créer un fichier de disposition mobile.

Tout d'abord, copiez *Views\\Shared\\\_Layout.cshtml* vers
*Views\\Shared\\\_Layout.Mobile.cshtml*. Ouvrez *\_Layout.Mobile.cshtml*
et remplacez le titre **MVC5 Application** par **MVC5 Application
(Mobile)**.

Dans chaque appel `Html.ActionLink` pour la barre de navigation, supprimez « Parcourir par »
de chaque lien*ActionLink*. Le code suivant affiche la balise `<ul class="nav navbar-nav">` terminée d'un fichier de disposition mobile.

    <ul class="nav navbar-nav">
        <li>@Html.ActionLink("Home", "Index", "Home")</li>
        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
    </ul>

Copiez le fichier *Views\\Home\\AllTags.cshtml* vers
*Views\\Home\\AllTags.Mobile.cshtml*. Ouvrez le nouveau fichier et, pour l'élément
`<h2>`, remplacez « Tags » par « Tags (M) » :

    <h2>Tags (M)</h2>

Accédez à la page des balises à l'aide d'un navigateur de bureau et de l'émulateur
de navigateur mobile. L'émulateur de navigateur mobile affiche les deux modifications que
vous avez effectuées (le titre de *\_Layout.Mobile.cshtml*
et le titre de *AllTags.Mobile.cshtml*).

![][12]

Par contre, la vue de bureau n'a pas changé (avec des titres de *\_Layout.cshtml* et de
*AllTags.cshtml*).

![][13]

## <a name="bkmk_browserviews"></a>Création d'affichages mobiles

Outre les affichages mobiles et de bureau, vous pouvez créer des affichages
pour un navigateur en particulier. Par exemple, il est possible de
créer des affichages spécialement adaptés aux navigateurs de l'iPhone et sous Android. Dans cette section,
vous créerez une disposition pour le navigateur de l'iPhone et une version
 pour iPhone de l'affichage *AllTags*.

Ouvrez le fichier *Global.asax* et ajoutez le code suivant comme dernière ligne
de la méthode`Application_Start`.

    DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone")
    {
        ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf
            ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0)
    });

Ce code définit un nouveau mode d'affichage appelé « iPhone » qui répondra
à chaque demande entrante. Si la demande entrante correspond à
la condition que vous avez définie (c'est-à-dire si l'agent utilisateur
contient la chaîne « iPhone »), ASP.NET MVC recherche des
affichages dont les noms contiennent le suffixe « iPhone ».

> [WACOM.NOTE] Lors de l'ajout de modes d'affichage de navigateur mobile, pour iPhone et Android par exemple, assurez-vous de définir le premier argument sur `0` (insérez-le en haut de la liste) pour que le mode navigateur ait la priorité sur le modèle mobile (\*.Mobile.cshtml). Si le modèle mobile est en haut de la liste, il est sélectionné à la place du mode d'affichage souhaité (le premier résultat est prioritaire et le modèle mobile correspond à tous les navigateurs mobiles).

Dans le code, cliquez avec le bouton droit sur `DefaultDisplayMode`, sélectionnez **Resolve**
puis `using System.Web.WebPages;`. Cette action permet d'ajouter
une référence à l'espace de noms`System.Web.WebPages`,
qui se situe là où les types `DisplayModeProvider` et `DefaultDisplayMode` sont définis.

![][14]

Vous pouvez également ajouter manuellement la ligne suivante à
la section `using` du fichier.

    using System.Web.WebPages;

Enregistrez les modifications. Copiez le fichier
*Views\\Shared\\\_Layout.Mobile.cshtml* dans
*Views\\Shared\\\_Layout.iPhone.cshtml*. Ouvrez le nouveau
fichier et remplacez le titre `MVC5 Application (Mobile)` par
`MVC5 Application (iPhone)`.

Copiez le fichier *Views\\Home\\AllTags.Mobile.cshtml* dans
*Views\\Home\\AllTags.iPhone.cshtml*. Dans le nouveau fichier,
pour l'élément `<h2>`, remplacez « Tags (M) » par « Tags (iPhone) ».

Exécutez l'application. Lancez un émulateur de navigateur mobile, vérifiez que
son agent utilisateur est défini sur « iPhone » et parcourez la vue *AllTags*. Si vous utilisez
l'émulateur dans les outils destinés aux développeur F12 d'Internet Explorer 11,
configurez l'émulation comme suit :

-   Profil du navigateur = **Windows Phone**
-   Chaîne d'agent utilisateur = **Custom**
-   Chaîne personnalisée = **Apple-iPhone5C1/1001.525**

La capture d'écran qui suit présente la vue *AllTags* affichée dans
l'émulateur des outils destinés aux développeurs F12 d'Internet Explorer 11, avec la chaîne d'agent utilisateur personnalisée (il s'agit d'une chaîne d'agent utilisateur d'un iPhone 5C).

![][15]

Dans le navigateur mobile, sélectionnez le lien **Intervenants**. En
l'absence d'affichage mobile (*AllSpeakers.Mobile.cshtml*), la vue par défaut des intervenants
(*AllSpeakers.cshtml*) est affichée à l'aide du mode de disposition mobile
(*\_Layout.Mobile.cshtml*). Comme indiqué ci-dessous, le titre **MVC5 Application
(Mobile)** est défini dans *\_Layout.Mobile.cshtml*.

![][16]

Vous pouvez globalement désactiver l'affichage d'une vue par défaut (non mobile)
au sein d'une disposition mobile en définissant `RequireConsistentDisplayMode` sur
`true` dans le fichier *Views\\\_ViewStart.cshtml*, comme indiqué ci-dessous :

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = true;
    }

Lorsque `RequireConsistentDisplayMode` est défini sur `true`, la disposition mobile
(*\_Layout.Mobile.cshtml*) est utilisée uniquement pour les affichages
mobiles (lorsque le fichier de vue se présente sous la forme ***NomVue**.Mobile.cshtml* par exemple). Vous pouvez définir
`RequireConsistentDisplayMode` sur `true` si votre disposition mobile
ne fonctionne pas correctement avec les affichages non mobiles. La capture d'écran suivante
indique comment la page *Intervenants* s'affiche lorsque `RequireConsistentDisplayMode` est
défini sur `true` (sans la chaîne « (Mobile) » dans la barre de navigation, dans la partie supérieure).

![][17]

Vous pouvez désactiver le mode d'affichage cohérent dans une vue spécifique en définissant
`RequireConsistentDisplayMode` sur `false` dans le fichier de vue. Le
balisage suivant du fichier *Views\\Home\\AllSpeakers.cshtml* définit
`RequireConsistentDisplayMode` sur `false` :

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All speakers";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = false;
    }

Dans cette section, nous avons vu comment créer des dispositions et des
affichages mobiles, ainsi que comment créer des dispositions et des
affichages pour des périphériques spécifiques comme l'iPhone. Cependant,
le principal avantage de l'infrastructure CSS Bootstrap est la
disposition réactive : il est possible d'appliquer une seule feuille de style pour tous
les navigateurs de bureau, de téléphone et de tablette afin d'obtenir un aspect cohérent. Cette section vous présente comment tirer parti de Bootstrap et créer
des affichages adaptés aux périphériques mobiles.

## <a name="bkmk_Improvespeakerslist"></a> Amélioration de la liste Intervenants

Comme vous venez de le voir, l'affichage *Intervenants* est lisible, mais
les liens sont petits et il est difficile de les sélectionner sur un périphérique mobile. Dans cette section,
vous allez adapter l'affichage *AllSpeakers* aux périphériques mobiles. Les
liens seront plus grands, faciles à sélectionner
et contiendront une zone de recherche pour trouver des intervenants rapidement.

Vous pouvez utiliser le style [linked list group][] Bootstrap pour
optimiser la vue *Intervenants*. Dans *Views\\Home\\AllSpeakers.cshtml*,
remplacez le contenu du fichier Razor par le code ci-dessous.

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

L'attribut `class="list-group"` de la balise `<div>` s'applique au style de liste
Bootstrap et l'attribut `class="input-group-item"`
s'applique au style de l'élément de liste Bootstrap pour chaque lien.

Actualisez le navigateur mobile. L'affichage actualisé ressemble à ceci :

![][18]

Avec le style [linked list group][] Bootstrap, toute la zone de chaque
lien est cliquable, ce qui confère un confort d'utilisation optimal. Basculez vers l'affichage
de bureau et jetez un œil à cet aspect cohérent.

![][19]

Certes, l'affichage du navigateur mobile a été amélioré, mais il est tout de même difficile
de parcourir la longue liste d'intervenants. Bootstrap est dépourvu d'une
fonction de filtre de recherche en natif. Il est possible d'en ajouter une à l'aide de
quelques lignes de code. Tout d'abord, vous devez ajouter un champ de recherche à l'affichage, puis
connecter du code JavaScript pour la fonction de filtre. Dans
*Views\\Home\\AllSpeakers.cshtml*, ajoutez une balise \<form\> juste après la balise \<h2\> , comme indiqué ci-dessous :

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

Remarque : les styles Bootstrap ont été appliqués aux balises `<form>` et
`<input>`. L'élément `<span>` ajoute un
[glyphicon][] Bootstrap au champ
de recherche.

Dans le dossier *Scripts*, ajoutez un fichier JavaScript intitulé *filter.js*. Ouvrez
le fichier et collez-y le code suivant :

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
*App\_Start\\BundleConfig.cs* et modifiez les premiers lots. Modifiez la
première instruction `bundles.Add` (pour le lot **jquery**) de manière à inclure le fichier
*Scripts\\filter.js*, comme décrit ci-dessous :

     bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                "~/Scripts/jquery-{version}.js",
                "~/Scripts/filter.js"));

Le lot **jquery** est déjà rendu par l'affichage par défaut *\_Layout*
. Plus tard, vous pourrez utiliser le même code JavaScript pour appliquer
la fonction de filtre aux autres vues de liste.

Actualisez le navigateur mobile et accédez à l'affichage *AllSpeakers*. Dans le champ
de recherche, entrez « sc ». La liste des intervenants devrait à présent être filtrée
selon vos critères de recherche.

![][20]

## <a name="bkmk_improvetags"></a> Amélioration de la liste Balises

À l'instar de la vue *Intervenants*, la vue *Balises* est lisible, mais les
liens sont petits et il est difficile de les sélectionner sur un périphérique mobile. Vous pouvez optimiser la vue *Balises* de la même façon que vous l'avez fait avec la vue *Intervenants*. Vous devez utiliser les modifications de code décrites plus haut mais en suivant la syntaxe de méthode `Html.ActionLink` dans *Views\\Home\\AllTags.cshtml* :

    @Html.ActionLink(tag, 
                     "SessionsByTag", 
                     new { tag }, 
                     new { @class = "list-group-item" })

Le navigateur de bureau actualisé se présente désormais ainsi :

![][21]

Quant au navigateur mobile actualisé, il se présente ainsi :

![][22]

> [WACOM.NOTE] Si vous remarquez que le format de liste d'origine apparaît toujours dans le navigateur mobile, et si vous vous demandez ce que votre style Bootstrap est devenu, il s'agit d'un artefact de votre précédente action visant à créer des vues spécifiques aux navigateurs mobiles. Toutefois, maintenant que vous utilisez l'infrastructure CSS Bootstrap pour créer une conception Web réactive, passez à l'étape suivante et supprimez les vues et les dispositions spécifiques aux navigateurs mobiles. Ensuite, le navigateur mobile actualisé affichera le style Bootstrap.

## <a name="bkmk_improvedates"></a> Amélioration de la liste Dates

Vous pouvez optimiser la vue *Dates* de la même façon que vous l'avez fait avec les vues *Intervenants* et
*Balises*. Vous devez utiliser les modifications de code décrites plus haut mais en suivant la syntaxe de méthode suivante `Html.ActionLink` dans *Views\\Home\\AllDates.cshtml* :

    @Html.ActionLink(date.ToString("ddd, MMM dd, h:mm tt"), 
                     "SessionsByDate", 
                     new { date }, 
                     new { @class = "list-group-item" })

La vue du navigateur mobile actualisé se présente ainsi :

![][23]

Vous pouvez encore optimiser la vue *Dates*. Il vous suffit de classer les valeurs date-heure
par date. Vous devez utiliser le style
[panels][] Bootstrap. Remplacez
le contenu du fichier *Views\\Home\\AllDates.cshtm* par le
code suivant :

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

Ce code crée une balise `<div class="panel panel-primary">` distincte pour
chaque date de la liste et utilise le style [linked list group][] pour les
liens respectifs, comme avant. Voici à quoi le navigateur mobile ressemble
lorsque ce code est exécuté :

![][24]

Basculez vers le navigateur de bureau. Remarquez l'aspect cohérent.

![][25]

## <a name="bkmk_improvesessionstable"></a> Amélioration de la vue SessionsTable

Dans cette section, la vue *SessionsTable* va gagner
en convivialité mobile. Cette modification est plus importante que les précédentes.

Dans le navigateur mobile, appuyez sur le bouton **Balise**, puis saisissez `asp` dans le
champ de recherche.

![][26]

Appuyez sur le lien **ASP.NET**.

![][27]

Comme vous pouvez le constater, l'affichage se fait sous la forme d'un tableau, actuellement
conçu pour être vu sur le navigateur de bureau. Malheureusement, ce type
d'affichage n'est pas adapté à un navigateur mobile. Pour remédier au problème, ouvrez le fichier
*Views\\Home\\SessionsTable.cshtml* puis remplacez le contenu du
fichier par le code suivant :

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

Le code se compose de trois étapes :

-   il utilise le style [custom linked list group][]
    Bootstrap pour mettre en forme les informations de session de façon verticale. Ainsi, toutes ces
    informations sont lisibles sur un navigateur mobile (à l'aide de classes telles que list-group-item-text)
-   il applique le style [grid system][] à la
    disposition, afin que les éléments de session s'affichent à l'horizontale dans le navigateur
    de bureau et à la verticale dans le navigateur mobile (à l'aide de la classe col-md-4)
-   il utilise les [utilitaires réactifs][] pour
    masquer les balises de session lorsqu'elles s'affichent dans le navigateur mobile (à l'aide de la classe hidden-xs)

Vous pouvez aussi appuyer sur le lien d'un titre pour accéder à la session associée. L'image
qui suit reflète les changements réalisés à l'aide du code.

![][]

Le système de grille Bootstrap que vous avez automatiquement appliqué permet de classer les
sessions à la verticale dans le navigateur mobile. En outre, vous remarquerez que les balises
ne s'affichent pas. Basculez vers le navigateur de bureau.

![][28]

Dans le navigateur de bureau, vous remarquez que les balises s'affichent désormais. De même, vous pouvez constater que le système de grille Bootstrap que
vous avez appliqué classe les éléments de session en deux colonnes. Si vous agrandissez le
navigateur, l'organisation s'étend sur trois colonnes.

## <a name="bkmk_improvesessionbycode"></a> Amélioration de la vue SessionByCode

Dernière étape, vous allez optimiser la vue *SessionByCode* pour l'adapter aux périphériques mobiles.

Dans le navigateur mobile, appuyez sur le bouton **Balise**, puis saisissez `asp` dans le
champ de recherche.

![][26]

Appuyez sur le lien **ASP.NET**. Les sessions de balise ASP.NET s'affichent.

![][]

Choisissez le lien **Conception d'une application à page unique avec ASP.NET et
AngularJS**.

![][29]

Si la vue bureau par défaut convient tout à fait, vous pouvez facilement améliorer son aspect à l'aide des composants d'interface graphique Bootstrap.

Ouvrez le fichier *Views\\Home\\SessionByCode.cshtml* et remplacez le contenu par
le balisage suivant :

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

Actualisez le navigateur mobile. L'image suivante reflète les changements
que vous venez de réaliser à l'aide du code :

![][30]

## Conclusion

Ce didacticiel vous a guidé dans l'utilisation d'ASP.NET MVC 5 pour développer des applications
Web adaptées aux périphériques mobiles. Vous avez notamment vu les points suivants :

-   Déployer une application ASP.NET MVC 5 dans un site Web Microsoft Azure
-   Utiliser Bootstrap pour créer une disposition Web réactive au sein de votre application
    MVC 5
-   Remplacer la disposition, les vues, les vues partielles, de façon globale ou
    pour une vue spécifique
-   Contrôler la disposition et l'application de remplacement partiel à l'aide de la propriété
    `RequireConsistentDisplayMode`
-   Créer des vues spécialement adaptées aux navigateurs, comme le navigateur
    de l'iPhone
-   Appliquer le style Bootstrap dans du code Razor

## Voir aussi

-   Site [Bootstrap][]
-   [Blog Bootstrap officiel][]
-   [Tutoriel Bootstrap Twitter de Tutorial Republic][]
-   [The Bootstrap Playground][]
-   [Bonnes pratiques pour les applications Web mobiles des recommandations W3C][]
-   [W3C Candidate Recommendation des requêtes Média][]

<!-- Internal Links -->
<!-- External Links -->
<!-- Images -->

  [Visual Studio Express 2013 pour le Web]: http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-web
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [projet de départ]: http://go.microsoft.com/fwlink/?LinkID=398780&clcid=0x409
  []: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-Fixed.png
  [outil Fiddler]: http://www.fiddler2.com/fiddler2/
  [Kit de développement logiciel (SDK) Azure pour Visual Studio 2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
  [Web Platform Installer - Kit de développement logiciel (SDK) Azure pour .NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk23NetVS13.png
  [outils destinés aux développeur F12 d'Internet Explorer 11]: http://msdn.microsoft.com/fr-fr/library/ie/dn255001.aspx
  [Google Chrome DevTools]: https://developers.google.com/chrome-developer-tools/docs/mobile-emulation
  [Émulateur mobile Opera]: http://www.opera.com/developer/tools/mobile/
  [Téléchargement du projet terminé]: http://go.microsoft.com/fwlink/?LinkID=398781&clcid=0x409
  [Déploiement du projet de départ sur un site Web Microsoft Azure]: #bkmk_DeployStarterProject
  [Infrastructure CSS Bootstrap]: #bkmk_bootstrap
  [Remplacement des vues, des dispositions et des vues partielles]: #bkmk_overrideviews
  [Amélioration de la liste Intervenants]: #bkmk_Improvespeakerslist
  [Amélioration de la liste Balises]: #bkmk_improvetags
  [Amélioration de la liste Dates]: #bkmk_improvedates
  [Amélioration de la vue SessionsTable]: #bkmk_improvesessionstable
  [Amélioration de la vue SessionByCode]: #bkmk_improvesessionbycode
  [1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-1.png
  [2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-2.png
  [3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-3.png
  [4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-4.png
  [5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-5.png
  [6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-6.png
  [7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7.png
  [8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-8.png
  [9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags.png
  [10]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET.png
  [11]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-NoBootstrap.png
  [Bootstrap]: http://getbootstrap.com/
  [12]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile.png
  [13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile-Desktop.png
  [14]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Resolve-DefaultDisplayMode.png
  [15]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsIPhone-_LayoutIPhone.png
  [16]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile.png
  [17]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile-Overridden.png
  [linked list group]: http://getbootstrap.com/components/#list-group-linked
  [18]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed.png
  [19]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-Desktop.png
  [glyphicon]: http://getbootstrap.com/components/#glyphicons
  [20]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-SearchBySC.png
  [21]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-Desktop.png
  [22]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed.png
  [23]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed.png
  [panels]: http://getbootstrap.com/components/#panels
  [24]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2.png
  [25]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2-Desktop.png
  [26]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-SearchByASP.png
  [27]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Tag-ASP.NET.png
  [custom linked list group]: http://getbootstrap.com/components/#list-group-custom-content
  [grid system]: http://getbootstrap.com/css/#grid
  [utilitaires réactifs]: http://getbootstrap.com/css/#responsive-utilities
  [28]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Fixed-Tag-ASP.NET-Desktop.png
  [29]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-3-644.png
  [30]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-Fixed-3-644.png
  [Blog Bootstrap officiel]: http://blog.getbootstrap.com/
  [Tutoriel Bootstrap Twitter de Tutorial Republic]: http://www.tutorialrepublic.com/twitter-bootstrap-tutorial/
  [The Bootstrap Playground]: http://www.bootply.com/
  [Bonnes pratiques pour les applications Web mobiles des recommandations W3C]: http://www.w3.org/TR/mwabp/
  [W3C Candidate Recommendation des requêtes Média]: http://www.w3.org/TR/css3-mediaqueries/
