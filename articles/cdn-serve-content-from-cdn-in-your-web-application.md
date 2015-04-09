<properties 
	pageTitle="Utiliser du contenu provenant d'un CDN dans votre application web" 
	description="Un didacticiel qui explique comment utiliser le contenu d'un CDN pour améliorer les performances de votre application web." 
	services="cdn" 
	documentationCenter=".net" 
	authors="cephalin" 
	Gestionnaire = " wpickett " 
	editor="tysonn"/>

<tags 
	ms.service="cdn" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="cephalin"/>

# Distribution de contenu depuis Azure CDN dans votre application web #

Ce didacticiel vous montre comment profiter d'Azure CDN pour améliorer la portée et les performances de votre application web. Azure CDN améliore les performances de votre application web dans les cas suivants :

- Vous avez de nombreux liens vers du contenu statique ou semi-statique dans vos pages.
- Vos clients accèdent à votre application depuis le monde entier.
- Vous voulez décharger le trafic en provenance de votre serveur web.
- Vous voulez réduire le nombre de connexions clientes simultanées à votre serveur web (ce point est traité en détail dans la rubrique [Regroupement et minimisation](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)). 
- Vous voulez augmenter les temps perçus de chargement/d'actualisation de vos pages.

## Contenu ##

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-	[Distribution de contenu statique provenant d'un point de terminaison Azure CDN](#deploy)
-	[Automatisation du téléchargement du contenu de votre application ASP.NET vers votre point de terminaison CDN](#upload)
-	[Configuration du cache du réseau de distribution de contenu (CDN) pour refléter la mise à jour de contenu souhaitée](#update)
-	[Distribution immédiate du nouveau contenu au moyen de chaînes de requête](#query)

## Éléments requis ##

Ce didacticiel nécessite les éléments suivants :

-	Un [compte Microsoft Azure](/account/) Vous pouvez demander un compte d'évaluation gratuit.
-	Visual Studio 2013 avec un [Kit de développement logiciel (SDK) Azure](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) pour des GUI de gestion d'objets blob
-	[Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409) (utilisé dans la section [Automatisation du téléchargement du contenu de votre application ASP.NET vers votre point de terminaison CDN](#upload))

> [AZURE.NOTE] Pour suivre ce didacticiel, vous avez besoin d'un compte Azure :
> + Vous pouvez [ouvrir un compte Azure gratuitement] (/pricing/free-trial/?WT.mc_id=A261C142F) : vous obtenez alors des crédits dont vous pouvez vous servir pour tester les services Azure payants, et même quand ils sont épuisés, vous pouvez conserver le compte et utiliser les services Azure gratuits, notamment Websites.
> + Vous pouvez [activer les avantages de l'abonnement MSDN] (/pricing/member-offers/msdn-benefits-details/) : votre abonnement MSDN vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.

<a name="static"></a>
## Distribution de contenu statique provenant d'un point de terminaison Azure CDN

Dans cette section du didacticiel, vous allez apprendre à créer un réseau de distribution de contenu (CDN) et l'utiliser pour distribuer votre contenu statique. Voici les principales opérations :

1. création un compte de stockage ;
2. création d'un CDN lié au compte de stockage ;
3. création d'un conteneur d'objets blob dans votre compte de stockage ;
4. téléchargement de contenu dans votre conteneur d'objets blob ;
5. liaison au contenu que vous avez téléchargé en utilisant son URL CDN.

Commençons. Procédez comme suit pour commencer à utiliser le réseau de distribution de contenu (CDN) Azure :

1. Pour créer un point de terminaison CDN, connectez-vous à votre [portail de gestion Azure](http://manage.windowsazure.com/). 
1. Cliquez sur **Nouveau > Data Services > Stockage > Création rapide**. Spécifiez une URL, un emplacement et cliquez sur **Créer un compte de stockage**. 

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-1.PNG)

	>[AZURE.NOTE] Notez que j'utilise East Asia (Asie orientale) comme région, car elle est suffisamment lointaine pour que je teste plus tard mon réseau CDN depuis l'Amérique du Nord.

2. Quand l'état du nouveau compte de stockage est **En ligne**, créez un point de terminaison CDN lié au compte de stockage que vous avez créé. Cliquez sur **Nouveau > Services d'application > CDN > Création rapide**. Sélectionnez le compte de stockage que vous avez créé et cliquez sur **Créer**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2.PNG)

	>[AZURE.NOTE] Quand votre CDN est créé, le portail Azure affiche son URL et le domaine d'origine auquel il est lié. Cependant, la configuration du point de terminaison CDN peut prendre un certain temps pour se propager à tous les emplacements des nœuds.  

3. Appliquez une commande ping à votre point de terminaison CDN pour vérifier qu'il est en ligne. Si votre point de terminaison n'est pas propagé à tous les nœuds, un message similaire à celui-ci s'affiche.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-fail.PNG)

	Attendez une heure et réessayez. Lorsque la propagation de votre point de terminaison CDN est terminée, celui-ci doit répondre aux commandes ping.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-succeed.PNG)

4. Vous pouvez alors déjà voir où le point de terminaison CDN se trouve le plus près de vous. Sur mon ordinateur de bureau, l'adresse IP qui répond est **93.184.215.201**. Liez-la à un site tel que [www.ip-address.org](http://www.ip-address.org) : vous constatez que le serveur se trouve à Washington D.C.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-4.PNG)

	Pour obtenir la liste des emplacements de tous les nœuds CDN, consultez la page [Emplacements des nœuds CDN Azure](http://msdn.microsoft.com/library/azure/gg680302.aspx).

3. De retour dans le portail Azure, sous l'onglet **CDN**, cliquez sur le nom du point de terminaison CDN que vous venez de créer.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequerya.PNG)

3. Cliquez sur **Activer la chaîne de requête** pour activer les chaînes de requête dans le cache Azure CDN. Lorsque vous activez cette fonction, un lien identique accessible avec différentes chaînes de requête sera mis en cache avec des entrées différentes.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequeryb.PNG)

	>[AZURE.NOTE] Alors que l'activation des chaînes de requête n'est pas nécessaire pour cette partie du didacticiel, vous voudrez la désactiver au plus tôt par commodité du fait que toute modification apportée ici prend du temps à se propager aux autres nœuds et que vous ne voulez pas que du contenu non activé pour les chaînes de requête encombre le cache CDN (la mise à jour du contenu CDN est abordée plus tard). Pour savoir comment tirer parti de cette possibilité, consultez la section [Distribution immédiate du nouveau contenu au moyen de chaînes de requête](#query).

6. Dans l'Explorateur de serveurs de Visual Studio 2013, cliquez sur le bouton **Se connecter à Microsoft Azure**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-5.PNG)

7.  Suivez les instructions de l'invite pour vous connecter à votre compte Azure. 
8.  Quand vous êtes connecté, développez **Microsoft Azure > Stockage > votre compte de stockage**. Cliquez avec le bouton droit sur **Objet blob** et sélectionnez **Créer un conteneur d'objets blob**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-6.PNG)

8.	Spécifiez un nom pour le conteneur et cliquez sur **OK**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-7.PNG)

9.	Dans l'Explorateur de serveurs, double-cliquez sur le conteneur d'objets blob que vous avez créé pour le gérer. L'interface de gestion doit s'afficher dans le volet central.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-8.PNG)

10.	Cliquez sur le bouton **Télécharger l'objet blob** pour télécharger les images, les scripts ou les feuilles de style utilisés par vos pages web dans le conteneur d'objets blob. La progression du téléchargement est indiquée dans le **Journal des activités Azure** ; les objets blob s'affichent dans la vue du conteneur quand ils sont téléchargés. 

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-9.PNG)

11.	Notez que, lorsque vous avez téléchargé les objets blob, vous devez les rendre publics pour que vous puissiez y accéder. Dans l'Explorateur de serveurs, cliquez avec le bouton droit sur le conteneur, puis sélectionnez **Propriétés**. Configurez la propriété **Accès public en lecture** sur **Objet blob**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-10.PNG)

12.	Dans un navigateur, accédez à l'URL d'un des objets blob pour tester leur accès public. Exemple : je peux tester la première image dans ma liste téléchargée avec  `http://cephalinstorage.blob.core.windows.net/cdn/cephas_lin.png`.

	Notez que je n'utilise pas l'adresse HTTPS fournie dans l'interface de gestion des objets blob Visual Studio. En utilisant le protocole HTTP, vous testez si le contenu est publiquement accessible, ce qui est indispensable pour Azure CDN.

13.	Si vous constatez que l'objet blob est correctement restitué dans votre navigateur, remplacez l'URL `http://<yourStorageAccountName>.blob.core.windows.net` par l'URL de votre réseau CDN Azure. Dans mon cas, pour tester la première image de mon point de terminaison CDN, j'utilise `http://az623979.vo.msecnd.net/cdn/cephas_lin.png`.

	>[AZURE.NOTE] L'URL du point de terminaison CDN figure dans l'onglet CDN du portail de gestion Azure.

	Si vous comparez les performances de l'accès direct aux objets blob et de l'accès CDN, vous constatez un gain de performances avec Azure CDN. L'image ci-dessous représente la capture d'écran des Outils de développement F12 d'Internet Explorer 11 pour l'accès à l'URL d'objet blob de mon image :

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-blob.PNG)

	et pour l'accès à l'URL CDN de la même image 

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-cdn.PNG)

 	Faites attention aux nombres indiqués pour la durée de la **demande** qui représente le temps jusqu'au premier octet ou le temps nécessaire entre l'envoi de la demande et la réception de la réponse du serveur. Lorsque j'accède à l'objet blob hébergé dans la région East Asia, il me faut 266 ms du fait que la demande doit traverser l'Océan Pacifique juste pour atteindre le serveur. Cependant, quand j'accède au réseau CDN Azure, cela ne prend que 16 ms, soit une **amélioration des performances pratiquement multipliée par 20** !
	
15.	Il ne s'agit plus maintenant que d'utiliser le nouveau lien dans votre page web. Exemple : je peux ajouter la balise d'image suivante :

		<img alt="Mugshot" src="http://az623979.vo.msecnd.net/cdn/cephas_lin.png" />

Dans cette section, vous avez appris à créer un point de terminaison CDN, à lui télécharger du contenu et à le lier au contenu CDN d'une page web.

<a name="upload"></a>
## Automatisation du téléchargement du contenu de votre application ASP.NET vers votre point de terminaison CDN

Si vous voulez télécharger facilement tout le contenu statique de votre application web ASP.NET dans votre point de terminaison CDN, ou si vous voulez déployer votre application web en utilisant la remise continue (pour un exemple, voir [Remise continue pour Cloud Services dans Azure](cloud-services-dotnet-continuous-delivery.md)), vous pouvez utiliser Azure PowerShell pour automatiser la synchronisation des fichiers de contenu les plus récents avec les objets blob Azure à chaque déploiement de votre application web. Par exemple, vous pouvez exécuter le script de la page [Téléchargement de contenu d'une application ASP.NET dans des objets blob Azure](http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a) pour télécharger tous les fichiers de contenu dans une application ASP.NET. Pour utiliser ce script :

4. Dans le menu **Démarrer**, sélectionnez **Microsoft Azure PowerShell**.
5. Dans la fenêtre Azure PowerShell, exécutez `Get-AzurePublishSettingsFile` pour télécharger un fichier de paramètres de publication pour votre compte Azure.
6. Après avoir téléchargé ce fichier, exécutez la commande suivante : 

		Import-AzurePublishSettingsFile "<chemin du fichier téléchargé>"

	>[AZURE.NOTE] Quand vous importez votre fichier de paramètres de publication, il s'agira du compte Azure par défaut utilisé pour toutes les sessions Azure PowerShell. Cela signifie que vous ne devez effectuer qu'une seule fois les opérations ci-dessus.
	
1. Téléchargez le script à partir de la [page de téléchargement](http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a). Enregistrez-le dans le dossier de projet de votre application ASP.NET.
2. Cliquez avec le bouton droit sur le script téléchargé, puis cliquez sur **Propriétés**.
3. Cliquez sur **Débloquer**.
4. Ouvrez une fenêtre PowerShell et exécutez la commande suivante :

		cd <dossier du Projet>
		.\UploadContentToAzureBlobs.ps1 -StorageAccount "<nom du compte de stockage>" -StorageContainer "<nom du conteneur>"

Ce script télécharge tous les fichiers de vos dossiers *\Content* et *\Scripts* dans le compte de stockage et le conteneur spécifiés. ce qui présente les avantages suivants :

-	réplication automatique de la structure des fichiers de votre projet Visual Studio ;
-	création automatique de conteneurs d'objets blob en fonction des besoins ;
-	réutilisation du même compte de stockage Azure et du même point de terminaison CDN pour plusieurs applications web, chacun dans un conteneur d'objets blob distinct ;
-	mise à jour facile du réseau CDN Azure avec du nouveau contenu. Pour en savoir plus sur la mise à jour du contenu, consultez la section [Configuration du cache du réseau de distribution de contenu (CDN) pour refléter la mise à jour de contenu souhaitée](#update).

Pour le paramètre -StorageContainer, il est pratique d'utiliser le nom de votre application web ou du projet Visual Studio. Comme j'ai précédemment utilisé le nom générique " cdn " comme nom de conteneur, l'utilisation du nom de votre application web permet d'organiser le contenu associé dans le même conteneur facilement identifiable.

Quand le téléchargement du contenu est terminé, vous pouvez lier n'importe quoi dans vos dossiers *\Content* et *\Scripts* dans votre code HTML (par exemple, vos fichiers .cshtml) en utilisant `http://<yourCDNName>.vo.msecnd.net/<containerName>`. Voici un exemple de quelque chose que je peux utiliser dans une vue Razor : 

	<img alt="Mugshot" src="http://az623979.vo.msecnd.net/MyMvcApp/Content/cephas_lin.png" />

Pour un exemple d'intégration de scripts PowerShell dans votre configuration de remise continue, consultez la page [Remise continue pour Cloud Services dans Azure] (cloud-services-dotnet-continuous-delivery.md). 

<a name="update"></a>
## Configuration du cache du réseau de distribution de contenu (CDN) pour refléter la mise à jour de contenu souhaitée

Supposons maintenant qu'après avoir téléchargé les fichiers statiques de votre application web dans un conteneur d'objets blob, vous modifiez un des fichiers de votre projet et que vous le téléchargez à nouveau dans le conteneur. Vous pouvez penser qu'il est automatiquement mis à jour dans votre point de terminaison CDN, mais vous vous demandez pour quoi vous ne voyez pas la mise à jour lorsque vous accédez à l'URL CDN du contenu. 

La vérité est que le réseau CDN effectue réellement la mise à jour à partir du stockage de vos objets blob, mais il le fait en appliquant au contenu une règle par défaut de 7 jours pour la mise en cache. Cela signifie que lorsqu'un nœud CDN extrait votre contenu du stockage d'objets blob, ce contenu n'est pas actualisé tant qu'il n'a pas expiré dans le cache.

La bonne nouvelle est que vous pouvez personnaliser l'expiration du cache. Comme la plupart des navigateurs, Azure CDN respecte la durée d'expiration spécifiée dans l'en-tête Cache-Control du contenu. Vous pouvez spécifier une valeur personnalisée pour l'en-tête Cache-Control en accédant au conteneur d'objets blob dans le portail Azure et en modifiant les propriétés des objets blob. La capture d'écran ci-dessous indique 1 heure (3 600 secondes) pour l'expiration du cache. 

![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-updates-1.PNG)

Vous pouvez d'abord faire cela dans votre script PowerShell pour définir les en-têtes Cache-Control de tous les objets blob. Pour le script dans [Automatisation du téléchargement du contenu de votre application ASP.NET vers votre point de terminaison CDN](#upload), recherchez l'extrait de code suivant :

    Set-AzureStorageBlobContent `
        -Container $StorageContainer `
        -Context $context `
        -File $file.FullName `
        -Blob $blobFileName `
        -Properties @{ContentType=$contentType} `
        -Force

et modifiez-le comme suit :  

    Set-AzureStorageBlobContent `
        -Container $StorageContainer `
        -Context $context `
        -File $file.FullName `
        -Blob $blobFileName `
        -Properties @{ContentType=$contentType; CacheControl="public, max-age=3600"} `
        -Force

Vous devrez peut-être attendre l'expiration complète du contenu en cache au bout de 7 jours sur votre réseau CDN Azure avant l'extraction du nouveau contenu avec le nouvel en-tête Cache-Control. Cela illustre le fait que les valeurs personnalisées de mise en cache n'ont pas d'effet si vous voulez mettre à jour votre contenu pour qu'il soit immédiatement actif, par exemple pour des mises à jour de fichiers JavaScript ou CSS. Cependant, vous pouvez contourner ce problème en renommant les fichiers ou en modifiant leur version via des chaînes de requête. Pour plus d'informations, consultez la section [Distribution immédiate du nouveau contenu au moyen de chaînes de requête](#query).

Bien sûr, il y a un moment et un endroit pour la mise en cache. Exemple : vous pouvez avoir un contenu qui ne nécessite pas de mise à jour fréquente (comme des matchs de la Coupe du Monde qui peuvent être actualisés toutes les 3 heures), mais qui nécessite un trafic suffisamment important pour que vous vouliez le décharger de votre serveur web. Ce contenu est un bon candidat pour la mise en cache Azure CDN.

<a name="query"></a>
## Distribution immédiate du nouveau contenu au moyen de chaînes de requête

Dans Azure CDN, vous pouvez activer des chaînes de requête de façon à ce que le contenu d'URL comportant des chaînes de requête données soit mis en cache séparément. Cette fonctionnalité est intéressante si vous voulez placer immédiatement un contenu mis à jour donné dans les navigateurs clients au lieu d'attendre l'expiration du contenu CDN en cache. Supposons que je publie ma page web avec un numéro de version dans l'URL.  
<pre class="prettyprint">
&lt;link href=&quot;http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css<mark>?v=3.0.0</mark>&quot; rel=&quot;stylesheet&quot;/&gt;
</pre>

Quand je publie une mise à jour CSS et que j'utilise un numéro de version différent dans mon URL CSS :  
<pre class="prettyprint">
&lt;link href=&quot;http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css<mark>?v=3.1.1</mark>&quot; rel=&quot;stylesheet&quot;/&gt;
</pre>

Pour un point de terminaison CDN dont les chaînes de requête sont activées, les deux URL sont uniques. Une nouvelle requête à mon serveur web récupérera donc le nouveau fichier *bootstrap.css*. Cependant, pour un point de terminaison CDN dont les chaînes de requête ne sont pas activées, les URL sont identiques : il distribuera donc simplement le fichier *bootstrap.css* en cache. 

L'astuce consiste alors à mettre automatiquement à jour le numéro de version. Cela est facile dans Visual Studio. Dans un fichier .cshtml où j'utilise le lien ci-dessus, je peux spécifier un numéro de version basé sur le numéro d'assembly.  
<pre class="prettyprint">
@{
    <mark>var cdnVersion = System.Reflection.Assembly.GetAssembly(
        typeof(MyMvcApp.Controllers.HomeController))
        .GetName().Version.ToString();</mark>
}

...

&lt;link href=&quot;http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css<mark>?v=@cdnVersion</mark>&quot; rel=&quot;stylesheet&quot;/&gt;
</pre>

Si vous modifiez le numéro d'assembly à chaque cycle de publication, chaque fois que vous publiez votre application web, vous êtes sûr d'obtenir un numéro de version unique qui reste identique jusqu'au prochain cycle de publication. Vous pouvez également faire en sorte que Visual Studio incrémente automatiquement le numéro de version de l'assembly à chaque génération de l'application web en ouvrant le fichier *Properties\AssemblyInfo.cs* dans votre projet Visual Studio et en utilisant `*` dans `AssemblyVersion`. Par exemple :

	[assembly: AssemblyVersion("1.0.0.*")]

## Qu'en est-il des scripts regroupés et des feuilles de style dans ASP.NET ? ##

Avec [Azure Websites](/services/websites/) et [Azure Cloud Services](/services/cloud-services/), vous obtenez la meilleure intégration d'Azure CDN au [regroupement et à la minimisation d'ASP.NET](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification). 

L'intégration des sites Web Azure ou des services cloud Azure avec Azure CDN offre les avantages suivants :

- Intégration du déploiement de contenu (images, scripts et feuilles de style) dans le cadre du processus de [déploiement continu] de votre site web Azure (web-sites-publish-source-control.md)
- Mise à niveau simplifiée pour vos packages NuGet à contenu CDN, comme les versions jQuery ou Bootstrap 
- Gestion de votre application web et de votre contenu CDN depuis la même interface Visual Studio

Pour connaître les didacticiels associés, consultez :
- [Intégrer un site web Azure à CDN Azure](cdn-websites-with-cdn.md)
- [Intégrer un service cloud à Azure CDN](cdn-cloud-service-with-cdn.md)

Sans intégration avec les services Sites Web Azure ou Azure Cloud Services, vous pouvez utiliser Azure CDN pour les regroupements de vos scripts, avec les mises en garde suivantes :

- Vous devez télécharger manuellement vers le serveur les scripts regroupés dans le stockage d'objets blob. Une solution par programmation est proposée par [stackoverflow](http://stackoverflow.com/a/13736433).
- Dans vos fichiers .cshtml, transformez les balises script/CSS restituées pour utiliser le réseau de distribution de contenu (CDN) Azure. Par exemple :

		@Html.Raw(Styles.Render("~/Content/css").ToString().Insert(0, "http://<nom du CDN>.vo.msecnd.net"))

# Informations complémentaires #
- [Vue d'ensemble du réseau de distribution de contenu (CDN) Azure](http://msdn.microsoft.com/library/azure/ff919703.aspx)
- [Intégrer un site web Azure à CDN Azure](cdn-websites-with-cdn.md)
- [Intégrer un service cloud à Azure CDN](cdn-cloud-service-with-cdn.md)
- [Comment mapper le contenu du réseau de distribution de contenu Azure (CDN) à un domaine personnalisé](http://msdn.microsoft.com/library/azure/gg680307.aspx)
- [Utilisation du réseau de distribution de contenu (CDN) Azure](cdn-how-to-use.md)

<!--HONumber=49-->