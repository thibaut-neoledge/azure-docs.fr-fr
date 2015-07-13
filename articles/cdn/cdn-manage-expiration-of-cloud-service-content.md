<properties 
 pageTitle="Comment gérer l'expiration des contenus de service cloud dans le réseau de distribution de contenu Azure (CDN)" 
 description="" 
 services="cdn" 
 documentationCenter=".NET" 
 authors="zhangmanling" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="cdn" 
 ms.workload="media" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="04/25/2015" 
 ms.author="mazha"/>

#Comment gérer l'expiration des contenus de service cloud dans le réseau de distribution de contenu Azure (CDN)

Les objets qui bénéficient le plus de la mise en cache Azure CDN sont ceux qui sont fréquemment sollicités pendant leur durée de vie. Un objet reste dans le cache pour la durée de vie, puis est actualisé à partir du service cloud une fois cette période écoulée. Ensuite, le processus se répète.

Si vous ne fournissez pas de valeurs de cache, la durée de vie d'un objet est de 7 jours.

Pour le contenu statique tel que les images et les feuilles de style, vous pouvez contrôler la fréquence de mise à jour en incluant un fichier web.config dans le dossier CDN qui abrite le contenu et en modifiant les paramètres **clientCache** pour contrôler l’en-tête Cache-Control de votre contenu. Les paramètres web.config affectent tous les éléments du dossier et de tous les sous-dossiers, sauf s'ils sont redéfinis dans un autre sous-dossier. Par exemple, vous pouvez définir une durée de vie par défaut à la racine pour que tout le contenu statique soit mis en cache pendant 3 jours, tout en définissant une durée de mise en cache de 6 heures pour un sous-dossier dont le contenu varie davantage.

Le code XML suivant montre comment configurer **clientCache** pour spécifier un âge maximal de 3 jours :

	<configuration> 
	  <system.webServer> 
	        <staticContent> 
	            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" /> 
	        </staticContent> 
	  </system.webServer> 
	</configuration>

Spécifier **UseMaxAge** ajoute un en-tête Cache-Control: max-age=<nnn> à la réponse en fonction de la valeur spécifiée dans l’attribut **CacheControlMaxAge**. Le format de la période pour l’attribut **cacheControlMaxAge** est <days>.<hours>:<min>:<sec>. Pour plus d’informations sur le nœud **clientCache**, consultez [Cache client <clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).

Pour le contenu retourné à partir d’applications telles que des pages .aspx, vous pouvez définir le comportement de mise en cache du CDN par programme en définissant la propriété **HttpResponse.Cache**. Pour plus d’informations sur la propriété **HttpResponse.Cache**, consultez les pages [HttpResponse.Cache, propriété](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) et [HttpCachePolicy, classe](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).

Si vous souhaitez mettre en cache du contenu d’application par programme, assurez-vous que le contenu est marqué comme pouvant être mis en cache en définissant HttpCacheability sur *Public*. En outre, assurez-vous qu'un validateur de cache est défini. Le validateur de cache peut être un horodatage de dernière modification défini en appelant SetLastModified, ou une valeur etag définie en appelant SetETag. Si vous le souhaitez, vous pouvez également spécifier un délai d'expiration du cache en appelant SetExpires, ou vous pouvez vous en remettre à la méthode heuristique de mise en cache par défaut décrite précédemment dans ce document.

Par exemple, pour mettre en cache du contenu pendant une heure, ajoutez les éléments suivants :

            // Set the caching parameters.
            Response.Cache.SetExpires(DateTime.Now.AddHours(1));
            Response.Cache.SetCacheability(HttpCacheability.Public);
            Response.Cache.SetLastModified(DateTime.Now);

##Voir aussi

[Comment gérer l’expiration des contenus d’objets blob dans le réseau de distribution de contenu Azure (CDN)](./cdn-manage-expiration-of-blob-content.md)

<!---HONumber=62-->