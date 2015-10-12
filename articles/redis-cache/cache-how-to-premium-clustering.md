<properties 
	pageTitle="Comment configurer le clustering Redis pour un Cache Redis Azure Premium" 
	description="Découvrez comment créer et gérer le clustering Redis pour vos instances de Cache Redis Azure de niveau Premium" 
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
	ms.date="09/30/2015" 
	ms.author="sdanie"/>

# Comment configurer le clustering Redis pour un Cache Redis Azure Premium
Le Cache Redis Azure offre différents types de caches, permettant de choisir parmi plusieurs tailles et fonctionnalités de caches en toute flexibilité, y compris le nouveau niveau Premium actuellement en version préliminaire.

Le niveau Premium de Cache Redis Azure comprend le clustering, la persistance et la prise en charge de réseau virtuel. Cet article décrit comment configurer le clustering dans une instance Premium de Cache Redis Azure.

Pour plus d’informations sur les autres fonctionnalités du cache Premium, consultez [Comment configurer la persistance pour un Cache Redis Azure Premium](cache-how-to-premium-persistence.md) et [Comment configurer la prise en charge de réseau virtuel pour un Cache Redis Azure Premium](cache-how-to-premium-vnet.md).

>[AZURE.NOTE]Le niveau Premium de Cache Redis Azure est actuellement en version préliminaire.

## Qu’est-ce que le cluster Redis ?
Le Cache Redis Azure propose le cluster Redis tel qu’[implémenté dans Redis](http://redis.io/topics/cluster-tutorial). Avec le cluster Redis, vous bénéficiez des avantages suivants.

-	Possibilité de fractionner automatiquement votre dataset parmi plusieurs nœuds. 
-	Continuité des opérations quand un sous-ensemble de nœuds rencontre des erreurs ou ne peut pas communiquer avec le reste du cluster. 
-	Débit supplémentaire : le débit augmente de façon linéaire à mesure que vous augmentez le nombre de partitions. 
-	Taille de mémoire supplémentaire : augmente de façon linéaire à mesure que vous augmentez le nombre de partitions.  

Pour plus d’informations sur la taille, le débit et la bande passante des caches Premium, consultez le [Forum aux questions sur le Cache Redis Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

Dans Azure, le cluster Redis est proposé sous forme de modèle principal/réplica où chaque partition a une paire principal/réplica avec réplication gérée par le service Cache Redis Azure.

## Clustering
La configuration du clustering s’effectue dans le panneau **Nouveau cache Redis** lors de la création du cache. Pour créer un cache, connectez-vous au [portail Azure en version préliminaire](https://portal.azure.com) et cliquez sur **Nouveau**->**Données + stockage**>**Cache Redis**.

![Création d’un cache Redis][redis-cache-new-cache-menu]

Pour configurer le clustering, sélectionnez d’abord l’un des caches **Premium** dans le panneau **Choisir un niveau tarifaire**.

![Choisir un niveau tarifaire][redis-cache-premium-pricing-tier]

La configuration du clustering s’effectue dans le panneau **Cluster Redis**.

![Clustering][redis-cache-clustering]

Vous pouvez avoir jusqu’à 10 partitions dans le cluster. Cliquez sur **Activé** et faites glisser le curseur ou tapez un nombre compris entre 1 et 10 pour **Nombre de partitions**, puis cliquez sur **OK**.

Chaque partition est une paire de cache principal/réplica gérée par Azure et la taille totale du cache est calculée en multipliant le nombre de partitions par la taille de cache sélectionnée dans le niveau tarifaire.

![Clustering][redis-cache-clustering-selected]

Une fois le cache créé, vous vous y connectez et l’utilisez tout comme un cache hors cluster. Redis distribuera les données parmi les partitions de Cache.

## Forum aux questions sur le clustering

La liste suivante présente différentes réponses aux questions les plus fréquemment posées sur le clustering du Cache Redis Azure.

## Quelle est la taille de cache la plus grande que je peux créer ?

La plus grande taille de cache Premium est 53 Go. Vous pouvez créer jusqu’à 10 partitions, ce qui donne une taille maximale de 530 Go. Si vous avez besoin d’une plus grande taille, vous pouvez en [faire la demande](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Pour plus d’informations, consultez [Tarification - Cache Redis Azure](https://azure.microsoft.com/pricing/details/cache/).

## Tous les clients Redis prennent-ils en charge le clustering ?

À l’heure actuelle, les clients ne prennent pas tous en charge le clustering Redis. StackExchange.Redis est l’un de ceux qui le prennent en charge. Pour plus d’informations sur d’autres clients, consultez la section [Playing with the cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) du [didacticiel sur le cluster Redis](http://redis.io/topics/cluster-tutorial).

>[AZURE.NOTE]Si vous utilisez StackExchange.Redis comme client, assurez-vous d’utiliser la dernière version de [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 ou une version ultérieure pour que le clustering fonctionne correctement.

## Comment puis-je me connecter à mon cache quand le clustering est activé ?

Vous pouvez vous connecter à votre cache à l’aide des mêmes [points de terminaison, ports et clés](cache-configure.md#properties) que ceux que vous utilisez pour vous connecter à un cache pour lequel le clustering n’est pas activé. Redis gère le clustering sur le serveur principal pour que vous n’ayez pas à le gérer à partir de votre client.

## Puis-je me connecter directement aux différentes partitions de mon cache ?

Cela n’est pas officiellement pris en charge. Ceci étant dit, chaque partition composée d’une paire de caches principal/réplica désignés collectivement sous le nom d’« instance de cache ». Vous pouvez vous connecter à ces instances de cache à l’aide de redis-cli.exe selon le modèle suivant.

Sans SSL, utilisez les commandes suivantes :

	Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
	Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
	Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
	...
	Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Avec SSL, remplacez `1300N` par `1500N`.

## Puis-je configurer le clustering pour un cache créé précédemment ?

Pendant la période d’évaluation, vous pouvez activer et configurer le clustering uniquement quand vous créez un cache.

## Puis-je configurer le clustering pour un cache De base ou Standard ?

Le clustering est disponible uniquement pour les caches de niveau Premium.

## Étapes suivantes

Découvrez comment utiliser d’autres fonctionnalités de cache Premium. - [Comment configurer la persistance pour un Cache Redis Azure Premium](cache-how-to-premium-persistence.md) - [Comment configurer la prise en charge de réseau virtuel pour un Cache Redis Azure Premium](cache-how-to-premium-vnet.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-clustering/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-clustering/redis-cache-premium-pricing-tier.png

[NewCacheMenu]: ./media/cache-how-to-premium-clustering/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-how-to-premium-clustering/redis-cache-cache-create.png

[redis-cache-premium-pricing-group]: ./media/cache-how-to-premium-clustering/redis-cache-premium-pricing-group.png

[redis-cache-premium-features]: ./media/cache-how-to-premium-clustering/redis-cache-premium-features.png

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

<!---HONumber=Oct15_HO1-->