<properties 
	pageTitle="Intégration d'un service cloud à Azure CDN" 
	description="Didacticiel qui explique comment déployer un service cloud qui traite le contenu à partir d'un point de terminaison CDN Azure intégré" 
	services="cdn, cloud-services" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="cephalin"/>

#<a name="intro"></a> Intégration d’un service cloud à Azure CDN #

<!-- Keeping this article pinned to the old portal because CDN is not yet lit up in the new portal -->

Vous pouvez intégrer un service cloud à Azure CDN pour distribuer du contenu à partir du chemin d'accès `~/CDN` du service cloud. Cette approche offre les avantages suivants :

- Facilité de déploiement et de mise à jour des images, scripts et feuilles de style dans les annuaires de projet du service cloud
- Facilité de mise à niveau des package NuGet dans votre service cloud (par exemple, les versions de jQuery ou de Bootstrap) 
- Gestion de votre application web et de votre contenu CDN, le tout depuis la même interface Visual Studio
- Flux de travail unifié pour le déploiement de votre application web et de votre contenu CDN
- Intégration du regroupement et de la minimisation d'ASP.NET avec Azure CDN

## Contenu ##

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-	[Intégration d'un point de terminaison Azure CDN à votre service cloud et traitement du contenu statique dans vos pages Web depuis Azure CDN](#deploy)
-	[Configuration des paramètres du cache pour le contenu statique de votre service cloud](#caching)
-	[Distribution du contenu à partir d'actions de contrôleur via Azure CDN](#controller)
-	[Traitement du contenu regroupé et minimisé via Azure CDN tout en préservant le débogage des scripts dans Visual Studio](#bundling)
-	[Configuration de secours de vos scripts et feuilles de style CSS quand votre service Azure CDN est hors connexion](#fallback) 

## Ce que vous allez créer ##

Vous allez déployer un rôle Web de service cloud en utilisant le modèle ASP.NET MVC par défaut, ajouter du code pour distribuer le contenu depuis un service Azure CDN intégré (ex. image, résultats de l'action d'un contrôleur, et fichiers JavaScript et CSS par défaut) et écrire du code pour configurer le mécanisme de secours des lots traités si le service CDN est hors connexion.

## Éléments requis ##

Ce didacticiel nécessite les éléments suivants :

-	Un [compte Microsoft Azure](/account/) actif.
-	Visual Studio 2013 avec le [Kit de développement logiciel (SDK) Azure](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)

> [AZURE.NOTE]Vous avez besoin d’un compte Azure pour suivre ce didacticiel : + Vous pouvez [ouvrir un compte Azure gratuitement](/pricing/free-trial/) : vous obtenez alors des crédits dont vous pouvez vous servir pour essayer les services Azure payants et, une fois vos crédits épuisés, vous pouvez conserver le compte et utiliser les services Azure gratuits, notamment [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps. Vous pouvez [activer les avantages d’abonnement MSDN](/pricing/member-offers/msdn-benefits-details/) : votre abonnement MSDN vous octroie des crédits chaque mois que vous pouvez utiliser pour des services Azure payants.

<a name="deploy"></a>
## Déploiement d'un service cloud avec un point de terminaison CDN intégré ##

Dans cette section, vous allez déployer le modèle d'application ASP.NET MVC par défaut dans Visual Studio 2013 dans un rôle web de service cloud, puis l'intégrer à un nouveau point de terminaison CDN. Procédez comme suit :

1. Dans Visual Studio 2013, créez un service cloud Azure depuis la barre de menus : **Fichier > Nouveau > Projet > Cloud > Azure Cloud Service**. Attribuez-lui un nom et cliquez sur **OK**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)

2. Sélectionnez **Rôle Web ASP.NET** et cliquez sur le bouton **>**. Cliquez sur OK.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)

3. Sélectionnez **MVC** et cliquez sur **OK**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)

4. Publiez ce rôle web dans un service cloud Azure. Cliquez avec le bouton droit sur le projet de service cloud et sélectionnez **Publier**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

5. Si vous n'êtes pas encore connecté à Microsoft Azure, cliquez sur **Se connecter**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)

6. Dans la page de connexion, connectez-vous au compte Microsoft que vous avez utilisé pour activer votre compte Azure.
7. Une fois que vous êtes connecté, cliquez sur **Suivant**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)

8. En supposant que vous n'avez pas créé de compte de service cloud ou de stockage, Visual Studio vous aide à créer les deux. Dans la boîte de dialogue **Créer un service cloud et un compte**, tapez le nom souhaité pour le service. Cliquez sur **Créer**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)

9. Dans la page des paramètres de publication, vérifiez la configuration et cliquez sur **Publier**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)

	>[AZURE.NOTE]La publication des services cloud prend beaucoup de temps. L'option Activer Web Deploy pour tous les rôles accélère notablement le débogage de votre service cloud en fournissant des mises à jour rapides (mais provisoires) à vos rôles web. Pour en savoir plus sur cette option, consultez la page [Publication d'un service cloud en utilisant les Outils Azure](http://msdn.microsoft.com/library/ff683672.aspx).

	Quand le **Journal des activités Microsoft Azure** indique que la publication est **Terminée**, vous devez créer un point de terminaison CDN intégré à ce service cloud.

1. Pour créer un point de terminaison CDN, connectez-vous à votre [portail de gestion Azure](http://manage.windowsazure.com/).
2. Cliquez sur **Nouveau** > **Services d'application** > **CDN** > **Création rapide**. Sélectionnez **http://*&lt;servicename>*.cloudapp.net/cdn/** et cliquez sur **Créer**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-10-createcdn.png)

	>[AZURE.NOTE]Une fois votre point de terminaison CDN créé, le portail Azure affiche son URL et le domaine d'origine auquel il est intégré. Cependant, la configuration du nouveau point de terminaison CDN peut prendre un certain temps pour se propager à tous les emplacements des nœuds CDN.

	Notez que le point de terminaison CDN est lié au chemin **cdn/** de votre service cloud. Vous pouvez créer un dossier **cdn** dans votre projet **WebRole1** ou (comme c'est le cas dans ce didacticiel) utiliser la réécriture de l'URL pour éliminer tous les liens entrants de ce chemin.

3. De retour dans le portail Azure, dans l'onglet **CDN**, cliquez sur le nom du point de terminaison CDN que vous venez de créer.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-11-disablequerya.png)

3. Cliquez sur **Activer la chaîne de requête** pour activer les chaînes de requête dans le cache CDN. Lorsque vous activez cette fonction, un lien identique accessible avec différentes chaînes de requête sera mis en cache avec des entrées différentes.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-12-disablequeryb.png)

	>[AZURE.NOTE]Comme l'activation des chaînes de requête n'est pas nécessaire pour cette partie du didacticiel, désactivez-la au plus tôt par commodité, car la propagation à tous les nœuds CDN de toute modification apportée ici prend du temps et le contenu non activé pour les chaînes de requête ne doit pas encombrer le cache CDN (la mise à jour du contenu CDN est abordée plus tard).

3. Appliquez une commande ping à votre point de terminaison CDN pour vérifier qu'il est propagé à tous les nœuds CDN. Vous devrez peut-être attendre pendant une heure maximum avant qu'il réponde à vos commandes ping.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-13-testcdn.png)

2. De retour dans Visual Studio 2013, ouvrez **Web.config** dans votre projet **WebRole1** et ajoutez le code suivant dans la balise `<system.webServer>` :
	<pre class="prettyprint">
&lt;system.webServer>
  <mark>&lt;rewrite>
    &lt;rules>
      &lt;rule name="RewriteIncomingCdnRequest" stopProcessing="true">
        &lt;match url="^cdn/(.*)$"/>
        &lt;action type="Rewrite" url="{R:1}"/>
      &lt;/rule>
    &lt;/rules>
  &lt;/rewrite></mark>
  ...
&lt;/system.webServer>
</pre>

4. Republiez le service cloud. Cliquez avec le bouton droit sur le projet de service cloud et sélectionnez **Publier**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

1. Lorsque l'état de la publication est **Terminé**, ouvrez une fenêtre de navigateur et accédez à l'adresse **http://*&lt;cdnName>*.vo.msecnd.net/Content/bootstrap.css**. Dans ma configuration, cette URL est la suivante :

		http://az632148.vo.msecnd.net/Content/bootstrap.css

	Elle correspond à l'URL d'origine suivante au niveau du point de terminaison CDN :

		http://cephalinservice.cloudapp.net/cdn/Content/bootstrap.css

	Après la réécriture de l'URL dans mon application web, voici le fichier qui est réellement placé dans la mémoire cache de mon CDN :

		http://cephalinservice.cloudapp.net/Content/bootstrap.css

	Lorsque vous atteignez la page **http://*&lt;cdnName>*.vo.msecnd.net/Content/bootstrap.css**, vous êtes invité à télécharger le fichier bootstrap.css provenant de votre application Web publiée.

	![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

De même, vous pouvez accéder à toute URL accessible publiquement à l’adresse **http://*&lt;serviceName>*.cloudapp.net/**, directement depuis votre point de terminaison CDN. Par exemple :

-	un fichier .js à partir du chemin d'accès /Script ;
-	un fichier de contenu à partir du chemin d'accès /Content ;
-	un contrôleur/une action ; 
-	si la chaîne de requête est activée sur le point de terminaison CDN, une URL avec des chaînes de requête.

En fait, dans la configuration ci-dessus, vous pouvez héberger l'ensemble du service cloud à partir de **http://*&lt;cdnName>*.vo.msecnd.net/**. Si je me rends à l'adresse **http://az632148.vo.msecnd.net/**, j'obtiens le résultat de l'action à partir de Home/Index.

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

Cependant, cela ne signifie pas que c'est toujours (ou généralement) une bonne idée pour traiter l'ensemble du service cloud via Azure CDN. Mises en garde éventuelles :

-	Cette approche nécessite que l'ensemble de votre site soit public, car Azure CDN ne peut pas traiter du contenu privé.
-	Si le point de terminaison CDN passe hors connexion quelle qu'en soit la raison (maintenance prévue ou erreur utilisateur), l'ensemble de votre service cloud passe hors connexion à moins qu'il soit possible de rediriger les clients vers l'URL d'origine **http://*&lt;serviceName>*.cloudapp.net/**. 
-	Même avec les paramètres Cache-Control personnalisés (voir [Configuration des options de mise en cache des fichiers statiques dans votre service cloud](#caching)), un point de terminaison CDN n'améliore pas les performances des contenus très dynamiques. Si vous avez essayé de charger la page d'accueil depuis votre point de terminaison CDN (voir ci-dessus), notez qu'il faut environ 5 secondes pour charger la page d'accueil par défaut la première fois, alors qu'il s'agit d'une page relativement simple. Imaginez ce qui se passe du côté client si cette page comporte un contenu dynamique qui doit s'actualiser toutes les minutes. Le traitement du contenu dynamique depuis un point de terminaison CDN nécessite une brève expiration du cache qui se traduit en fréquentes erreurs dans le cache au niveau du point de terminaison. Cela nuit aux performances ou à votre service cloud, et va à l'encontre de la fonction d'un réseau de distribution de contenu (CDN).

La solution alternative consiste à déterminer au cas par cas le contenu à traiter à partir d'Azure CDN dans votre service cloud. À cette fin, vous avez déjà vu comment accéder à des fichiers de contenu individuels depuis le point de terminaison CDN. Je vais vous montrer comment traiter une action sur un contrôleur donné via le point de terminaison CDN dans la section [Distribution de contenu à partir d'actions de contrôleur via Azure CDN](#controller).

Vous pouvez spécifier une règle de réécriture de l'URL plus restrictive pour limiter le contenu accessible via votre point de terminaison CDN. Pour limiter, par exemple, la réécriture de l'URL dans le dossier *\\Scripts*, modifiez la règle de réécriture ci-dessus comme suit : <pre class="prettyprint"> &lt;rule name=";RewriteIncomingCdnRequest"; stopProcessing=";true";&gt; &lt;match url=";^cdn/<mark>Scripts/</mark>(.*)$";/&gt; &lt;action type=";Rewrite"; url=";<mark>Scripts/</mark>{R:1}";/&gt; &lt;/rule&gt; </pre>

<a name="caching"></a>
## Configuration des options de mise en cache pour les fichiers statiques de votre service cloud ##

Avec l'intégration d'Azure CDN à votre service cloud, vous pouvez spécifier comment vous voulez que le contenu statique soit mis en cache dans le point de terminaison CDN. Pour cela, ouvrez *Web.config* à partir de votre projet de rôle Web (par exemple, WebRole1) et ajoutez l'élément `<staticContent>` à `<system.webServer>`. Le code XML ci-dessous configure le cache pour expirer dans les 3 jours. <pre class="prettyprint"> &lt;system.webServer&gt; <mark>&lt;staticContent&gt; &lt;clientCache cacheControlMode=";UseMaxAge"; cacheControlMaxAge=";3.00:00:00";/&gt; &lt;/staticContent&gt;</mark> ... &lt;/system.webServer&gt; </pre>

Lorsque vous faites cela, tous les fichiers statiques de votre service cloud respectent la même règle dans le cache de votre CDN. Pour un contrôle plus granulaire des paramètres du cache, ajoutez un fichier *Web.config* dans un dossier et ajoutez-lui vos paramètres. Exemple : ajoutez un fichier *Web.config* au dossier *\\Content* et remplacez son contenu par le code XML suivant :

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

Ce paramètre entraîne la mise en cache de tous les fichiers statiques du dossier *\\Content* pendant 15 jours.

Pour plus d’informations sur la configuration de l’élément `<clientCache>`, consultez la page [Cache du client &lt;clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

Dans la rubrique [Distribution de contenu à partir d'actions de contrôleur via Azure CDN](#controller), je vais également vous montrer comment configurer les paramètres du cache pour les résultats de l'action du contrôleur dans le cache du CDN.

<a name="controller"></a>
## Distribution du contenu à partir d'actions de contrôleur via Azure CDN ##

Lorsque vous intégrez un rôle web de service cloud à Azure CDN, il est relativement facile de distribuer du contenu à partir d'actions de contrôleur via le réseau de distribution de contenu (CDN) Azure. En dehors du traitement de votre service cloud directement via Azure CDN (démontré ci-dessus), [Maarten Balliauw](https://twitter.com/maartenballiauw) vous démontre comment le faire avec le contrôleur MemeGenerator à la page [Réduction de la latence sur Internet avec le réseau de distribution de contenu (CDN) Azure](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN). Je reproduis simplement cette page ici.

Supposez que, dans votre service cloud, vous vouliez créer des idées culturelles basées sur une image de Chuck Norris jeune (photo de [Alan Light](http://www.flickr.com/photos/alan-light/218493788/)) ainsi :

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Une simple action `Index` permet aux clients de spécifier les superlatifs dans l’image, puis de créer l’idée lorsqu’ils publient vers l’action. Comme il s'agit de Chuck Norris, vous espérez que cette page devienne extrêmement populaire dans le monde entier. Cet exemple illustre bien la distribution de contenu semi-dynamique avec Azure CDN.

Procédez comme ci-dessus pour configurer cette action de contrôleur :

1. Dans le dossier *\\Controllers*, créez un fichier .cs nommé *MemeGeneratorController.cs* et remplacez son contenu par le code suivant. N'oubliez pas de remplacer la partie en surbrillance par le nom de votre CDN.  
	<pre class="prettyprint">
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Drawing;
using System.IO;
using System.Net;
using System.Web.Hosting;
using System.Web.Mvc;
using System.Web.UI;

namespace WebRole1.Controllers
{
    public class MemeGeneratorController : Controller
    {
        static readonly Dictionary&lt;string, Tuple&lt;string ,string>> Memes = new Dictionary&lt;string, Tuple&lt;string, string>>();

        public ActionResult Index()
        {
            return View();
        }

        [HttpPost, ActionName("Index")]
    	public ActionResult Index_Post(string top, string bottom)
        {
            var identifier = Guid.NewGuid().ToString();
            if (!Memes.ContainsKey(identifier))
            {
                Memes.Add(identifier, new Tuple&lt;string, string>(top, bottom));
            }

            return Content("&lt;a href="" + Url.Action("Show", new {id = identifier}) + "">here's your meme&lt;/a>");
        }

        [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
        public ActionResult Show(string id)
        {
            Tuple&lt;string, string> data = null;
            if (!Memes.TryGetValue(id, out data))
            {
                return new HttpStatusCodeResult(HttpStatusCode.NotFound);
            }

            if (Debugger.IsAttached) // Preserve the debug experience
            {
                return Redirect(string.Format("/MemeGenerator/Generate?top={0}&amp;bottom={1}", data.Item1, data.Item2));
            }
            else // Get content from Azure CDN
            {
                return Redirect(string.Format("http://<mark>&lt;yourCdnName></mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}", data.Item1, data.Item2));
            }
        }

        [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
        public ActionResult Generate(string top, string bottom)
        {
            string imageFilePath = HostingEnvironment.MapPath("<mark>~/Content/chuck.bmp</mark>");
            Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);

            using (Graphics graphics = Graphics.FromImage(bitmap))
            {
                SizeF size = new SizeF();
                using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                {
                    graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
                }
                using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                {
                    graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
                }
            }

            MemoryStream ms = new MemoryStream();
            bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
            return File(ms.ToArray(), "image/png");
        }

        private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
        {
            // Compute actual size, shrink if needed
            while (true)
            {
                size = g.MeasureString(text, font);

                // Correspond, back out
                if (size.Height &lt; i.Height &amp;&amp;
                     size.Width &lt; i.Width) { return font; }

                // Try a smaller font (90% of old size)
                Font oldFont = font;
                font = new Font(font.Name, (float)(font.Size * .9), font.Style);
                oldFont.Dispose();
            }
        }
    }
}
</pre>

2. Cliquez avec le bouton droit sur l’action `Index()` par défaut et sélectionnez **Ajouter une vue**.

	![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)

3.  Acceptez les paramètres ci-dessous et cliquez sur **Ajouter**.

	![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)

4. Ouvrez le nouveau fichier *Views\\MemeGenerator\\Index.cshtml* et remplacez son contenu par le simple code HTML suivant pour envoyer les superlatifs :

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. Republiez le service cloud et accédez à l'adresse **http://*&lt;serviceName>*.cloudapp.net/MemeGenerator/Index** dans votre navigateur.

Quand vous envoyez les valeurs du formulaire dans `/MemeGenerator/Index`, la méthode d’action `Index_Post` renvoie un lien vers la méthode d’action `Show` avec l’identificateur d’entrée respectif. En cliquant sur le lien, vous atteignez le code suivant : <pre class="prettyprint"> [OutputCache(VaryByParam = ";*";, Duration = 1, Location = OutputCacheLocation.Downstream)] public ActionResult Show(string id) { Tuple&lt;string, string&gt; data = null; if (!Memes.TryGetValue(id, out data)) { return new HttpStatusCodeResult(HttpStatusCode.NotFound); }

    if (Debugger.IsAttached) // Preserve the debug experience
    {
        return Redirect(string.Format(";/MemeGenerator/Generate?top={0}&bottom={1}";, data.Item1, data.Item2));
    }
    else // Get content from Azure CDN
    {
        return Redirect(string.Format(";http://<mark>&lt;cdnName&gt;</mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}";, data.Item1, data.Item2));
    }
} </pre>

Si votre débogueur local est lié, vous effectuez un débogage normal avec une redirection locale. S'il est exécuté dans le service cloud, il redirige vers :

	http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Qui correspond à l'URL d'origine suivante au niveau de votre point de terminaison CDN :

	http://<youCloudServiceName>.cloudapp.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Après la règle de réécriture de l'URL précédemment appliquée, le fichier qui est réellement placé dans la mémoire cache du point de terminaison de votre CDN est :

	http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Vous pouvez alors appliquer l’attribut `OutputCacheAttribute` à la méthode `Generate` pour spécifier la manière dont l’action doit être mise en cache, et qu’Azure CDN respectera. Le code ci-dessous spécifie 1 heure (3 600 secondes) pour l'expiration du cache.

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

De même, vous pouvez distribuer le contenu de l'action d'un contrôleur dans votre service cloud via le réseau de distribution de contenu (CDN) Azure avec l'option de mise en cache voulue.

Dans la section suivante, je vais vous montrer comment distribuer les scripts et les feuilles de style CSS regroupés et minimisés via Azure CDN.

<a name="bundling"></a>
## Intégration du regroupement et de la minimisation d'ASP.NET avec Azure CDN ##

Les scripts et les feuilles de style CSS, qui ne changent pas fréquemment, sont les principaux candidats pour le cache Azure CDN. La distribution de l'ensemble du rôle web via votre réseau de distribution de contenu (CDN) Azure est la manière la plus facile d'intégrer le regroupement et la minimisation à Azure CDN. Cependant, comme vous ne voudrez peut-être pas faire cela, je vais vous montrer comment le faire en conservant l'approche souhaitée par le développeur concernant le regroupement et la minimisation ASP.NET, par exemple :

-	Mode débogage puissant
-	Facilité de déploiement
-	Mises à jour immédiates à destination des clients lors des mises à niveau des versions des scripts et des feuilles de style CSS
-	Mécanisme de secours en cas de défaillance de votre point de terminaison CDN
-	Modifications minimales du code

Dans le projet **WebRole1** que vous avez créé dans [Déploiement d'un service cloud avec un point de terminaison CDN intégré](#deploy), ouvrez le fichier *App_Start\\BundleConfig.cs* et examinez les appels de la méthode `bundles.Add()`.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

La première instruction `bundles.Add()` ajoute un regroupement de script dans le répertoire virtuel `~/bundles/jquery`. Ouvrez ensuite le fichier *Views\\Shared_Layout.cshtml* pour voir comment la balise de regroupement de script est rendue. Vous devez trouver la ligne de code Razor suivante :

    @Scripts.Render("~/bundles/jquery")

Quand ce code Razor est exécuté dans le rôle web Azure, il restitue une balise `<script>` pour le regroupement du script similaire à ce qui suit :

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Cependant, quand il est exécuté dans Visual Studio en appuyant sur `F5`, il restitue individuellement chaque fichier de script dans le regroupement (dans le cas ci-dessus, un seul fichier de script se trouve dans le regroupement) :

    <script src="/Scripts/jquery-1.10.2.js"></script>

Cela permet de déboguer le code JavaScript dans votre environnement de développement tout en réduisant les connexions clientes simultanées (regroupement) et en améliorant les performances de téléchargement (minimisation) en production. Cette fonctionnalité est très intéressante à conserver avec l'intégration Azure CDN. De plus, du fait que le regroupement restitué contient déjà une chaîne de version générée automatiquement, vous voudrez répliquer cette fonctionnalité de façon que, chaque fois que vous mettrez à jour votre version jQuery via NuGet, il sera possible de la mettre à jour côté client le plus rapidement possible.

Procédez comme suit pour l'intégration du regroupement et de la minimisation ASP.NET à votre point de terminaison CDN.

1. De retour dans *App_Start\\BundleConfig.cs*, modifiez les méthodes `bundles.Add()` pour utiliser un autre [constructeur de regroupement](http://msdn.microsoft.com/library/jj646464.aspx) qui spécifie une adresse CDN. Pour cela, remplacez la définition de la méthode `RegisterBundles` par le code suivant :  
	<pre class="prettyprint">
public static void RegisterBundles(BundleCollection bundles)
{
    <mark>bundles.UseCdn = true;
    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
        .GetName().Version.ToString();
    var cdnUrl = "http://&lt;yourCDNName>.vo.msecnd.net/{0}?v=" + version;</mark>

    bundles.Add(new ScriptBundle("~/bundles/jquery"<mark>, string.Format(cdnUrl, "bundles/jquery")</mark>).Include(
                "~/Scripts/jquery-{version}.js"));

    bundles.Add(new ScriptBundle("~/bundles/jqueryval"<mark>, string.Format(cdnUrl, "bundles/jqueryval")</mark>).Include(
                "~/Scripts/jquery.validate*"));

    // Use the development version of Modernizr to develop with and learn from. Then, when you're
    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
    bundles.Add(new ScriptBundle("~/bundles/modernizr"<mark>, string.Format(cdnUrl, "bundles/modernizer")</mark>).Include(
                "~/Scripts/modernizr-*"));

    bundles.Add(new ScriptBundle("~/bundles/bootstrap"<mark>, string.Format(cdnUrl, "bundles/bootstrap")</mark>).Include(
                "~/Scripts/bootstrap.js",
                "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css"<mark>, string.Format(cdnUrl, "Content/css")</mark>).Include(
                "~/Content/bootstrap.css",
                "~/Content/site.css"));
}
</pre>N'oubliez pas de remplacer `<yourCDNName>` par le nom de votre réseau de distribution de contenu (CDN) Azure.

	Exprimé simplement, vous configurez `bundles.UseCdn = true` et ajoutez une URL CDN correctement formatée à chaque regroupement. Par exemple : le premier constructeur dans le code :

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	est identique à :

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

	Ce constructeur indique au regroupement et à la minimisation ASP.NET de restituer des fichiers de script individuels lorsqu'ils sont débogués localement, mais d'utiliser l'adresse CDN spécifiée pour accéder au script en question. Cependant, notez deux caractéristiques importantes avec cette URL CDN correctement formatée :
	
	-	L'origine de cette URL CDN est `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`, qui est en vérité le répertoire virtuel du regroupement de script dans votre service cloud.
	-	Comme vous utilisez un constructeur CDN, la balise du script CDN pour le regroupement ne contient plus la chaîne de caractères de la version automatiquement générée dans l'URL restituée. Vous devez créer manuellement une chaîne de version unique à chaque modification du regroupement de script pour forcer une erreur dans le cache de votre réseau de distribution de contenu (CDN) Azure. En même temps, cette chaîne de version unique doit rester constante tout au long du déploiement pour un accès maximal au cache de votre réseau de distribution de contenu (CDN) Azure après le déploiement du regroupement.
	-	La chaîne de requête v=<W.X.Y.Z> est extraite de *Properties\\AssemblyInfo.cs* dans votre projet de rôle web. Vous pouvez avoir un flux de travail de déploiement qui inclut l'incrémentation de la version de l'assembly chaque fois que vous publiez dans Azure. Vous pouvez également modifier *Properties\\AssemblyInfo.cs* dans votre projet pour incrémenter automatiquement la chaîne de caractères de la version à chaque génération en utilisant le caractère générique '*'. Par exemple :
	
			[assembly: AssemblyVersion("1.0.0.*")]
	
		Toute autre stratégie pour simplifier la génération d'une chaîne unique tout au long d'un déploiement fonctionne dans ce cas.

3. Republiez le service cloud et accédez à la page d'accueil.
 
4. Affichez le code HTML de la page. Vous devez voir l'URL CDN restituée avec une chaîne de version unique chaque fois que vous republiez des modifications dans votre service cloud. Par exemple :
	<pre class="prettyprint">
...

&lt;link href="http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>

&lt;script src="http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449">&lt;/script>

...

&lt;script src="http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25449">&lt;/script>

&lt;script src="http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449">&lt;/script>

...</pre>

5. Dans Visual Studio, tapez `F5` pour déboguer le service cloud.

6. Affichez le code HTML de la page. Vous voyez chaque fichier de script restitué individuellement de façon à effectuer un débogage cohérent dans Visual Studio.
	<pre class="prettyprint">
...

    &lt;link href="/Content/bootstrap.css" rel="stylesheet"/>
&lt;link href="/Content/site.css" rel="stylesheet"/>

    &lt;script src="/Scripts/modernizr-2.6.2.js">&lt;/script>

...

    &lt;script src="/Scripts/jquery-1.10.2.js">&lt;/script>

    &lt;script src="/Scripts/bootstrap.js">&lt;/script>
&lt;script src="/Scripts/respond.js">&lt;/script>

...    
</pre>

<a name="fallback"></a>
## Mécanisme de secours pour les URL CDN ##

Lorsque le point de terminaison de votre réseau de distribution de contenu (CDN) présente une défaillance pour quelque raison que ce soit, vous voulez que votre page web soit suffisamment bien conçue pour accéder à votre serveur web d'origine comme option de secours pour le chargement de JavaScript ou Bootstrap. C'est une chose de perdre des images sur votre site web à cause d'une indisponibilité CDN, c'en est une autre de perdre dans une page une fonctionnalité vitale fournie par vos scripts et vos feuilles de style.

La classe [Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) contient la propriété [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) qui vous permet de configurer le mécanisme de secours en cas de défaillance CDN. Pour utiliser cette propriété, procédez comme suit :

1. Dans votre projet de rôle Web, ouvrez *App_Start\\BundleConfig.cs*, où vous avez ajouté une URL CDN dans chaque [constructeur de regroupement](http://msdn.microsoft.com/library/jj646464.aspx), puis apportez les modifications en surbrillance pour ajouter un mécanisme de secours aux regroupements par défaut :  
	<pre class="prettyprint">
public static void RegisterBundles(BundleCollection bundles)
{
    var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
        .GetName().Version.ToString();
    var cdnUrl = "http://cdnurl.vo.msecnd.net/.../{0}?" + version;
    bundles.UseCdn = true;

    bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")) 
				<mark>{ CdnFallbackExpression = "window.jquery" }</mark>
                .Include("~/Scripts/jquery-{version}.js"));

    bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")) 
				<mark>{ CdnFallbackExpression = "$.validator" }</mark>
            	.Include("~/Scripts/jquery.validate*"));

    // Use the development version of Modernizr to develop with and learn from. Then, when you're
    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
    bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")) 
				<mark>{ CdnFallbackExpression = "window.Modernizr" }</mark>
				.Include("~/Scripts/modernizr-*"));

    bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")) 	
				<mark>{ CdnFallbackExpression = "$.fn.modal" }</mark>
        		.Include(
	              		"~/Scripts/bootstrap.js",
	              		"~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                "~/Content/bootstrap.css",
                "~/Content/site.css"));
}</pre>Quand `CdnFallbackExpression` n’a pas la valeur null, le script est injecté dans le code HTML pour tester où le regroupement est correctement chargé et, dans le cas contraire, pour accéder au regroupement directement depuis le serveur web d’origine. Cette propriété doit être configurée avec une expression JavaScript qui teste si le regroupement CDN correspondant est correctement chargé. L'expression nécessaire pour tester chaque regroupement est différente en fonction du contenu. Pour les regroupements par défaut ci-dessus :
	
	-	`window.jquery` est défini dans jquery-{version}.js
	-	`$.validator` est défini dans jquery.validate.js
	-	`window.Modernizr` est défini dans modernizer-{version}.js
	-	`$.fn.modal` est défini dans bootstrap.js
	
	Vous aurez peut-être remarqué que je n’ai pas configuré CdnFallbackExpression pour le regroupement `~/Cointent/css`. Cela est dû au fait qu’un [bogue dans System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) injecte une balise `<script>` pour le fichier CSS de secours à la place de la balise attendue `<link>`.
	
	Il existe néanmoins un bon [mécanisme de secours pour les styles](https://github.com/EmberConsultingGroup/StyleBundleFallback) proposé par [Ember Consulting Group](https://github.com/EmberConsultingGroup).

2. Pour utiliser cette solution pour le CSS, créez un fichier .cs dans le répertoire *App_Start*, en le nommant *StyleBundleExtensions.cs*, puis remplacez son contenu par du [code issu de GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs).

4. Dans *App_Start\\StyleFundleExtensions.cs*, renommez l'espace de noms avec le nom de votre rôle web (par exemple, **WebRole1**).

3. Revenez à `App_Start\BundleConfig.cs` et remplacez la dernière instruction `bundles.Add` par le code suivant en surbrillance :
	<pre class="prettyprint">
bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
    .Include(
          "~/Content/bootstrap.css",
          "~/Content/site.css"));
</pre>Cette nouvelle méthode d'extension utilise la même idée pour injecter un script dans le code HTML afin de vérifier le DOM pour le nom de classe, le nom de règle et la valeur de règle correspondants dans le regroupement CSS. Elle rétablit le serveur web d'origine si elle ne trouve pas de correspondance.

4. Republiez le service cloud et accédez à la page d'accueil.
5. Affichez le code HTML de la page. Vous devez trouver des scripts injectés similaires à ce qui suit :    
	<pre class="prettyprint">...

	&lt;link href="http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
<mark>&lt;script>(function() {
                var loadFallback,
                    len = document.styleSheets.length;
                for (var i = 0; i &lt; len; i++) {
                    var sheet = document.styleSheets[i];
                    if (sheet.href.indexOf('http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474') !== -1) {
                        var meta = document.createElement('meta');
                        meta.className = 'sr-only';
                        document.head.appendChild(meta);
                        var value = window.getComputedStyle(meta).getPropertyValue('width');
                        document.head.removeChild(meta);
                        if (value !== '1px') {
                            document.write('&lt;link href="/Content/css" rel="stylesheet" type="text/css" />');
                        }
                    }
                }
                return true;
            }())||document.write('&lt;script src="/Content/css">&lt;/script>');&lt;/script></mark>

    &lt;script src="http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474">&lt;/script>
<mark>&lt;script>(window.Modernizr)||document.write('&lt;script src="/bundles/modernizr">&lt;/script>');&lt;/script></mark>

...	

    &lt;script src="http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25474">&lt;/script>
<mark>&lt;script>(window.jquery)||document.write('&lt;script src="/bundles/jquery">&lt;/script>');&lt;/script></mark>

    &lt;script src="http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474">&lt;/script>
<mark>&lt;script>($.fn.modal)||document.write('&lt;script src="/bundles/bootstrap">&lt;/script>');&lt;/script></mark>

...
</pre>Notez que le script injecté pour le regroupement CSS contient toujours les éléments restants provenant de la propriété `CdnFallbackExpression` dans la ligne :

        }())||document.write('<script src="/Content/css"></script>');</script>

	Mais, du fait que la première partie de l'expression || renvoie toujours la valeur true (dans la ligne juste au-dessus), la fonction document.write() ne s'exécute jamais.

## Informations complémentaires ##
- [Vue d’ensemble du réseau de distribution de contenu (CDN) Azure](cdn-overview.md)
- [Distribution de contenu depuis Azure CDN dans votre application web](cdn-serve-content-from-cdn-in-your-web-application.md)
- [Utiliser Azure CDN dans Azure App Service](../cdn-websites-with-cdn.md)
- [Regroupement et minimisation d’ASP.NET](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
- [Utilisation du réseau de distribution de contenu (CDN) Azure](cdn-how-to-use-cdn.md)
 

<!---HONumber=July15_HO3-->