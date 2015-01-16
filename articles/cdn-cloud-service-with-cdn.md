<properties urlDisplayName="Integrate a cloud service with Azure CDN" pageTitle="Intégration d'un service cloud à Azure CDN" metaKeywords="Azure tutorial, Azure web app tutorial, ASP.NET, CDN, MVC, cloud service" description="Un didacticiel qui explique comment déployer un service cloud qui traite le contenu à partir d'un point de terminaison CDN Azure intégré" metaCanonical="" services="cdn,cloud-services" documentationCenter=".NET" title="Integrate a cloud service with Azure CDN" authors="cephalin" solutions="" manager="wpickett" editor="tysonn" />

<tags ms.service="cdn" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/02/2014" ms.author="cephalin" />

<a name="intro"></a>
# Intégration d'un service cloud à Azure CDN #

Vous pouvez intégrer un service cloud à Azure CDN pour servir n'importe quel contenu à partir du chemin d'accès `~/CDN` du service. Cette approche offre les avantages suivants :

- Facilité de déploiement et de mise à jour des images, scripts et feuilles de style dans les annuaires de projet du service cloud
- Facilité de mise à niveau des package NuGet dans votre service cloud (par exemple, les versions de jQuery ou de Bootstrap) 
- Gestion de votre application web et de votre contenu CDN, le tout depuis la même interface Visual Studio
- Flux de travail unifié pour le déploiement de votre application web et de votre contenu CDN
- Intégration du regroupement et de la minimisation d'ASP.NET avec Azure CDN

## Contenu ##

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-	[Intégration d'un point de terminaison Azure CDN à votre service cloud et traitement du contenu statique dans vos pages web depuis Azure CDN](#deploy)
-	[Configuration des paramètres du cache pour le contenu statique de votre service cloud](#caching)
-	[Distribution du contenu à partir d'actions de contrôleur via Azure CDN](#controller)
-	[Traitement du contenu regroupé et minimisé via Azure CDN tout en préservant le débogage des scripts dans Visual Studio](#bundling)
-	[Configuration de secours de vos scripts et feuilles de style CSS lorsque votre service Azure CDN est hors connexion](#fallback) 

## Ce que vous allez créer ##

Vous allez déployer un rôle Web de service cloud en utilisant le modèle ASP.NET MVC par défaut, ajouter du code pour distribuer le contenu depuis un service Azure CDN intégré (ex. image, résultats de l'action d'un contrôleur, et fichiers JavaScript et CSS par défaut) et écrire du code pour configurer le mécanisme de secours des lots traités si le service CDN est hors connexion.

## Éléments requis ##

Ce didacticiel nécessite les éléments suivants :

-	Un [compte Microsoft Azure](http://azure.microsoft.com/fr-fr/account/) actif
-	Visual Studio 2013 avec le [Kit de développement logiciel (SDK) Azure](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>Pour suivre ce didacticiel, vous avez besoin d'un compte Azure :</h5>
  <ul>
    <li>Vous pouvez <a href="http://azure.microsoft.com/fr-fr/pricing/free-trial/?WT.mc_id=A261C142F">ouvrir un compte Azure gratuitement</a> : vous obtenez alors des crédits dont vous pouvez vous servir pour tester les services Azure payants, et même lorsqu'ils sont épuisés, vous pouvez conserver le compte et utiliser les services Azure gratuits, notamment Sites Web.</li>
    <li>Vous pouvez <a href="http://azure.microsoft.com/fr-fr/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F">activer les avantages de l'abonnement MSDN</a> : votre abonnement MSDN vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.</li>
  <ul>
</div>

<a name="deploy"></a>
## Déploiement d'un service cloud avec un point de terminaison CDN intégré ##

Dans cette section, vous allez déployer le modèle d'application ASP.NET MVC par défaut dans Visual Studio 2013 dans un rôle web de service cloud, puis l'intégrer à un nouveau point de terminaison CDN. Procédez comme suit :

1. Dans Visual Studio 2013, créez un service cloud Azure depuis la barre de menus : **Fichier > Nouveau > Projet > Cloud > Windows Azure Cloud Service**. Attribuez-lui un nom et cliquez sur **OK**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)

2. Sélectionnez **Rôle Web ASP.NET**, puis cliquez sur le bouton **>**. Cliquez sur OK.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)

3. Sélectionnez **MVC** et cliquez sur **OK**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)

4. Publiez ce rôle web dans un service cloud Azure. Cliquez avec le bouton droit sur le projet de service cloud et sélectionnez **Publier**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

5. Si vous n'êtes pas encore connecté à Microsoft Azure, cliquez sur le bouton **Se connecter**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)

6. Dans la page de connexion, connectez-vous au compte Microsoft que vous avez utilisé pour activer votre compte Azure.
7. Lorsque vous êtes connecté, cliquez sur **Suivant**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)

8. En supposant que vous n'avez pas créé de compte de service cloud ou de stockage, Visual Studio vous aide à créer les deux. Dans la boîte de dialogue **Créer un service cloud et un compte**, tapez le nom souhaité pour le service. Puis, cliquez sur **Créer**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)

9. Dans la page des paramètres de publication, vérifiez la configuration et cliquez sur **Publier**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)

	>[WACOM.NOTE] La publication des services cloud prend beaucoup de temps. L'option Activer Web Deploy pour tous les rôles accélère notablement le débogage de votre service cloud en fournissant des mises à jour rapides (mais provisoires) à vos rôles web. Pour en savoir plus sur cette option, consultez la page [Publication d'un service cloud en utilisant les Outils Azure](http://msdn.microsoft.com/fr-fr/library/ff683672.aspx).

	Lorsque le **Journal des activités Windows Azure** indique que la publication est **Terminée**, vous allez créer un point de terminaison CDN intégré à ce service cloud. 

1. Pour créer un point de terminaison CDN, connectez-vous à votre [Portail de gestion Azure](http://manage.windowsazure.com/). 
2. Cliquez sur **Nouveau** > **App Services** > **CDN** > **Création rapide**. Sélectionnez **http://*<servicename>*.cloudapp.net/cdn/** et cliquez sur **Créer**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-10-createcdn.png)

	>[WACOM.NOTE] Lorsque votre point de terminaison CDN est créé, le portail Azure affiche son URL et le domaine d'origine auquel il est intégré. Cependant, la configuration du nouveau point de terminaison CDN peut prendre un certain temps pour se propager à tous les emplacements des nœuds CDN. 

	Notez que le point de terminaison CDN est lié au chemin **cdn/** de votre service cloud. Vous pouvez créer un dossier **cdn** dans votre projet **WebRole1** ou (comme c'est le cas dans ce didacticiel) utiliser la réécriture de l'URL pour éliminer tous les liens entrants de ce chemin.

3. De retour dans le portail Azure, dans l'onglet **CDN**, cliquez sur le nom du point de terminaison CDN que vous venez de créer.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-11-disablequerya.png)

3. Cliquez sur **Activer la chaîne de requête** pour activer les chaînes de requête dans le cache CDN. Lorsque vous activez cette fonction, un lien identique accessible avec différentes chaînes de requête sera mis en cache avec des entrées différentes.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-12-disablequeryb.png)

	>[WACOM.NOTE] Comme l'activation des chaînes de requête n'est pas nécessaire pour cette partie du didacticiel, désactivez-la au plus tôt par commodité, car la propagation à tous les nœuds CDN de toute modification apportée ici prend du temps et le contenu non activé pour les chaînes de requête ne doit pas encombrer le cache CDN (la mise à jour du contenu CDN est abordée plus tard).

3. Appliquez une commande ping à votre point de terminaison CDN pour vérifier qu'il est propagé à tous les nœuds CDN. Vous devrez peut-être attendre pendant une heure maximum avant qu'il réponde à vos commandes ping.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-13-testcdn.png)

2. De retour dans Visual Studio 2013, ouvrez **Web.config** dans votre projet **WebRole1** et ajoutez le code suivant dans la balise `<system.webServer>` :  
	<pre class="prettyprint">
	<system.webServer>
	  <mark><rewrite>
	    <rules>
	      <rule name=&quot;RewriteIncomingCdnRequest&quot; stopProcessing=&quot;true&quot;>
	        <match url=&quot;^cdn/(.*)$&quot;/>
	        <action type=&quot;Rewrite&quot; url=&quot;{R:1}&quot;/>
	      </rule>
	    </rules>
	  </rewrite></mark>
      ...
	</system.webServer>
	</pre>

4. Republiez le service cloud. Cliquez avec le bouton droit sur le projet de service cloud et sélectionnez **Publier**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

1. Lorsque l'état de la publication est **Terminée**, ouvrez une fenêtre de navigateur et accédez à **http://*<cdnName>*.vo.msecnd.net/Content/bootstrap.css**. Dans ma configuration, cette URL est la suivante :

		http://az632148.vo.msecnd.net/Content/bootstrap.css

	Elle correspond à l'URL d'origine suivante au niveau du point de terminaison CDN :

		http://cephalinservice.cloudapp.net/cdn/Content/bootstrap.css

	Après la réécriture de l'URL dans mon application web, voici le fichier qui est réellement placé dans la mémoire cache de mon CDN :

		http://cephalinservice.cloudapp.net/Content/bootstrap.css

	Lorsque vous atteignez la page **http://*<cdnName>*.vo.msecnd.net/Content/bootstrap.css**, vous êtes invité à télécharger le fichier bootstrap.css provenant de votre application web publiée. 

	![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

De même, vous pouvez accéder à toute URL accessible publiquement à l'adresse **http://*<serviceName>*.cloudapp.net/**, directement depuis votre point de terminaison CDN. Par exemple :

-	un fichier .js à partir du chemin d'accès /Script ;
-	un fichier de contenu à partir du chemin d'accès /Content ;
-	un contrôleur/une action ; 
-	si la chaîne de requête est activée sur le point de terminaison CDN, une URL avec des chaînes de requête.

En fait, dans la configuration ci-dessus, vous pouvez héberger l'ensemble du service cloud à partir de **http://*<cdnName>*.vo.msecnd.net/**. Si vous vous rendez à l'adresse **http://az632148.vo.msecnd.net/**, vous obtenez le résultat de l'action à partir de Home/Index.

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

Cependant, cela ne signifie pas que c'est toujours (ou généralement) une bonne idée pour traiter l'ensemble du service cloud via Azure CDN. Mises en garde éventuelles :

-	Cette approche nécessite que l'ensemble de votre site soit public, car Azure CDN ne peut pas traiter du contenu privé.
-	Si le point de terminaison CDN passe hors connexion quelle qu'en soit la raison (maintenance prévue ou erreur utilisateur), l'ensemble de votre service cloud passe hors connexion à moins qu'il soit possible de rediriger les clients vers l'URL d'origine **http://*<serviceName>*.cloudapp.net/**. 
-	Même avec les paramètres Cache-Control personnalisés (voir [Configuration des options de mise en cache des fichiers statiques dans votre service cloud](#caching)), un point de terminaison CDN n'améliore pas les performances des contenus très dynamiques. Si vous avez essayé de charger la page d'accueil depuis votre point de terminaison CDN (voir ci-dessus), notez qu'il faut environ 5 secondes pour charger la page d'accueil par défaut la première fois, alors qu'il s'agit d'une page relativement simple. Imaginez ce qui se passe du côté client si cette page comporte un contenu dynamique qui doit s'actualiser toutes les minutes. Le traitement du contenu dynamique depuis un point de terminaison CDN nécessite une brève expiration du cache qui se traduit en fréquentes erreurs dans le cache au niveau du point de terminaison. Cela nuit aux performances ou à votre service cloud et va à l'encontre de la fonction d'un réseau de distribution de contenu (CDN).

La solution alternative consiste à déterminer au cas par cas le contenu à traiter à partir d'Azure CDN dans votre service cloud. À cette fin, vous avez déjà vu comment accéder à des fichiers de contenu individuels depuis le point de terminaison CDN. Je vais vous montrer comment traiter une action sur un contrôleur donné via le point de terminaison CDN dans la section [Distribution de contenu à partir d'actions de contrôleur via Azure CDN](#controller).

Vous pouvez spécifier une règle de réécriture de l'URL plus restrictive pour limiter le contenu accessible via votre point de terminaison CDN. Par exemple : pour limiter la réécriture de l'URL dans le dossier *\Scripts*, modifiez la règle de réécriture ci-dessus comme suit :   
<pre class="prettyprint">
<rule name=&quot;RewriteIncomingCdnRequest&quot; stopProcessing=&quot;true&quot;>
  <match url=&quot;^cdn/<mark>Scripts/</mark>(.*)$&quot;/>
  <action type=&quot;Rewrite&quot; url=&quot;<mark>Scripts/</mark>{R:1}&quot;/>
</rule>
</pre>

<a name="caching"></a>
## Configuration des options de mise en cache pour les fichiers statiques de votre service cloud ##

Avec l'intégration d'Azure CDN à votre service cloud, vous pouvez spécifier comment vous voulez que le contenu statique soit mis en cache dans le point de terminaison CDN. Pour cela, ouvrez *Web.config* à partir de votre projet de rôle web (par exemple, WebRole1) et ajoutez l'élément `<staticContent>` à `<system.webServer>`. Le code XML ci-dessous configure l'expiration du cache dans 3 jours.  
<pre class="prettyprint">
<system.webServer>
  <mark><staticContent>
    <clientCache cacheControlMode=&quot;UseMaxAge&quot; cacheControlMaxAge=&quot;3.00:00:00&quot;/>
  </staticContent></mark>
  ...
</system.webServer>
</pre>

Lorsque vous faites cela, tous les fichiers statiques de votre service cloud respectent la même règle dans le cache de votre CDN. Pour un contrôle plus granulaire des paramètres du cache, ajoutez un fichier *Web.config* dans un dossier et ajoutez-lui vos paramètres. Par exemple, ajoutez un fichier *Web.config* au répertoire *\Content* et remplacez son contenu par le code XML suivant :

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

Ce paramètre entraîne la mise en cache de tous les fichiers statiques du dossier *\Content* pendant 15 jours.

Pour plus d'informations sur la configuration de l'élément `<clientCache>`, consultez le [cache client <clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

Dans la rubrique [Distribution de contenu à partir d'actions de contrôleur via Azure CDN](#controller), je vais également vous montrer comment configurer les paramètres du cache pour les résultats de l'action du contrôleur dans le cache du CDN.

<a name="controller"></a>
## Distribution du contenu à partir d'actions de contrôleur via Azure CDN ##

Lorsque vous intégrez un rôle web de service cloud à Azure CDN, il est relativement facile de distribuer du contenu à partir d'actions de contrôleur via le réseau de distribution de contenu (CDN) Azure. En dehors du traitement de votre service cloud directement via Azure CDN (démontré ci-dessus), [Maarten Balliauw](https://twitter.com/maartenballiauw) vous démontre comment le faire avec le contrôleur MemeGenerator à la page [Réduction de la latence sur Internet avec le réseau de distribution de contenu (CDN) Microsoft Azure](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN). Je reproduis simplement cette page ici.

Supposez que, dans votre service cloud, vous vouliez créer des mèmes basés sur une image de Chuck Norris jeune (photo de [Alan Light](http://www.flickr.com/photos/alan-light/218493788/)) comme ceci :

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Une simple action `Index` permet aux clients de spécifier les superlatifs dans l'image, puis de créer le mème lorsqu'ils publient vers l'action. Comme il s'agit de Chuck Norris, vous espérez que cette page devienne extrêmement populaire dans le monde entier. Cet exemple illustre bien la distribution de contenu semi-dynamique avec Azure CDN. 

Procédez comme ci-dessus pour configurer cette action de contrôleur :

1. Dans le répertoire *\Controllers*, créez un fichier .cs nommé *MemeGeneratorController.cs* et remplacez son contenu par le code suivant. N'oubliez pas de remplacer la partie en surbrillance par le nom de votre CDN.  
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
	        static readonly Dictionary<string, Tuple<string ,string&gt;> Memes = new Dictionary<string, Tuple<string, string&gt;>();

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
	                Memes.Add(identifier, new Tuple<string, string>(top, bottom));
	            }
	
	            return Content(&quot;<a href=\&quot;&quot; + Url.Action(&quot;Show&quot;, new {id = identifier}) + &quot;\&quot;>here&#39;s your meme</a>&quot;);
	        }


	        [OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Show(string id)
	        {
	            Tuple<string, string> data = null;
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
	                return Redirect(string.Format(&quot;http://<mark><yourCdnName></mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
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
	                if (size.Height < i.Height &&
	                     size.Width < i.Width) { return font; }
	
	                // Try a smaller font (90% of old size)
	                Font oldFont = font;
	                font = new Font(font.Name, (float)(font.Size * .9), font.Style);
	                oldFont.Dispose();
	            }
	        }
	    }
	}
	</pre>

2. Cliquez avec le bouton droit sur l'action `Index()` par défaut, puis sélectionnez **Ajouter une vue**.

	![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)

3.  Acceptez les paramètres ci-dessous et cliquez sur **Ajouter**.

	![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)

4. Ouvrez le nouveau fichier *Views\MemeGenerator\Index.cshtml* et remplacez son contenu par le simple code HTML suivant, qui permet d'envoyer des superlatifs :

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. Republiez le service cloud et accédez à l'adresse **http://*<serviceName>*.cloudapp.net/MemeGenerator/Index** dans votre navigateur. 

Lorsque vous envoyez les valeurs du formulaire dans `/MemeGenerator/Index`, la méthode d'action `Index_Post` renvoie un lien vers la méthode d'action `Show` avec l'identificateur d'entrée correspondant. Lorsque vous cliquez sur ce lien, vous atteignez le code suivant :  
<pre class="prettyprint">
[OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
public ActionResult Show(string id)
{
    Tuple<string, string> data = null;
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
        return Redirect(string.Format(&quot;http://<mark><cdnName></mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
    }
}
</pre>

Si votre débogueur local est lié, vous effectuez un débogage normal avec une redirection locale. S'il est exécuté dans le service cloud, il redirige vers :

	http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Qui correspond à l'URL d'origine suivante au niveau de votre point de terminaison CDN :

	http://<youCloudServiceName>.cloudapp.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Après la règle de réécriture de l'URL précédemment appliquée, le fichier qui est réellement placé dans la mémoire cache du point de terminaison de votre CDN est :

	http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Vous pouvez alors appliquer l'attribut `OutputCacheAttribute` à la méthode `Generate` pour spécifier la manière dont l'action doit être mise en cache, et qu'Azure CDN respectera. Le code ci-dessous spécifie 1 heure (3 600 secondes) pour l'expiration du cache.

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

De même, vous pouvez distribuer le contenu de l'action d'un contrôleur dans votre service cloud via le réseau de distribution de contenu (CDN) Azure avec l'option de mise en cache voulue.

Dans la section suivante, je vais vous montrer comment distribuer les scripts et les feuilles de style CSS regroupés et minimisés via Azure CDN. 

<a name="bundling"></a>
## Intégration du regroupement et de la minimisation d'ASP.NET avec Azure CDN ##

Les scripts et les feuilles de style CSS, qui ne changent pas fréquemment, sont les principaux candidats pour le cache Azure CDN. La distribution de l'ensemble du rôle web via votre réseau de distribution de contenu (CDN) Azure est la manière la plus facile d'intégrer le regroupement et la minimisation à Azure CDN. Cependant, comme vous ne voudrez peut-être pas faire cela, je vais vous montrer comment le faire en conservant l'approche souhaitée par le développeur concernant le regroupement et la minimisation ASP.NET, par exemple :

-	Mode débogage puissant
-	Facilité de déploiement
-	Mises à jour immédiates à destination des clients lors des mises à niveau des versions des scripts et des feuilles de style CSS
-	Mécanisme de secours en cas de défaillance de votre point de terminaison CDN
-	Modifications minimales du code

Dans le projet **WebRole1** créé durant l'étape [Intégration d'un point de terminaison Azure CDN à votre service cloud et traitement du contenu statique dans vos pages web depuis Azure CDN](#deploy), ouvrez *App_Start\BundleConfig.cs* et observez les appels de la méthode `bundles.Add()`.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

La première instruction `bundles.Add()` ajout un regroupement de scripts au répertoire virtuel `~/bundles/jquery`. Ouvrez ensuite le fichier *Views\Shared\_Layout.cshtml* pour voir comment la balise de regroupement de script est rendue. Vous devez trouver la ligne de code Razor suivante :

    @Scripts.Render("~/bundles/jquery")

Lorsque ce code Razor est exécuté dans le rôle web Azure, il restitue une balise `<script>` pour le regroupement du script similaire à ce qui suit : 

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Cependant, lorsqu'il est exécuté dans Visual Studio en tapant `F5`, il restitue individuellement chaque fichier de script dans le regroupement (dans le cas ci-dessus, un seul fichier de script se trouve dans le regroupement) :

    <script src="/Scripts/jquery-1.10.2.js"></script>

Cela permet de déboguer le code JavaScript dans votre environnement de développement tout en réduisant les connexions clientes simultanées (regroupement) et en améliorant les performances de téléchargement (minimisation) en production. Cette fonctionnalité est très intéressante à conserver avec l'intégration Azure CDN. De plus, du fait que le regroupement restitué contient déjà une chaîne de version générée automatiquement, vous voudrez répliquer cette fonctionnalité de façon que, chaque fois que vous mettrez à jour votre version jQuery via NuGet, il sera possible de la mettre à jour côté client le plus rapidement possible.

Procédez comme suit pour l'intégration du regroupement et de la minimisation ASP.NET à votre point de terminaison CDN.

1. Une fois revenu dans *App_Start\BundleConfig.cs*, modifiez les méthodes `bundles.Add()` pour utiliser un autre [constructeur de regroupement](http://msdn.microsoft.com/fr-fr/library/jj646464.aspx), qui spécifie une adresse CDN. Pour cela, remplacez la définition de la méthode `RegisterBundles` par le code suivant :  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    <mark>bundles.UseCdn = true;
	    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://<yourCDNName>.vo.msecnd.net/{0}?v=&quot; + version;</mark>
	
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

	Pour exprimer les choses clairement, vous configurez `bundles.UseCdn = true` et ajoutez une URL CDN formatée correctement à chaque regroupement. Par exemple : le premier constructeur dans le code :

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	est identique à : 

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

	Ce constructeur indique au regroupement et à la minimisation ASP.NET de restituer des fichiers de script individuels lorsqu'ils sont débogués localement, mais d'utiliser l'adresse CDN spécifiée pour accéder au script en question. Cependant, notez deux caractéristiques importantes avec cette URL CDN correctement formatée :
	
	-	L'origine de cette URL CDN est `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`, qui correspond au répertoire virtuel du regroupement de script dans votre service cloud.
	-	Comme vous utilisez un constructeur CDN, la balise du script CDN pour le regroupement ne contient plus la chaîne de caractères de la version automatiquement générée dans l'URL restituée. Vous devez créer manuellement une chaîne de version unique à chaque modification du regroupement de script pour forcer une erreur dans le cache de votre réseau de distribution de contenu (CDN) Azure. En même temps, cette chaîne de version unique doit rester constante tout au long du déploiement pour un accès maximal au cache de votre réseau de distribution de contenu (CDN) Azure après le déploiement du regroupement.
	-	La chaîne de requête v=<W.X.Y.Z> est extraite de *Properties\AssemblyInfo.cs* dans votre projet de rôle web. Vous pouvez avoir un flux de travail de déploiement qui inclut l'incrémentation de la version de l'assembly chaque fois que vous publiez dans Azure. Vous pouvez également modifier *Properties\AssemblyInfo.cs* dans votre projet pour incrémenter automatiquement la chaîne de caractères de la version à chaque génération en utilisant le caractère générique '*'. Par exemple :
	
			[assembly: AssemblyVersion("1.0.0.*")]
	
		Toute autre stratégie pour simplifier la génération d'une chaîne unique tout au long d'un déploiement fonctionne dans ce cas.

3. Republiez le service cloud et accédez à la page d'accueil.
 
4. Affichez le code HTML de la page. Vous devez voir l'URL CDN restituée avec une chaîne de version unique chaque fois que vous republiez des modifications dans votre service cloud. Par exemple :  
	<pre class="prettyprint">
	...

    &lt;link href=&quot;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25449&quot; rel=&quot;stylesheet&quot;/&gt;

    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

	...

    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

	...</pre>

5. Dans Visual Studio, appuyez sur la touche `F5`pour déboguer le service cloud. 

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

Lorsque le point de terminaison de votre réseau de distribution de contenu (CDN) présente une défaillance pour quelque raison que ce soit, vous voulez que votre page web soit suffisamment bien conçue pour accéder à votre serveur web d'origine comme option de secours pour le chargement de JavaScript ou Bootstrap. C'est une chose de perdre des images sur votre site web à cause d'une indisponibilité CDN, c'en est une autre de perdre dans une page une fonctionnalité vitale fournie par vos scripts et vos feuilles de style.

La classe [Bundle](http://msdn.microsoft.com/fr-fr/library/system.web.optimization.bundle.aspx) contient la propriété [CdnFallbackExpression](http://msdn.microsoft.com/fr-fr/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) qui vous permet de configurer le mécanisme de secours en cas de défaillance CDN. Pour utiliser cette propriété, procédez comme suit :

1. Dans votre projet de rôle web, ouvrez *App_Start\BundleConfig.cs*, où vous avez ajouté une URL CDN dans chaque [constructeur de regroupement](http://msdn.microsoft.com/fr-fr/library/jj646464.aspx), puis apportez les modifications en surbrillance pour ajouter un mécanisme de secours aux regroupements par défaut :  
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

	Lorsque `CdnFallbackExpression` n'a pas la valeur null, le script est injecté dans le code HTML pour tester où le regroupement est correctement chargé et, dans le cas contraire, pour accéder au regroupement directement depuis le serveur web d'origine. Cette propriété doit être configurée avec une expression JavaScript qui teste si le regroupement CDN correspondant est correctement chargé. L'expression nécessaire pour tester chaque regroupement est différente en fonction du contenu. Pour les regroupements par défaut ci-dessus :
	
	-	`window.jquery` est défini dans jquery-{version}.js
	-	`$.validator` est défini dans jquery.validate.js
	-	`window.Modernizr` est défini dans modernizer-{version}.js
	-	`$.fn.modal` est défini dans bootstrap.js
	
	Vous aurez peut-être remarqué que je n'ai pas configuré CdnFallbackExpression pour le regroupement `~/Cointent/css`. Cela est dû au fait qu'un [bogue dans System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) injecte une balise `<script>` pour le fichier CSS de secours à la place de la balise `<link>` attendue.
	
	Il existe néanmoins un bon [mécanisme de secours pour les styles](https://github.com/EmberConsultingGroup/StyleBundleFallback) proposé par [Ember Consulting Group](https://github.com/EmberConsultingGroup). 

2. Pour utiliser cette solution pour le CSS, créez un fichier .cs dans le répertoire *App_Start*, en le nommant *StyleBundleExtensions.cs*, puis remplacez son contenu par du [code issu de GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs). 

4. Dans *App_Start\StyleFundleExtensions.cs*, renommez l'espace de noms avec le nom de votre rôle web (par exemple, **WebRole1**). 

3. Revenez à `App_Start\BundleConfig.cs` et remplacez la dernière instruction `bundles.Add` par le code en surbrillance suivant :  
	<pre class="prettyprint">
	bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
	    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
	    .Include(
	          "~/Content/bootstrap.css",
	          "~/Content/site.css"));
	</pre>

	Cette nouvelle méthode d'extension utilise la même idée pour injecter un script dans le code HTML afin de vérifier le DOM pour le nom de classe, le nom de règle et la valeur de règle correspondants dans le regroupement CSS. Elle rétablit le serveur web d'origine si elle ne trouve pas de correspondance.

4. Republiez le service cloud et accédez à la page d'accueil. 
5. Affichez le code HTML de la page. Vous devez trouver des scripts injectés similaires à ce qui suit :    
	<pre class="prettyprint">...
	
		&lt;link href=&quot;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474&quot; rel=&quot;stylesheet&quot;/&gt;
	<mark>&lt;script&gt;(function() {
	                var loadFallback,
	                    len = document.styleSheets.length;
	                for (var i = 0; i &lt; len; i++) {
	                    var sheet = document.styleSheets[i];
	                    if (sheet.href.indexOf(&#39;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474&#39;) !== -1) {
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
	
	    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;(window.Modernizr)||document.write(&#39;&lt;script src=&quot;/bundles/modernizr&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	...	
	
	    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;(window.jquery)||document.write(&#39;&lt;script src=&quot;/bundles/jquery&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;($.fn.modal)||document.write(&#39;&lt;script src=&quot;/bundles/bootstrap&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	...
	</pre>

	Notez que le script injecté pour le regroupement CSS contient toujours les éléments restants provenant de la propriété `CdnFallbackExpression` dans la ligne :

        }())||document.write('<script src="/Content/css"><\/script>');</script>

	Mais, du fait que la première partie de l'expression || renvoie toujours la valeur true (dans la ligne juste au-dessus), la fonction document.write() ne s'exécute jamais.

# Informations complémentaires #
- [Vue d'ensemble du réseau de distribution de contenu (CDN) Azure](http://msdn.microsoft.com/library/azure/ff919703.aspx)
- [Distribution de contenu depuis Azure CDN dans votre application web](http://azure.microsoft.com/fr-fr/Documentation/Articles/cdn-serve-content-from-cdn-in-your-web-application/)
- [Intégration d'un site web Azure au CDN Azure](http://azure.microsoft.com/fr-fr/documentation/articles/cdn-websites-with-cdn/)
- [Regroupement et minimisation d'ASP.NET](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
- [Utilisation du réseau de distribution de contenu (CDN) Azure](http://azure.microsoft.com/fr-fr/documentation/articles/cdn-how-to-use/)
