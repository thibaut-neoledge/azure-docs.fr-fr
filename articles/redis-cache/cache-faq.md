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
	ms.date="11/30/2015" 
	ms.author="sdanie"/>

# Forum aux questions sur le Cache Redis Azure

Découvrez les réponses aux questions les plus fréquentes, les modèles et les meilleures pratiques pour Cache Redis Azure.

<a name="cache-size"></a>
## Que propose Cache Redis et quelle taille dois-je utiliser ?
Chaque offre Cache Redis Azure propose différents niveaux de **taille**, de **bande passante**, de **haute disponibilité** et de **contrat SLA**.

Voici quelques considérations relatives au choix d’une offre de Cache :

-	**Mémoire** : les niveaux De base et Standard offrent respectivement 250 Mo et 53 Go. Le niveau Premium offre jusqu’à 530 Go, voire plus [sur demande](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Pour plus d’informations, consultez [Tarification - Cache Redis Azure](https://azure.microsoft.com/pricing/details/cache/).
-	**Performances réseau** : si vous avez une charge de travail qui nécessite un débit élevé, le niveau Premium offre davantage de bande passante par rapport au niveau Standard ou De base. En outre, à chaque niveau les caches de taille supérieure offrent davantage de bande passante en raison de la machine virtuelle sous-jacente qui héberge le cache. Pour plus d’informations, consultez le tableau suivant.
-	**Débit** : la gamme Premium offre le débit maximal disponible. Si le client ou le serveur de cache atteint la limite de bande passante, vous recevez des erreurs d’expiration du côté client. Pour plus d’informations, consultez le tableau suivant.
-	**Haute disponibilité/contrat SLA** : le Cache Redis Azure garantit la disponibilité d’un cache Standard/Premium au moins 99,9 % du temps. Pour en savoir plus sur notre contrat SLA, consultez [Tarification - Cache Redis Azure](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Le contrat SLA couvre uniquement la connectivité aux points de terminaison du Cache. Le contrat SLA ne couvre pas la protection contre la perte de données. Nous vous recommandons d’utiliser la fonctionnalité de persistance des données Redis disponible au niveau Premium pour accroître la résilience contre la perte de données.
-	**Persistance des données Redis** : le niveau Premium vous permet de conserver les données du cache dans un compte de stockage Azure. Dans un cache De base/Standard, toutes les données sont stockées uniquement dans la mémoire. En cas de problème lié à l’infrastructure sous-jacente, il existe un risque de perte de données. Nous vous recommandons d’utiliser la fonctionnalité de persistance des données Redis disponible au niveau Premium pour accroître la résilience contre la perte de données. Le Cache Redis Azure propose des options RDB et AOF (à venir) de persistance Redis. Pour plus d’informations, consultez [Comment configurer la persistance pour un Cache Redis Azure Premium](cache-how-to-premium-persistence.md).
-	**Cluster Redis** : si vous souhaitez créer des caches de plus de 53 Go ou partager des données parmi plusieurs nœuds Redis, vous pouvez utiliser le clustering Redis disponible au niveau Premium. Chaque nœud se compose d’une paire de caches principal/réplica pour la haute disponibilité. Pour plus d’informations, consultez [Comment configurer le clustering Redis pour un Cache Redis Azure Premium](cache-how-to-premium-clustering.md).
-	**Amélioration de l’isolement réseau et de la sécurité** : le déploiement de réseau virtuel Azure (VNET, Azure Virtual Network) fournit une sécurité et un isolement renforcés pour votre Cache Redis Azure, ainsi que des sous-réseaux, des stratégies de contrôle d’accès et d’autres fonctionnalités permettant de restreindre davantage l’accès. Pour plus d’informations, consultez [Comment configurer la prise en charge de réseau virtuel pour un Cache Redis Azure Premium](cache-how-to-premium-vnet.md).
-	**Configurer Redis** : au niveaux Standard et Premium, vous pouvez configurer Redis pour les notifications d’espace de clés.
-	**Nombre maximal de connexions client**: le niveau Premium offre le nombre maximal de clients pouvant se connecter à Redis, avec un nombre de connexions plus élevé pour les caches de taille supérieure. [Pour plus d’informations, consultez la page sur la tarification](https://azure.microsoft.com/pricing/details/cache/).
-	**Noyau dédié pour le serveur Redis** : au niveau Premium, toutes les tailles de cache ont un noyau dédié pour Redis. Aux niveaux De base/Standard, les tailles C1 et supérieures ont un noyau dédié pour le serveur Redis.
-	**Redis étant monothread**, le fait d’avoir plus de deux cœurs n’offre aucun avantage supplémentaire, mais les machines virtuelles de grande taille ont généralement plus de bande passante que les plus petites. Si le client ou le serveur de cache atteint la limite de bande passante, vous recevez des erreurs d’expiration du côté client.
-	**Améliorations de performances** : les caches au niveau Premium sont déployés sur du matériel qui dispose de processeurs plus rapides et offrent de meilleures performances par rapport au niveau De base ou Standard. Les caches de niveau Premium ont un débit supérieur et des latences moindres.

Le tableau suivant présente les valeurs maximales de bande passante observées lors de tests exécutés sur différentes tailles de caches Standard et Premium à l’aide de `redis-benchmark.exe` à partir d’une machine virtuelle IaaS sur le point de terminaison du Cache Redis Azure. Notez que ces valeurs ne sont pas garanties et qu’il n’y a pas de contrat SLA pour ces chiffres, mais ils sont à peu près normaux. Vous devez tester la charge de votre application pour déterminer la taille de cache adaptée.

De ce tableau, nous pouvons tirer les conclusions suivantes.

-	Pour un cache de même dimension, le débit du niveau Premium est plus élevé que celui du niveau Standard. Par exemple, pour un cache de 6 Go, le débit de P1 est de 140 000 demandes par seconde (RPS), contre 49 000 dans le cas de C3.
-	Avec le clustering Redis, le débit augmente de façon linéaire à mesure que vous augmentez le nombre de partitions (nœuds) dans le cluster. Par exemple, si vous créez un cluster P4 de 10 partitions, le débit disponible est alors de 250 000 * 10 = 2,5 millions de demandes par seconde.
-	Pour les tailles de clé supérieures, le débit du niveau Premium est plus élevé que celui du niveau Standard.

| Niveau tarifaire | Taille | Bande passante disponible | Taille de clé de 1 Ko |
|----------------------|--------|----------------------------|--------------------------------|
| **Tailles de cache Standard** | &nbsp; |**Mégabits par seconde (Mbits/s)** | **Demandes par seconde (RPS)** |
| C0 | 250 Mo | 5 | 600 |
| C1 | 1 Go | 100 | 12 200 |
| C2 | 2,5 Go | 200 | 24 000 |
| C3 | 6 Go | 400 | 49 000 |
| C4 | 13 Go | 500 | 61 000 |
| C5 | 26 Go | 1 000 | 115 000 |
| C6 | 53 Go | 2000 | 150 000 |
| **Tailles de cache Premium** | &nbsp; | &nbsp; | **Demandes par seconde (RPS), par partition** |
| P1 | 6 Go | 1 000 | 140 000 |
| P2 | 13 Go | 2000 | 220 000 |
| P3 | 26 Go | 2000 | 220 000 |
| P4 | 53 Go | 4000 | 250 000 |


Pour obtenir des instructions sur le téléchargement des outils Redis comme `redis-benchmark.exe`, consultez la section [Comment exécuter des commandes Redis ?](#cache-commands).

<a name="cache-region"></a>
## Dans quelle région dois-je placer mon cache ?

Pour optimiser les performances et la latence, placez votre Cache Redis Azure dans la même région que celle de votre application de client de cache.

<a name="cache-billing"></a>
## Combien me coûte le Cache Redis Azure ?

La tarification du Cache Redis Azure est disponible [ici](http://azure.microsoft.com/pricing/details/cache/). La page Tarifs appliqués répertorie les tarifs appliqués à un taux horaire. Les caches sont facturés à la minute, de la création du cache jusqu’à sa suppression. Aucune option ne vous permet d’arrêter ou de suspendre la facturation d'un cache.

<a name="cache-timeouts"></a>
## Pourquoi est-ce que je reçois des erreurs d’expiration du délai ?

L’expiration du délai se produit dans le client que vous utilisez pour communiquer avec Redis. La plupart du temps, le serveur Redis n’expire pas. Lorsqu’une commande est envoyée au serveur Redis, elle est placée dans la file d’attente, puis le serveur Redis la récupère et l’exécute. Toutefois, le client peut expirer pendant ce processus. Si c’est le cas, une exception est levée côté appelant. Pour plus d’informations sur la résolution des problèmes de délai, consultez le billet de blog sur l’[Examen des exceptions de délai d’attente dans StackExchange.Redis pour le Cache Redis Azure](http://azure.microsoft.com/blog/2015/02/10/investigating-timeout-exceptions-in-stackexchange-redis-for-azure-redis-cache/).

<a name="cache-monitor"></a>
## Comment surveiller l’intégrité et les performances de mon cache ?

Les instances du Cache Redis Microsoft Azure peuvent être surveillées dans le [portail Azure en version préliminaire](https://portal.azure.com). Vous pouvez afficher les mesures, épingler des graphiques de mesure au Tableau d’accueil, personnaliser la plage de date et d’heure des graphiques de surveillance, ajouter et supprimer des mesures dans les graphiques et définir des alertes lorsque certaines conditions sont remplies. Ces outils vous permettent de surveiller l’intégrité de vos instances Cache Redis Azure et vous aident à gérer vos applications de mise en cache. Pour plus d’informations sur la surveillance de vos caches, consultez la page [Surveillance du Cache Redis Azure](https://msdn.microsoft.com/library/azure/dn763945.aspx).

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

ConfigurationOptions|Description|Recommandation
---|---|---
AbortOnConnectFail|Lorsque la valeur est True, la connexion n’est pas rétablie après une panne réseau.|La valeur False laisse StackExchange.Redis se reconnecter automatiquement .
ConnectRetry|Nombre de tentatives de connexion pendant la connexion initiale.||
ConnectTimeout|Délai d’expiration en millisecondes pour les opérations de connexion.|

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

-	[Activez les diagnostics du cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) afin de pouvoir [surveiller](https://msdn.microsoft.com/library/azure/dn763945.aspx) l’intégrité de votre cache. Vous pouvez afficher les mesures dans le portail Azure en version préliminaire et vous pouvez également les [télécharger et les analyser](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) à l’aide des outils de votre choix.
-	Vous pouvez utiliser redis-benchmark.exe pour tester la charge de votre serveur Redis.
	-	Assurez-vous que le client de test de la charge et le cache Redis se trouvent dans la même région.
-	Utilisez redis-cli.exe et surveillez le cache à l’aide de la commande INFO.
	-	Si votre charge provoque une fragmentation élevée de la mémoire, vous devez augmenter la taille de votre cache.
-	Pour obtenir des instructions sur le téléchargement des outils Redis, consultez la section [Comment exécuter des commandes Redis ?](#cache-commands).

<a name="cache-commands"></a>
## Comment exécuter des commandes Redis ?

Vous pouvez utiliser les commandes répertoriées dans [Commandes Redis](http://redis.io/commands#), à l’exception de celles répertoriées dans [Commandes Redis non prises en charge dans le Cache Redis Azure](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Vous disposez de plusieurs options pour exécuter des commandes Redis.

-	Si vous avez un cache Standard ou Premium, vous pouvez exécuter les commandes Redis à l’aide de la [console Redis](cache-configure.md#redis-console). Elle offre un moyen sécurisé d’exécuter des commandes Redis dans la version préliminaire du portail.
-	Vous pouvez aussi utiliser les outils en ligne de commande Redis. Pour ce faire, effectuez les étapes suivantes.
	-	Téléchargez les [outils en ligne de commande Redis](https://github.com/MSOpenTech/redis/releases/download/win-2.8.19.1/redis-2.8.19.zip).
	-	Connectez-vous au cache avec `redis-cli.exe`. Transmettez le point de terminaison du cache à l’aide du commutateur -h et la clé à l’aide de -a, comme indiqué dans l’exemple suivant.
		-	`redis-cli -h <your cache name>.redis.cache.windows.net -a <key>`
	-	Notez que les outils Redis en ligne de commande ne fonctionnent pas avec le port SSL, mais vous pouvez utiliser un utilitaire comme `stunnel` pour connecter en toute sécurité les outils au port SSL en suivant les instructions du billet de blog [Annonce du fournisseur d’état de session ASP.NET pour la version préliminaire de Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

<a name="cache-common-patterns"></a>
## Quels sont les modèles courants de cache et quels sont les points importants à prendre en considération ?

-	Microsoft Patterns & Practices propose les conseils suivants.
	-	[Conseils de mise en cache](https://github.com/mspnp/azure-guidance/blob/master/Caching.md).
	-	[Conseils sur l’implémentation et la conception d’applications cloud Azure](https://github.com/mspnp/azure-guidance)
-	[Modèles de cache courants du Cache Redis Azure](cache-howto-common-cache-patterns.md)

<a name="cache-reference"></a>
## Pourquoi est-ce que le Cache Redis Azure n’a pas de référence de bibliothèque de classes MSDN comme certains autres services Azure ?

Le Cache Redis Microsoft Azure est basé sur le logiciel open source Cache Redis, ce qui vous donne accès à un cache Redis sécurisé et dédié, géré par Microsoft. Plusieurs [clients Redis](http://redis.io/clients) sont disponibles pour de nombreux langages de programmation. Chaque client possède sa propre API qui effectue des appels à l’instance de cache Redis à l’aide de [commandes Redis](http://redis.io/commands).

Étant donné que chaque client est différent, il n’y a pas de référence centralisée au sujet des classes sur MSDN. Chaque client a sa propre documentation de référence. En plus de la documentation de référence, il existe plusieurs didacticiels sur Azure.com qui montrent comment débuter avec le Cache Redis Azure avec plusieurs langages et clients de cache sur la page [Documentation Cache Redis](http://azure.microsoft.com/documentation/services/redis-cache/).


## Quelle est l'offre Azure Cache qui me convient ?

>[AZURE.IMPORTANT]Microsoft recommande d’utiliser le Cache Redis Azure pour tous les nouveaux développements.

Le Cache Azure propose actuellement trois offres :

-	Cache Redis Azure
-	Service de cache géré Azure
-	In-Role Cache Azure


### Cache Redis Azure
Le Cache Redis Azure fait l’objet d’une disponibilité générale dans des tailles pouvant aller jusqu’à 53 Go et son contrat SLA de disponibilité atteint 99,9 %. Le nouveau [niveau Premium](cache-premium-tier.md) propose des tailles pouvant aller jusqu’à 530 Go et prend en charge le clustering, le réseau virtuel et la persistance, avec un contrat SLA de 99,9 %.

Le Cache Redis Azure permet aux clients d’utiliser un cache Redis sécurisé et dédié, géré par Microsoft. Avec cette offre, vous pouvez exploiter toutes les fonctionnalités et l’écosystème fournis par Redis, ainsi que la fiabilité des services d’hébergement et de surveillance Microsoft.

À la différence des caches classiques qui traitent uniquement des paires clé-valeur, Redis est réputé pour ses types de données hautement performants. Redis prend également en charge l’exécution d’opérations atomiques sur ces types, comme : l’ajout à une chaîne, l’incrémentation de la valeur d’un hachage, la transmission de type push vers une liste, le calcul d’une intersection, union et différence ensemblistes ou l’obtention du membre dont le classement est le plus élevé dans un ensemble trié. Parmi les autres fonctionnalités, citons notamment la prise en charge des transactions, pub/sub, la création de scripts Lua, les clés d’une durée de vie limitée et les paramètres de configuration permettant à Redis de se comporter davantage comme un cache classique.

Un autre aspect clé du succès de Redis est son écosystème open source intègre et vif construit tout autour. Ce succès se reflète dans la diversité des clients Redis disponibles dans plusieurs langues. Cet avantage permet une utilisation par presque toutes les charges de travail qui peuvent être développées dans Azure.

Pour plus d’informations sur la prise en main du Cache Redis Azure, consultez [Comment utiliser le Cache Redis Azure](cache-dotnet-how-to-use-azure-redis-cache.md) et la [documentation du Cache Redis Azure](https://azure.microsoft.com/documentation/services/redis-cache/).

### Service de cache géré
Si vous êtes un client existant du service de cache géré Azure, vous pouvez continuer d’utiliser le service existant ou choisir de migrer vers le Cache Redis Azure pour tirer parti de son ensemble de fonctionnalités enrichies. Le service de cache géré Azure est également mis à la disposition générale et offre aussi un contrat SLA garantissant une disponibilité de 99,9 %.

### In-Role Cache
Si vous auto-hébergez le cache à l’aide d’In-Role Cache, vous pouvez également continuer ainsi. In-Role Cache étant un composant logiciel auto-hébergé et non un service hébergé de Microsoft, aucun contrat SLA n’est proposé. Les utilisateurs d’In-Role Cache peuvent choisir de migrer vers le Cache Redis Azure pour tirer parti de son ensemble de fonctionnalités enrichies et obtenir un contrat SLA.

<!---HONumber=AcomDC_1203_2015-->