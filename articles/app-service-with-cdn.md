<properties 
	pageTitle="Intégration d'une application web Azure à Azure CDN" 
	description="Didacticiel qui explique comment déployer une application web qui distribue du contenu à partir d'un point de terminaison Azure CDN intégré" 
	services="app-service\web" 
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
	ms.date="03/04/2015" 
	ms.author="cephalin"/>

<a name="intro"></a>
# Intégration d'une application web Azure à Azure CDN #

L'intégration d'Azure App Service à [Azure CDN](http://azure.microsoft.com/services/cdn/) permet d'ajouter les fonctionnalités de mise à l'échelle globale inhérentes à l'application web App Service en distribuant globalement le contenu de votre application web à partir des nœuds de serveur les plus proches de vos clients (la liste récente de tous les emplacements de nœuds actuels se trouve [ici](http://msdn.microsoft.com/library/azure/gg680302.aspx). Cette intégration augmente considérablement les performances de l'application web Azure App Service et améliore nettement l'expérience utilisateur de votre application web pour les utilisateurs du monde entier. 

L'intégration des applications web Azure à Azure CDN offre les avantages suivants :

- Intégration du déploiement de contenu (images, scripts et feuilles de style) dans le cadre du processus de [déploiement continu](web-sites-publish-source-control.md) de votre application web Azure
- Facilité de mise à niveau des package NuGet de votre application web dans Azure App Service (par exemple, les versions de jQuery ou de Bootstrap) 
- Gestion de votre application web et de votre contenu CDN depuis la même interface Visual Studio
- Intégration du regroupement et de la minimisation d'ASP.NET avec Azure CDN

## Ce que vous allez créer ##

Vous allez déployer une application web sur Azure App Service en utilisant le modèle ASP.NET MVC par défaut dans Visual Studio, ajouter du code pour distribuer le contenu depuis un service Azure CDN intégré (ex. image, résultats de l'action d'un contrôleur, et fichiers JavaScript et CSS par défaut) et écrire du code pour configurer le mécanisme de secours des lots traités si le service CDN est hors connexion.

## Éléments requis ##

Ce didacticiel nécessite les éléments suivants :

-	Un [compte Microsoft Azure](http://azure.microsoft.com/account/) actif
-	Visual Studio 2013 avec le [Kit de développement logiciel (SDK) Azure](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)

> [AZURE.NOTE] Pour suivre ce didacticiel, vous avez besoin d'un compte Azure :
> + Vous pouvez [ouvrir un compte Azure gratuitement](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) : vous obtenez alors des crédits dont vous pouvez vous servir pour tester les services Azure payants. Même lorsqu'ils sont épuisés, vous pouvez conserver le compte et utiliser les services Azure gratuits, notamment Web Apps.
> + Vous pouvez [activer les avantages de l'abonnement MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) : votre abonnement MSDN vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.

<a name="deploy"></a>
## Déployer une application web Azure avec un point de terminaison CDN intégré ##

Dans cette section, vous allez déployer le modèle d'application ASP.NET MVC par défaut dans Visual Studio 2013 sur une application web Azure, puis l'intégrer à un nouveau point de terminaison CDN. Procédez comme suit :

1. Dans Visual Studio 2013, créez une application web ASP.NET à partir de la barre de menu en accédant à **Fichier > Nouveau > Projet > Web > Application web ASP.NET**. Attribuez-lui un nom, puis cliquez sur **OK**.

	![](media/cdn-websites-with-cdn/1-new-project.png)

3. Sélectionnez **MVC**, puis cliquez sur **Gérer les abonnements**.

	![](media/cdn-websites-with-cdn/2-webapp-template.png)

4. Cliquez sur **Se connecter**.

	![](media/cdn-websites-with-cdn/3-manage-subscription.png)

6. Dans la page de connexion, connectez-vous au compte Microsoft que vous avez utilisé pour activer votre compte Azure.
7. Une fois connecté, cliquez sur **Fermer**. Ensuite, cliquez sur **OK** pour continuer.

	![](media/cdn-websites-with-cdn/4-signed-in.png)

8. Si vous n'avez pas déjà créé d'application web Azure, Visual Studio peut vous aider à le faire. Dans la boîte de dialogue **Configurer le site web Microsoft Azure**, vérifiez que le nom de votre site est unique. Cliquez ensuite sur **OK**.

	![](media/cdn-websites-with-cdn/5-create-website.png)

9. Une fois votre application ASP.NET créée, publiez-la sur Azure dans le volet Activité de publication sur le Web en cliquant sur **Publier`<nom de l'application>` sur ce site dès maintenant**. Cliquez sur **Publier** pour terminer le processus.

	![](media/cdn-websites-with-cdn/6-publish-website.png)

	Une fois publiée, votre application web s'affiche dans le navigateur. 

1. Pour créer un point de terminaison CDN, connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com/). 
2. Cliquez sur **Nouveau** > **Services d'application** > **CDN** > **Création rapide**. Sélectionnez **http://*&lt;nom du site>*.azurewebsites.net/**, puis cliquez sur **Créer**.

	![](media/cdn-websites-with-cdn/7-create-cdn.png)

	> [AZURE.NOTE] Une fois votre point de terminaison CDN créé, le portail Azure affiche son URL et le domaine d'origine auquel il est intégré. Cependant, la configuration du nouveau point de terminaison CDN peut prendre un certain temps pour se propager à tous les emplacements des nœuds CDN. 

3. De retour dans le portail Azure, sous l'onglet **CDN**, cliquez sur le nom du point de terminaison CDN que vous venez de créer.

	![](media/cdn-websites-with-cdn/8-select-cdn.png)

3. Cliquez sur **Activer la chaîne de requête** pour activer les chaînes de requête dans le cache CDN. Lorsque vous activez cette fonction, un lien identique accessible avec différentes chaînes de requête sera mis en cache avec des entrées différentes.

	![](media/cdn-websites-with-cdn/9-enable-query-string.png)

	>[AZURE.NOTE] Comme l'activation des chaînes de requête n'est pas nécessaire pour cette partie du didacticiel, désactivez-la au plus tôt par commodité, car la propagation à tous les nœuds CDN de toute modification apportée ici prend du temps et le contenu non activé pour les chaînes de requête ne doit pas encombrer le cache CDN (la mise à jour du contenu CDN est abordée plus tard).

2. À présent, cliquez sur l'adresse du point de terminaison CDN. Si le point de terminaison est prêt, votre application web doit être affichée. Si vous obtenez une erreur **HTTP 404**, c'est que le point de terminaison CDN n'est pas prêt. La propagation de la configuration du CDN sur tous les nœuds de périmètre peut prendre jusqu'à une heure. 

	![](media/cdn-websites-with-cdn/11-access-success.png)

1. Ensuite, accédez au fichier **~/Content/bootstrap.css** de votre projet ASP.NET. Dans la fenêtre du navigateur, accédez à **http://*&lt;nom du CDN>*.vo.msecnd.net/Content/bootstrap.css**. Dans ma configuration, cette URL est la suivante :

		http://az673227.vo.msecnd.net/Content/bootstrap.css

	Elle correspond à l'URL d'origine suivante au niveau du point de terminaison CDN :

		http://cdnwebapp.azurewebsites.net/Content/bootstrap.css

	Quand vous accédez au fichier **http://*&lt;nom du CDN>*.vo.msecnd.net/Content/bootstrap.css**, vous êtes invité à télécharger le fichier bootstrap.css provenant de votre application web dans Azure. 

	![](media/cdn-websites-with-cdn/12-file-access.png)

De même, vous pouvez accéder à toute URL accessible publiquement à l'adresse **http://*&lt;nom du service>*.cloudapp.net/**, directement depuis votre point de terminaison CDN. Par exemple :

-	un fichier .js à partir du chemin d'accès /Script ;
-	un fichier de contenu à partir du chemin d'accès /Content ;
-	un contrôleur/une action ; 
-	si la chaîne de requête est activée sur le point de terminaison CDN, une URL avec des chaînes de requête.
-	l'intégralité de l'application web Azure, si tout le contenu est public.

Notez que ce n'est pas toujours une bonne idée de fournir l'intégralité d'une application web via Azure CDN. Mises en garde éventuelles :

-	Cette approche nécessite que l'ensemble de votre site soit public, car Azure CDN ne peut pas traiter du contenu privé.
-	Si le point de terminaison CDN passe hors connexion, quelle qu'en soit la raison (maintenance prévue ou erreur utilisateur), l'ensemble de votre application web passe hors connexion, à moins qu'il ne soit possible de rediriger les clients vers l'URL d'origine **http://*&lt;nom du site>*.azurewebsites.net/**. 
-	Même avec les paramètres Cache-Control personnalisés (consultez [Configuration des options de mise en cache pour les fichiers statiques de votre site web Azure](#caching)), un point de terminaison CDN n'améliore pas les performances des contenus très dynamiques. Si vous avez essayé de charger la page d'accueil depuis votre point de terminaison CDN (voir ci-dessus), notez qu'il faut environ 5 secondes pour charger la page d'accueil par défaut la première fois, alors qu'il s'agit d'une page relativement simple. Imaginez ce qui se passe du côté client si cette page comporte un contenu dynamique qui doit s'actualiser toutes les minutes. Le traitement du contenu dynamique depuis un point de terminaison CDN nécessite une brève expiration du cache qui se traduit en fréquentes erreurs dans le cache au niveau du point de terminaison. Cela nuit aux performances de votre application web Azure et va à l'encontre de la fonction d'un réseau de distribution de contenu (CDN).

La solution alternative consiste à déterminer au cas par cas le contenu à traiter à partir d'Azure CDN dans votre application web Azure. À cette fin, vous avez déjà vu comment accéder à des fichiers de contenu individuels depuis le point de terminaison CDN. Nous allons voir comment traiter une action sur un contrôleur donné via le point de terminaison CDN dans la section [Distribution du contenu à partir d'actions de contrôleur via Azure CDN](#controller).

<a name="caching"></a>
## Configurer les options de mise en cache pour les fichiers statiques de votre application web Azure ##

Avec l'intégration d'Azure CDN à votre application web Azure, vous pouvez spécifier comment vous voulez que le contenu statique soit mis en cache dans le point de terminaison CDN. Pour cela, ouvrez  *Web.config* à partir de votre projet ASP.NET (par exemple **cdnwebapp**), puis ajoutez un élément `<staticContent>` à `<system.webServer>`. Le code XML ci-dessous configure l'expiration du cache dans 3 jours.  
<pre class="prettyprint">
&lt;system.webServer&gt;
  <mark>&lt;staticContent&gt;
    &lt;clientCache cacheControlMode=&quot;UseMaxAge&quot; cacheControlMaxAge=&quot;3.00:00:00&quot;/&gt;
  &lt;/staticContent&gt;</mark>
  ...
&lt;/system.webServer&gt;
</pre>

Quand vous procédez ainsi, tous les fichiers statiques de votre application web Azure respectent la même règle dans le cache de votre CDN. Pour un contrôle plus granulaire des paramètres du cache, ajoutez un fichier  *Web.config* dans un dossier, puis ajoutez-lui vos paramètres. Par exemple, ajoutez un fichier  *Web.config* au dossier *\Content* et remplacez son contenu par le code XML suivant :

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

Ce paramètre entraîne la mise en cache de tous les fichiers statiques du dossier *\Content* pendant 15 jours.

Pour plus d'informations sur la façon de configurer l'élément `<clientCache>`, consultez [Cache du client&lt;clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

Dans la rubrique [Distribution du contenu à partir d'actions de contrôleur via Azure CDN](#controller), nous allons voir également comment configurer les paramètres du cache pour les résultats de l'action du contrôleur dans le cache du CDN.

<a name="controller"></a>
## Distribution du contenu à partir d'actions de contrôleur via Azure CDN ##

Quand vous intégrez une application web Azure à Azure CDN, il est relativement facile de distribuer du contenu à partir d'actions de contrôleur via Azure CDN. Une fois encore, si vous décidez de distribuer l'intégralité de l'application web Azure via votre CDN, cette opération n'est pas nécessaire, puisque toutes les actions de contrôleur sont déjà accessibles via le CDN. Cependant, les raisons évoquées dans la section [Déploiement d'un site web Azure avec un point de terminaison CDN intégré](#deploy) ont pu vous décider à utiliser les actions de contrôleur pour servir vos contenus à partir d'Azure CDN. [Maarten Balliauw](https://twitter.com/maartenballiauw) vous montre comment procéder avec un contrôleur MemeGenerator dans [Reducing latency on the web with the Azure CDN](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN). Je reproduis simplement cette page ici.

Supposez que, dans votre application web, vous vouliez créer des idées culturelles basées sur une image de Chuck Norris jeune (photo de [Alan Light](http://www.flickr.com/photos/alan-light/218493788/)) ainsi :

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Une simple action  `Index` permet aux clients de spécifier les superlatifs dans l'image, puis de créer l'idée culturelle lorsqu'ils publient vers l'action. Comme il s'agit de Chuck Norris, vous espérez que cette page devienne extrêmement populaire dans le monde entier. Cet exemple illustre bien la distribution de contenu semi-dynamique avec Azure CDN. 

Procédez comme ci-dessus pour configurer cette action de contrôleur :

1. Dans le dossier *\Controllers*, créez un fichier .cs nommé  *MemeGeneratorController.cs*, puis remplacez son contenu par le code suivant. N'oubliez pas de remplacer la partie en surbrillance par le chemin d'accès de votre fichier et le nom de votre CDN.
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
	
	namespace cdnwebapp.Controllers
	{
	    public class MemeGeneratorController : Controller
	    {
	        static readonly Dictionary&lt;string, Tuple&lt;string ,string&gt;&gt; Memes = new Dictionary&lt;string, Tuple&lt;string, string&gt;&gt;();

	        public ActionResult Index()
	        {
	            return View();
	        }
	
	        [HttpPost, ActionName(&quot;Index&quot;)]
        	public ActionResult Index_Post(string top, string bottom)
	        {
	            var identifier = Guid.NewGuid().ToString();
	            if (!Memes.ContainsKey(identifier))
	            {
	                Memes.Add(identifier, new Tuple&lt;string, string&gt;(top, bottom));
	            }
	
	            return Content(&quot;&lt;a href=\&quot;&quot; + Url.Action(&quot;Show&quot;, new {id = identifier}) + &quot;\&quot;&gt;here&#39;s your meme&lt;/a&gt;&quot;);
	        }


	        [OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Show(string id)
	        {
	            Tuple&lt;string, string&gt; data = null;
	            if (!Memes.TryGetValue(id, out data))
	            {
	                return new HttpStatusCodeResult(HttpStatusCode.NotFound);
	            }
	
	            if (Debugger.IsAttached) // Preserve the debug experience
	            {
	                return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
	            }
	            else // Get content from Azure CDN
	            {
	                return Redirect(string.Format(&quot;http://<mark>&lt;yourCDNName&gt;</mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}&quot;, data.Item1, data.Item2));
	            }
	        }

	        [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Generate(string top, string bottom)
	        {
	            string imageFilePath = HostingEnvironment.MapPath(&quot;<mark>~/Content/chuck.bmp</mark>&quot;);
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
	            return File(ms.ToArray(), &quot;image/png&quot;);
	        }
	
	        private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
	        {
	            // Compute actual size, shrink if needed
	            while (true)
	            {
	                size = g.MeasureString(text, font);
	
	                // It fits, back out
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

2. Cliquez avec le bouton droit sur l'action  `Index()` par défaut, puis sélectionnez **Ajouter une vue**.

	![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)

3.  Acceptez les paramètres ci-dessous, puis cliquez sur **Ajouter**.

	![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)

4. Ouvrez le nouveau  *Views\MemeGenerator\Index.cshtml*, puis remplacez le contenu par le code HTML suivant pour envoyer les superlatifs :

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. Republiez l'application web Azure, puis accédez à **http://*&lt;nom du service>*.cloudapp.net/MemeGenerator/Index** dans votre navigateur. 

Quand vous envoyez des valeurs de formulaire à  `/MemeGenerator/Index`, la méthode d'action  `Index_Post` renvoie un lien vers la méthode d'action  `Show` avec l'identificateur d'entrée correspondant. Quand vous cliquez sur ce lien, vous accédez au code suivant :  
<pre class="prettyprint">
[OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
public ActionResult Show(string id)
{
    Tuple&lt;string, string&gt; data = null;
    if (!Memes.TryGetValue(id, out data))
    {
        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
    }

    if (Debugger.IsAttached) // Preserve the debug experience
    {
        return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
    }
    else // Get content from Azure CDN
    {
        return Redirect(string.Format(&quot;http://<mark>&lt;yourCDNName&gt;</mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}&quot;, data.Item1, data.Item2));
    }
}
</pre>

Si votre débogueur local est lié, vous effectuez un débogage normal avec une redirection locale. S'il est exécuté dans l'application web Azure, il redirige vers :

	http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Qui correspond à l'URL d'origine suivante au niveau de votre point de terminaison CDN :

	http://<yourSiteName>.azurewebsites.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Après la règle de réécriture de l'URL précédemment appliquée, le fichier qui est réellement placé dans la mémoire cache du point de terminaison de votre CDN est :

	http://<yourSiteName>.azurewebsites.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Vous pouvez alors appliquer l'attribut  `OutputCacheAttribute` à la méthode  `Generate` pour spécifier la manière dont le résultat de l'action doit être mis en cache, et qu'Azure CDN respectera. Le code ci-dessous spécifie 1 heure (3 600 secondes) pour l'expiration du cache.

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

De même, vous pouvez distribuer le contenu de l'action d'un contrôleur dans votre application web Azure via Azure CDN avec l'option de mise en cache voulue.

Dans la section suivante, je vais vous montrer comment distribuer les scripts et les feuilles de style CSS regroupés et minimisés via Azure CDN. 

<a name="bundling"></a>
## Intégration du regroupement et de la minimisation d'ASP.NET avec Azure CDN ##

Les scripts et les feuilles de style CSS, qui ne changent pas fréquemment, sont les principaux candidats pour le cache Azure CDN. La distribution de l'ensemble de l'application web via Azure CDN est la manière la plus facile d'intégrer le regroupement et la minimisation à Azure CDN. Toutefois, comme les raisons évoquées dans [Intégration d'un point de terminaison Azure CDN à votre application web Azure et traitement du contenu statique dans vos pages web depuis Azure CDN](#deploy) peuvent vous convaincre de ne pas procéder ainsi, nous allons voir comment utiliser Azure CDN tout en conservant l'expérience de développeur désirée pour le regroupement et la minimisation ASP.NET, comme par exemple :

-	Mode débogage puissant
-	Facilité de déploiement
-	Mises à jour immédiates à destination des clients lors des mises à niveau des versions des scripts et des feuilles de style CSS
-	Mécanisme de secours en cas de défaillance de votre point de terminaison CDN
-	Modifications minimales du code

Dans le projet ASP.NET que vous avez créé dans [Intégration d'un point de terminaison Azure CDN à votre application web Azure et traitement du contenu statique dans vos pages web depuis Azure CDN](#deploy), ouvrez *App_Start\BundleConfig.cs*, examinez les appels de méthode  `bundles.Add()`.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

La première instruction  `bundles.Add()` ajoute un regroupement de scripts au répertoire virtuel `~/bundles/jquery`. Ouvrez ensuite  *Views\Shared\_Layout.cshtml* pour voir le rendu de la balise du regroupement de scripts. Vous devez trouver la ligne de code Razor suivante :

    @Scripts.Render("~/bundles/jquery")

Quand ce code Razor est exécuté dans l'application web Azure, il restitue une balise `<script>` pour le regroupement de scripts similaire à ce qui suit : 

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Cependant, quand il est exécuté dans Visual Studio en appuyant sur la touche `F5`, il restitue individuellement chaque fichier de script dans le regroupement (dans le cas ci-dessus, un seul fichier de script se trouve dans le regroupement) :

    <script src="/Scripts/jquery-1.10.2.js"></script>

Cela permet de déboguer le code JavaScript dans votre environnement de développement tout en réduisant les connexions clientes simultanées (regroupement) et en améliorant les performances de téléchargement (minimisation) en production. Cette fonctionnalité est très intéressante à conserver avec l'intégration Azure CDN. De plus, du fait que le regroupement restitué contient déjà une chaîne de version générée automatiquement, vous voudrez répliquer cette fonctionnalité de façon que, chaque fois que vous mettrez à jour votre version jQuery via NuGet, il sera possible de la mettre à jour côté client le plus rapidement possible.

Procédez comme suit pour l'intégration du regroupement et de la minimisation ASP.NET à votre point de terminaison CDN.

1. De retour dans  *App_Start\BundleConfig.cs*, modifiez les méthodes  `bundles.Add()` pour utiliser un autre [constructeur de regroupement](http://msdn.microsoft.com/library/jj646464.aspx) qui spécifie une adresse CDN. Pour cela, remplacez la définition de la méthode  `RegisterBundles` par le code suivant :  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    <mark>bundles.UseCdn = true;
	    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://&lt;yourCDNName&gt;.vo.msecnd.net/{0}?v=&quot; + version;</mark>
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;<mark>, string.Format(cdnUrl, &quot;bundles/jquery&quot;)</mark>).Include(
	                &quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;<mark>, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)</mark>).Include(
	                &quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;<mark>, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)</mark>).Include(
	                &quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;<mark>, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)</mark>).Include(
	                &quot;~/Scripts/bootstrap.js&quot;,
	                &quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;<mark>, string.Format(cdnUrl, &quot;Content/css&quot;)</mark>).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}
	</pre>

	N'oubliez pas de remplacer `<yourCDNName>` par le nom de votre réseau de distribution de contenu (CDN) Azure.

	Exprimé simplement, vous configurez  `bundles.UseCdn = true` et ajoutez une URL CDN correctement formatée à chaque regroupement. Par exemple : le premier constructeur dans le code :

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	est identique à : 

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

	Ce constructeur indique au regroupement et à la minimisation ASP.NET de restituer des fichiers de script individuels lorsqu'ils sont débogués localement, mais d'utiliser l'adresse CDN spécifiée pour accéder au script en question. Cependant, notez deux caractéristiques importantes avec cette URL CDN correctement formatée :
	
	-	L'origine de cette URL CDN est  `http://<yourSiteName>.azurewebsites.net/bundles/jquery?v=<W.X.Y.Z>`, qui est le répertoire virtuel du regroupement de scripts dans votre application web.
	-	Comme vous utilisez un constructeur CDN, la balise du script CDN pour le regroupement ne contient plus la chaîne de caractères de la version automatiquement générée dans l'URL restituée. Vous devez créer manuellement une chaîne de version unique à chaque modification du regroupement de script pour forcer une erreur dans le cache de votre réseau de distribution de contenu (CDN) Azure. En même temps, cette chaîne de version unique doit rester constante tout au long du déploiement pour un accès maximal au cache de votre réseau de distribution de contenu (CDN) Azure après le déploiement du regroupement.
	-	La chaîne de requête v=<W.X.Y.Z> est extraite de  *Properties\AssemblyInfo.cs* dans votre projet ASP.NET. Vous pouvez avoir un flux de travail de déploiement qui inclut l'incrémentation de la version de l'assembly chaque fois que vous publiez dans Azure. Vous pouvez également modifier  *Properties\AssemblyInfo.cs* dans votre projet pour incrémenter automatiquement la chaîne de caractères de la version à chaque génération en utilisant le caractère générique " * ". Par exemple :
	
			[assembly: AssemblyVersion("1.0.0.*")]
	
		Toute autre stratégie pour simplifier la génération d'une chaîne unique tout au long d'un déploiement fonctionne dans ce cas.

3. Republiez l'application ASP.NET et accédez à la page d'accueil.
 
4. Affichez le code HTML de la page. Vous devez voir l'URL CDN restituée avec une chaîne de version unique chaque fois que vous republiez des modifications dans votre application web Azure. Par exemple :  
	<pre class="prettyprint">
	...

    &lt;link href=&quot;http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25449&quot; rel=&quot;stylesheet&quot;/&gt;

    &lt;script src=&quot;http://az673227.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

	...

    &lt;script src=&quot;http://az673227.vo.msecnd.net/bundles/jquery?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

    &lt;script src=&quot;http://az673227.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

	...</pre>

5. Dans Visual Studio, appuyez sur la touche `F5` pour déboguer l'application ASP.NET. 

6. Affichez le code HTML de la page. Vous voyez chaque fichier de script restitué individuellement de façon à effectuer un débogage cohérent dans Visual Studio.  
	<pre class="prettyprint">
	...
	
	    &lt;link href=&quot;/Content/bootstrap.css&quot; rel=&quot;stylesheet&quot;/&gt;
	&lt;link href=&quot;/Content/site.css&quot; rel=&quot;stylesheet&quot;/&gt;
	
	    &lt;script src=&quot;/Scripts/modernizr-2.6.2.js&quot;&gt;&lt;/script&gt;
	
	...
	
	    &lt;script src=&quot;/Scripts/jquery-1.10.2.js&quot;&gt;&lt;/script&gt;
	
	    &lt;script src=&quot;/Scripts/bootstrap.js&quot;&gt;&lt;/script&gt;
	&lt;script src=&quot;/Scripts/respond.js&quot;&gt;&lt;/script&gt;
	
	...    
	</pre>

<a name="fallback"></a>
## Mécanisme de secours pour les URL CDN ##

Lorsque le point de terminaison de votre réseau de distribution de contenu (CDN) présente une défaillance pour quelque raison que ce soit, vous voulez que votre page web soit suffisamment bien conçue pour accéder à votre serveur web d'origine comme option de secours pour le chargement de JavaScript ou Bootstrap. C'est une chose de perdre des images dans votre application web à cause d'une indisponibilité CDN, c'en est une autre de perdre les fonctionnalités de page vitales fournies par vos scripts et vos feuilles de style.

La classe [Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) contient la propriété [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) qui vous permet de configurer le mécanisme de secours en cas de défaillance CDN. Pour utiliser cette propriété, procédez comme suit :

1. Dans votre projet ASP.NET, ouvrez   *App_Start\BundleConfig.cs*, où vous avez ajouté une URL CDN dans chaque [constructeur de regroupement](http://msdn.microsoft.com/library/jj646464.aspx), puis apportez les modifications en surbrillance pour ajouter un mécanisme de secours aux regroupements par défaut :  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://cdnurl.vo.msecnd.net/.../{0}?&quot; + version;
	    bundles.UseCdn = true;
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;, string.Format(cdnUrl, &quot;bundles/jquery&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;window.jquery&quot; }</mark>
	                .Include(&quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;$.validator&quot; }</mark>
	            	.Include(&quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;window.Modernizr&quot; }</mark>
					.Include(&quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)) 	
					<mark>{ CdnFallbackExpression = &quot;$.fn.modal&quot; }</mark>
	        		.Include(
		              		&quot;~/Scripts/bootstrap.js&quot;,
		              		&quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;, string.Format(cdnUrl, &quot;Content/css&quot;)).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}</pre>

	Si  `CdnFallbackExpression` n'a pas la valeur Null, le script est injecté dans le code HTML pour tester où le regroupement est correctement chargé et, dans le cas contraire, pour accéder au regroupement directement depuis le serveur web d'origine. Cette propriété doit être configurée avec une expression JavaScript qui teste si le regroupement CDN correspondant est correctement chargé. L'expression nécessaire pour tester chaque regroupement est différente en fonction du contenu. Pour les regroupements par défaut ci-dessus :
	
	-	`window.jquery` est défini dans jquery-{version}.js
	-	`$.validator` est défini dans jquery.validate.js
	-	`window.Modernizr` est défini dans modernizer-{version}.js
	-	`$.fn.modal` est défini dans bootstrap.js
	
	Vous aurez peut-être remarqué que CdnFallbackExpression n'a pas été configuré pour le regroupement `~/Cointent/css`. Cela est dû au fait qu'il existe un [bogue dans System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) qui injecte une balise `<script>` pour le fichier CSS de secours à la place de la balise `<link>` attendue.
	
	Il existe néanmoins un bon [mécanisme de secours pour les styles](https://github.com/EmberConsultingGroup/StyleBundleFallback) proposé par [Ember Consulting Group](https://github.com/EmberConsultingGroup). 

2. Pour utiliser cette solution pour CSS, créez un nouveau fichier .cs dans le dossier  *StyleBundleExtensions.cs* de votre projet ASP.NET  *App_Start*, puis remplacez son contenu par le [code de GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs). 

4. Dans  *App_Start\StyleFundleExtensions.cs*, remplacez le nom de l'espace de noms par celui de votre application ASP.NET (par exemple, **cdnwebapp**). 

3. Revenez à  `App_Start\BundleConfig.cs`, puis remplacez la dernière instruction  `bundles.Add` par le code mis en surbrillance :  
	<pre class="prettyprint">
	bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
	    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
	    .Include(
	          "~/Content/bootstrap.css",
	          "~/Content/site.css"));
	</pre>

	Cette nouvelle méthode d'extension utilise la même idée pour injecter un script dans le code HTML afin de vérifier le DOM pour le nom de classe, le nom de règle et la valeur de règle correspondants dans le regroupement CSS. Elle rétablit le serveur web d'origine si elle ne trouve pas de correspondance.

4. Republiez votre application web Azure et accédez à la page d'accueil. 
5. Affichez le code HTML de la page. Vous devez trouver des scripts injectés similaires à ce qui suit :    
	<pre class="prettyprint">...
	
		&lt;link href=&quot;http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25474&quot; rel=&quot;stylesheet&quot;/&gt;
	<mark>&lt;script&gt;(function() {
	                var loadFallback,
	                    len = document.styleSheets.length;
	                for (var i = 0; i &lt; len; i++) {
	                    var sheet = document.styleSheets[i];
	                    if (sheet.href.indexOf(&#39;http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25474&#39;) !== -1) {
	                        var meta = document.createElement(&#39;meta&#39;);
	                        meta.className = &#39;sr-only&#39;;
	                        document.head.appendChild(meta);
	                        var value = window.getComputedStyle(meta).getPropertyValue(&#39;width&#39;);
	                        document.head.removeChild(meta);
	                        if (value !== &#39;1px&#39;) {
	                            document.write(&#39;&lt;link href=&quot;/Content/css&quot; rel=&quot;stylesheet&quot; type=&quot;text/css&quot; /&gt;&#39;);
	                        }
	                    }
	                }
	                return true;
	            }())||document.write(&#39;&lt;script src=&quot;/Content/css&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	    &lt;script src=&quot;http://az673227.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;(window.Modernizr)||document.write(&#39;&lt;script src=&quot;/bundles/modernizr&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	...	
	
	    &lt;script src=&quot;http://az673227.vo.msecnd.net/bundles/jquery?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;(window.jquery)||document.write(&#39;&lt;script src=&quot;/bundles/jquery&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	    &lt;script src=&quot;http://az673227.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;($.fn.modal)||document.write(&#39;&lt;script src=&quot;/bundles/bootstrap&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	...
	</pre>

	Notez que le script injecté pour le regroupement CSS contient toujours les éléments restants provenant de la propriété  `CdnFallbackExpression` à la ligne :

        }())||document.write('<script src="/Content/css"><\/script>');</script>

	Mais, du fait que la première partie de l'expression || renvoie toujours la valeur true (dans la ligne juste au-dessus), la fonction document.write() ne s'exécute jamais.

6. Pour tester le bon fonctionnement du script de secours, revenez au tableau de bord de votre point de terminaison CDN, puis cliquez sur **Désactiver le point de terminaison**.

	![](media/cdn-websites-with-cdn/13-test-fallback.png)

7. Actualisez votre navigateur pour l'application web Azure. Vous devriez maintenant voir que l'ensemble des scripts et feuilles de style est chargé correctement.

# Informations complémentaires #
- [Vue d'ensemble du réseau de distribution de contenu (CDN) Azure](http://msdn.microsoft.com/library/azure/ff919703.aspx)
- [Distribution de contenu depuis Azure CDN dans votre application web](cdn-serve-content-from-cdn-in-your-web-application.md)
- [Intégration d'un service cloud à Azure CDN](cdn-cloud-service-with-cdn.md)
- [Regroupement et minimisation d'ASP.NET](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
- [Utilisation du réseau de distribution de contenu (CDN) Azure](cdn-how-to-use.md)

<!--HONumber=49-->