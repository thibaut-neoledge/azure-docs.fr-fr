<properties 
	pageTitle="Forum aux questions sur le Cache Redis Azure" 
	description="Découvrez les réponses aux questions les plus fréquentes, les modèles et les meilleures pratiques pour Cache Redis Azure." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/01/2015" 
	ms.author="sdanie"/>

# Forum aux questions sur le Cache Redis Azure

Découvrez les réponses aux questions les plus fréquentes, les modèles et les meilleures pratiques pour Cache Redis Azure.

<a name="cache-size"></a>
## Que propose Cache Redis et quelle taille dois-je utiliser ?

Chaque offre Cache Redis Azure propose différents niveaux de **taille**, de **bande passante**, de **haute disponibilité** et de **contrat SLA**.

-	SKU de base - nœud unique, sans réplication ni contrat SLA, tailles de cache de 250 Mo à 53 Go.
-	SKU standard - nœuds principal et secondaire avec réplication automatique, contrat SLA à 99,9 %, tailles de cache de 250 Mo à 53 Go.

Si vous souhaitez une plus haute disponibilité, choisissez l’offre de cache Standard, qui comporte un contrat SLA de 99,9 %. Pour le développement et la création de prototypes, ou pour les scénarios où un contrat SLA n’est pas nécessaire, l’offre De base peut suffire.

La taille du cache et la bande passante correspondent à peu près à la taille et à la bande passante des machines virtuelles qui hébergent le cache. Les 250 Mo des offres De base et Standard sont hébergés sur une très petite machine virtuelle (A0), qui est hébergée à l’aide de cœurs partagés, alors que les autres tailles sont hébergées à l’aide de cœurs dédiés. Les caches de 1 Go sont hébergés sur une machine virtuelle de petite taille (A1), qui a 1 cœur virtuel dédié utilisé à la fois pour le système d’exploitation et le cache Redis. Les caches de grande taille sont hébergés sur des instances de machine virtuelle plus grandes avec plusieurs cœurs virtuels dédiés.

Si votre cache présente un débit élevé, choisissez la taille de 1 Go ou plus afin que le cache soit exécuté à l’aide de cœurs dédiés. Le cache de 1 Go est hébergé sur une machine virtuelle à 1 cœur. Il est utilisé à la fois pour le système d’exploitation et pour le cache du service. Les caches de plus de 1 Go s’exécutent sur des machines virtuelles avec plusieurs cœurs et le cache Redis utilise un cœur dédié, qui n’est pas partagé avec le système d’exploitation.

**Redis n’a qu’un seul thread**, et donc avoir plus de deux cœurs n’offre aucun avantage supplémentaire, mais les **machines virtuelles de grande taille ont généralement plus de bande passante que les plus petites**. Si le client ou le serveur de cache atteint la limite de bande passante, vous recevez des erreurs d’expiration du côté client.

Le tableau suivant présente les valeurs de bande passante maximale observées lors du test de différentes tailles de Cache Redis Azure à l’aide de `redis-benchmark.exe` à partir d’une machine virtuelle IaaS sur le point de terminaison du Cache Redis Azure. Notez que ces valeurs ne sont pas garanties et qu’il n’y a pas de contrat SLA pour ces chiffres, mais ils sont à peu près normaux. Vous devez tester la charge de votre application pour déterminer la taille de cache adaptée.

<table>
  <tr>
    <th>Nom du cache</th>
    <th>Taille du cache</th>
    <th>Get/s (appels GET simples d’une valeur de 1&#160;Ko)</th>
    <th>Bande passante (Mbits/s)</th>
  </tr>
  <tr>
    <td>C0</td>
    <td>250&#160;Mo</td>
    <td>610</td>
    <td>5</td>
  </tr>
  <tr>
    <td>C1</td>
    <td>1 Go</td>
    <td>12&#160;200</td>
    <td>100</td>
  </tr>
  <tr>
    <td>C2</td>
    <td>2,5&#160;Go</td>
    <td>24&#160;300</td>
    <td>200</td>
  </tr>
  <tr>
    <td>C3</td>
    <td>6&#160;Go</td>
    <td>48&#160;875</td>
    <td>400</td>
  </tr>
  <tr>
    <td>C4</td>
    <td>13&#160;Go</td>
    <td>61&#160;350</td>
    <td>500</td>
  </tr>
  <tr>
    <td>C5</td>
    <td>26&#160;Go</td>
    <td>112&#160;275</td>
    <td>1&#160;000</td>
  </tr>
  <tr>
    <td>C6</td>
    <td>53&#160;Go</td>
    <td>153&#160;219</td>
    <td>Plus de 1&#160;000</td>
  </tr>
</table>

Pour obtenir des instructions sur le téléchargement des outils Redis comme `redis-benchmark.exe`, consultez la section [Comment exécuter des commandes Redis ?](#cache-commands).

<a name="cache-region"></a>
## Dans quelle région dois-je placer mon cache ?

Pour optimiser les performances et la latence, placez votre Cache Redis Azure dans la même région que celle de votre application de client de cache.

<a name="cache-timeouts"></a>
## Pourquoi est-ce que je reçois des erreurs d’expiration du délai ?

L’expiration du délai se produit dans le client que vous utilisez pour communiquer avec Redis. La plupart du temps, le serveur Redis n’expire pas. Lorsqu’une commande est envoyée au serveur Redis, elle est placée dans la file d’attente, puis le serveur Redis la récupère et l’exécute. Toutefois, le client peut expirer pendant ce processus. Si c’est le cas, une exception est levée côté appelant. Pour plus d’informations sur la résolution des problèmes de délai, consultez le billet de blog sur l’[Examen des exceptions de délai d’attente dans StackExchange.Redis pour le Cache Redis Azure](http://azure.microsoft.com/blog/2015/02/10/investigating-timeout-exceptions-in-stackexchange-redis-for-azure-redis-cache/).

<a name="cache-monitor"></a>
## Comment surveiller l’intégrité et les performances de mon cache ?

Les instances de Cache Redis Microsoft Azure peuvent être surveillées dans le [portail Azure en version préliminaire](https://portal.azure.com). Vous pouvez afficher les mesures, épingler des graphiques de mesure au Tableau d’accueil, personnaliser la plage de date et d’heure des graphiques de surveillance, ajouter et supprimer des mesures dans les graphiques et définir des alertes lorsque certaines conditions sont remplies. Ces outils vous permettent de surveiller l’intégrité de vos instances Cache Redis Azure et vous aident à gérer vos applications de mise en cache. Pour plus d’informations sur la surveillance de vos caches, consultez la page [Surveillance du Cache Redis Azure](https://msdn.microsoft.com/library/azure/dn763945.aspx).

<a name="cache-disconnect"></a>
## Pourquoi mon client a-t-il été déconnecté du cache ?

Voici quelques raisons pour lesquelles la déconnexion du cache peut se produire :

-	Causes côté client
	-	L’application cliente a été redéployée.
	-	L’application cliente a effectué une opération de mise à l’échelle.
		-	Dans le cas de Cloud Services ou de Web Apps, cela peut être dû à la mise à l’échelle automatique.
	-	La couche réseau côté client a été modifiée.
	-	Des erreurs temporaires se sont produites dans le client ou dans les nœuds du réseau entre le client et le serveur.
	-	Les limites de seuil de bande passante ont été atteintes.
	-	Les opérations côté processeur ont pris trop de temps.
-	Causes côté serveur
	-	Dans l’offre de cache Standard, le service Cache Redis Azure a créé un basculement du nœud principal vers le nœud secondaire.
	-	Azure appliquait un correctif à l’instance où le cache était déployé
		-	Il peut s’agir de mises à jour du serveur Redis ou d’une maintenance générale de la machine virtuelle.

<a name="cache-configuration"></a>
## En quoi consistent les options de configuration StackExchange.Redis ?

StackExchange.Redis présente de nombreuses options. Cette section présente certains des paramètres les plus courants. Pour plus d’informations sur les options de StackExchange.Redis, consultez la page [Configuration StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md).

<table>
  <tr>
    <th>ConfigurationOptions</th>
    <th>Description</th>
    <th>Recommandation</th>
  </tr>
  <tr>
    <td>AbortOnConnectFail</td>
    <td>Lorsque la valeur est True, la connexion n’est pas rétablie après une panne réseau.</td>
    <td>La valeur False laisse StackExchange.Redis se reconnecter automatiquement .</td>
  </tr>
  <tr>
    <td>ConnectRetry</td>
    <td>Nombre de tentatives de connexion pendant la connexion initiale.</td>
    <td></td>
  </tr>
  <tr>
    <td>ConnectTimeout</td>
    <td>Délai d’expiration en millisecondes pour les opérations de connexion.</td>
    <td></td>
  </tr>
</table>

Dans la plupart des cas, les valeurs par défaut du client sont suffisantes. Vous pouvez affiner les options en fonction de votre charge de travail.

-	Nouvelle tentatives
	-	Pour ConnectRetry et ConnectTimeout, la recommandation générale consiste à échouer rapidement, puis à réessayer. Cela dépend de votre charge de travail et de combien de temps en moyenne votre client prend pour émettre une commande Redis et recevoir une réponse.
	-	Laissez StackExchange.Redis se reconnecter automatiquement au lieu de vérifier l’état de la connexion et de vous reconnecter vous-même. **Évitez d’utiliser la propriété ConnectionMultiplexer.IsConnected**.
	-	Effet boule de neige : vous pouvez parfois rencontrer un problème, vous réessayez, mais le problème ne fait que grossir et n’est jamais résolu. Dans ce cas, utilisez un algorithme de nouvelle tentative exponentiel, comme décrit dans l’article [Conseils généraux sur les nouvelles tentatives](https://github.com/mspnp/azure-guidance/blob/master/Retry-General.md), publié par le groupe Microsoft Patterns & Practices.
-	Valeurs de délai d’expiration
	-	Réfléchissez à votre charge de travail et définissez les valeurs en conséquence. Si vous stockez des valeurs élevées, définissez le délai sur une valeur plus élevée.
		-	Définissez ABortOnConnectFail sur la valeur False et laissez StackExchange.Redis assurer la reconnexion à votre place.
-	Utilisez une seule instance de ConnectionMultiplexer pour l’application. Vous pouvez utiliser une LazyConnection pour créer une instance unique qui est renvoyée par une propriété Connection, comme indiqué dans [Connexion au cache avec la classe ConnectionMultiplexer](https://msdn.microsoft.com/library/azure/dn690521.aspx#Connect).
-	Définissez la propriété `ConnectionMultiplexer.ClientName` sur un nom d’instance unique pour faciliter le diagnostic.
-	Utilisez plusieurs instances `ConnectionMultiplexer` pour les charges de travail personnalisées.
	-	Vous pouvez suivre ce modèle si vous savez que la charge de votre application est variable. Par exemple :
		-	Vous pouvez avoir un multiplexeur pour gérer les clés de grande taille. 
		-	Vous pouvez avoir un multiplexeur pour gérer les clés de petite taille. 
		-	Vous pouvez définir différentes valeurs pour l’expiration du délai et la logique des nouvelles tentatives pour chaque ConnectionMultiplexer que vous utilisez.
		-	Définissez la propriété `ClientName` de chaque multiplexeur pour faciliter le diagnostic. 
		-	Cela permet d’obtenir une latence optimisée par `ConnectionMultiplexer`.

<a name="cache-redis-commands"></a>
## Quels sont les points à prendre en compte en ce qui concerne les commandes Redis les plus courantes ?

-	Vous ne devez pas exécuter certaines commandes Redis qui prennent un certain temps sans comprendre l’impact qu’ont ces commandes.
	-	Par exemple, n’exécutez pas la commande [KEYS](http://redis.io/commands/keys) en production, car elle peut prendre beaucoup de temps à renvoyer une réponse en fonction du nombre de clés. Redis est un serveur à thread unique qui traite une commande à la fois. Si vous avez émis d’autres commandes après KEYS, elles ne sont pas traitées tant que Redis traite la commande KEYS.
-	Taille des clés : dois-je utiliser des petites clés/valeurs ou de grandes clés/valeurs ? En général, cela dépend du scénario. Si votre scénario requiert des clés plus longues, vous pouvez ajuster les valeurs ConnectionTimeout et les valeurs des nouvelles tentatives et adapter votre logique de nouvelles tentatives. Du point de vue du serveur Redis, les plus petites valeurs sont celles qui présentent les meilleures performances.
	-	Cela ne veut pas dire que vous ne pouvez pas stocker de plus grandes valeurs dans Redis. Vous devez simplement être au fait de ces informations. Les temps de latence seront plus élevés. Si vous avez un jeu de données plus grand et un autre plus petit, vous pouvez utiliser plusieurs instances ConnectionMultiplexer, chacune configurée avec un ensemble différent de valeurs de délai d’expiration et de nouvelles tentatives, comme décrit dans la section précédente [En quoi consistent les options de configuration StackExchange.Redis](#cache-configuration).


<a name="cache-ssl"></a>
## Quand dois-je activer le port non-SSL pour la connexion à Redis ?

Le serveur Redis ne prend pas en charge SSL dès le départ, contrairement au Cache Redis Azure. Si vous vous connectez à un Cache Redis Azure et que votre client est compatible SSL, par exemple StackExchange.Redis, vous devez utiliser SSL.

Notez que le port non-SSL est désactivé par défaut pour les nouvelles instances Cache Redis Azure. Si votre client ne prend pas en charge SSL, vous devez activer le port non-SSL en suivant les instructions de la section [Ports d’accès](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) de l’article [Configuration d’un cache dans le Cache Redis Azure](https://msdn.microsoft.com/library/azure/dn793612.aspx).

Les outils Redis, comme `redis-cli`, ne fonctionnent pas avec le port SSL, mais vous pouvez utiliser un utilitaire comme `stunnel` pour connecter en toute sécurité les outils au port SSL en suivant les instructions du billet de blog [Annonce du fournisseur d’état de session ASP.NET pour la version préliminaire de Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Pour obtenir des instructions sur le téléchargement des outils Redis, consultez la section [Comment exécuter des commandes Redis ?](#cache-commands).

<a name="cache-benchmarking"></a>
## Comment puis-je évaluer et tester les performances de mon cache ?

-	[Activez les diagnostics du cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) afin de pouvoir [surveiller](https://msdn.microsoft.com/library/azure/dn763945.aspx) l’intégrité de votre cache. Vous pouvez afficher les mesures dans le portail, ainsi que les [télécharger et les analyser](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) à l’aide des outils de votre choix.
-	Vous pouvez utiliser redis-benchmark.exe pour tester la charge de votre serveur Redis.
	-	Assurez-vous que le client de test de la charge et le cache Redis se trouvent dans la même région.
-	Utilisez redis-cli.exe et surveillez le cache à l’aide de la commande INFO.
	-	Si votre charge provoque une fragmentation élevée de la mémoire, vous devez augmenter la taille de votre cache.
-	Pour obtenir des instructions sur le téléchargement des outils Redis, consultez la section [Comment exécuter des commandes Redis ?](#cache-commands).

<a name="cache-commands"></a>
## Comment exécuter des commandes Redis ?

Vous pouvez utiliser les commandes répertoriées dans la page sur les [Commandes Redis](http://redis.io/commands#). Pour exécuter ces commandes, vous pouvez utiliser les outils suivants.

-	Téléchargez les [outils en ligne de commande Redis](https://github.com/MSOpenTech/redis/releases/download/win-2.8.19.1/redis-2.8.19.zip).
-	Connectez-vous au cache avec `redis-cli.exe`. Transmettez le point de terminaison du cache à l’aide du commutateur -h et la clé à l’aide de -a, comme indiqué dans l’exemple suivant.
	-	`redis-cli -h <your cache name>.redis.cache.windows.net -a <key>`
-	Notez que les outils Redis en ligne de commande ne fonctionnent pas avec le port SSL, mais vous pouvez utiliser un utilitaire comme `stunnel` pour connecter en toute sécurité les outils au port SSL en suivant les instructions du billet de blog [Annonce du fournisseur d’état de session ASP.NET pour la version préliminaire de Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

<a name="cache-common-patterns"></a>
## Quels sont les modèles courants de cache et quels sont les points importants à prendre en considération ?

-	Microsoft Patterns & Practices propose les conseils suivants.
	-	[Conseils de mise en cache](https://github.com/mspnp/azure-guidance/blob/master/Caching.md).
	-	[Conseils sur l’implémentation et la conception d’applications cloud Azure](https://github.com/mspnp/azure-guidance)
-	[Modèles de cache courants du Cache Redis Azure](cache-howto-common-cache-patterns/)

<a name="cache-reference"></a>
## Pourquoi est-ce que le Cache Redis Azure n’a pas de référence de bibliothèque de classes MSDN comme certains autres services Azure ?

Le Cache Redis Microsoft Azure est basé sur le logiciel open source Cache Redis, ce qui vous donne accès à un cache Redis sécurisé et dédié, géré par Microsoft. Plusieurs [clients Redis](http://redis.io/clients) sont disponibles pour de nombreux langages de programmation. Chaque client possède sa propre API qui effectue des appels à l’instance de cache Redis à l’aide de [commandes Redis](http://redis.io/commands).

Étant donné que chaque client est différent, il n’y a pas de référence centralisée au sujet des classes sur MSDN. Chaque client a sa propre documentation de référence. En plus de la documentation de référence, il existe plusieurs didacticiels sur Azure.com qui montrent comment débuter avec le Cache Redis Azure avec plusieurs langages et clients de cache sur la page [Documentation Cache Redis](http://azure.microsoft.com/documentatgion/services/redis-cache/).

<!---HONumber=July15_HO1-->