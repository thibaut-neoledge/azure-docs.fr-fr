<properties 
	pageTitle="Mise à l’échelle du cache Azure Redis" 
	description="Apprenez comment mettre à l’échelle vos instances de cache Redis Azure." 
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
	ms.date="12/16/2015" 
	ms.author="sdanie"/>

# Mise à l’échelle du cache Azure Redis

>[AZURE.NOTE]La fonctionnalité de mise à l’échelle du cache Redis Azure est actuellement en version préliminaire. Au cours de la période préliminaire, vous ne pouvez pas mettre à l’échelle vers ou depuis un cache de niveau Premium, mais vous pouvez modifier le niveau tarifaire au sein d’un cache Premium. Vous pouvez également [modifier la taille de cluster](cache-how-to-premium-clustering.md#cluster-size) d’un cache Premium avec le clustering activé.

Le cache Redis Azure offre différents types de caches, permettant de choisir parmi plusieurs tailles et fonctionnalités de caches en toute flexibilité. Si les conditions requises de votre application changent après la création d’un cache, vous pouvez mettre à l’échelle la taille du cache en utilisant le panneau **Changer le niveau tarifaire** dans le [portail Azure](https://portal.azure.com).

## Quand mettre à l’échelle ?

Les fonctionnalités de [surveillance](cache-how-to-monitor.md) du cache Redis Azure permettent de surveiller l’intégrité et les performances de vos applications de cache pour déterminer la nécessité de mettre à l’échelle le cache.

Vous pouvez surveiller les mesures suivantes pour déterminer si vous avez besoin d’effectuer une mise à l’échelle.

-	Charge du serveur Redis
-	Utilisation de la mémoire
-	Bande passante réseau
-	Utilisation du processeur

Si vous déterminez que votre cache ne répond plus aux besoins de votre application, vous pouvez passer à un niveau de tarification de cache plus important ou plus réduit, adapté à votre application. Pour plus d’informations sur la détermination des niveaux de tarification de cache à utiliser, consultez la section [Quelle offre et quelle taille de cache Redis utiliser ?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## Mise à l’échelle d’un cache
Pour mettre à l’échelle votre cache, [accédez au cache](cache-configure.md#configure-redis-cache-settings) dans le [portail Azure](https://portal.azure.com), puis cliquez sur **Paramètres**, **Niveau tarifaire**.

Vous pouvez également cliquer sur le **Niveau Standard** ou le **Niveau De base** dans le panneau **Cache Redis**.

![Niveau de tarification][redis-cache-pricing-tier-part]

Sélectionnez le niveau de tarification souhaité dans le panneau **Niveau de tarification**, puis cliquez sur **Sélectionner**.

![Niveau de tarification][redis-cache-pricing-tier-blade]

>[AZURE.NOTE]Vous pouvez choisir un niveau de tarification différent avec les restrictions suivantes.
>
>-	Vous ne pouvez pas mettre à l’échelle vers ou depuis un cache **Premium**.
>-	Vous ne pouvez pas passer d’un cache **Standard** à un cache **De base**.
>-	Vous pouvez passer d’un cache **De base** à un cache **Standard**, mais vous ne pouvez pas modifier la taille en même temps. Si vous avez besoin d'une taille différente, vous pouvez effectuer ultérieurement une opération de mise à l'échelle vers la taille voulue.
>-	Vous ne pouvez pas mettre à l’échelle d’une taille supérieure vers la taille **C0 (250 Mo)**.

Lorsqu’un cache est mis à l’échelle vers un nouveau niveau de tarification, le statut **Mise à l’échelle** s’affiche sur le panneau **Cache Redis**.

![Mise à l'échelle][redis-cache-scaling]

Une fois la mise à l’échelle terminée, le statut passe de **Mise à l’échelle** à **En cours d'exécution**.

## Automatisation d’une opération de mise à l’échelle

En plus de la mise à l’échelle de vos instances de Cache Redis Azure dans le portail Azure, vous pouvez effectuer une mise à l’échelle en utilisant les applets de commande PowerShell du Cache Redis Azure, l’interface de ligne de commande Azure et les Bibliothèques de gestion Microsoft Azure (MAML).

### Mise à l’échelle à l’aide de PowerShell

Vous pouvez mettre à l’échelle vos instances de Cache Redis Azure avec PowerShell à l’aide de l’applet de commande [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx) lorsque les propriétés `Size`, `Sku` ou `ShardCount` sont modifiées. L’exemple suivant montre comment mettre à l’échelle un cache nommé `myCache` vers un cache de 2,5 Go.

	Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Pour plus d’informations sur la mise à l’échelle avec PowerShell, consultez [Pour mettre à l’échelle un cache Redis à l’aide de Powershell](cache-howto-manage-redis-cache-powershell.md#scale).

### Mise à l’échelle à l’aide de l’interface de ligne de commande Azure

Pour mettre à l’échelle vos instances de Cache Redis Azure à l’aide de l’interface de ligne de commande Azure, appelez la commande `azure rediscache set` et transmettez les modifications de configuration souhaitées qui incluent une nouvelle taille, la référence (SKU) ou la taille de cluster, en fonction de l’opération de mise à l’échelle souhaitée.

Pour plus d’informations sur la mise à l’échelle avec l’interface de ligne de commande Azure, consultez [Modification des paramètres d’un Cache Redis existant](cache-manage-cli.md#scale).

### Mise à l’échelle à l’aide de MAML

Pour mettre à l’échelle vos instances du Cache Redis Azure à l’aide des [Bibliothèques de gestion Microsoft Azure (MAML)](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/), appelez la méthode `IRedisOperations.CreateOrUpdate` et transmettez la nouvelle taille de `RedisProperties.SKU.Capacity`.

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }

Pour plus d’informations, consultez l’exemple [Gestion du cache Redis en utilisant les bibliothèques de gestion Microsoft Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML).

## FAQ sur la mise à l’échelle

La liste suivante présente différentes réponses aux questions les plus fréquemment posées sur la mise à l’échelle du cache Redis Azure.

## Puis-je mettre à l'échelle vers, depuis ou au sein d'un cache Premium ?

-	Vous ne pouvez pas passer à un niveau tarifaire de cache **Premium** à partir d’un niveau tarifaire **De base** ou **Standard**.
-	Vous ne pouvez pas passer d’un niveau tarifaire de cache **Premium** à un niveau tarifaire **De base** ou **Standard**.
-	Vous pouvez passer d’un niveau tarifaire de cache **Premium** à un autre.
-	Si vous avez activé le clustering lorsque vous avez créé votre cache **Premium**, vous pouvez [changer la taille du cluster](cache-how-to-premium-clustering.md#cluster-size).

Pour plus d’informations, consultez [Comment configurer le clustering Redis pour un Cache Redis Azure Premium](cache-how-to-premium-clustering.md).

## Après la mise à l’échelle, dois-je modifier le nom ou les clés d’accès de mon cache ?

Non, le nom et les clés d’accès de votre cache n’ont pas à être modifiés durant une opération de mise à l’échelle.

## Comment fonctionne la mise à l’échelle ?

Lorsqu’un cache **De base** est mis à l’échelle vers une taille différente, il est arrêté et un nouveau cache est approvisionné avec la nouvelle taille définie. Pendant ce temps, le cache n’est pas disponible et toutes les données qu’il contenait sont perdues.

Lorsqu’un cache **De base** est mis à l’échelle vers un cache **Standard**, un cache de réplica est approvisionné. Les données sont ensuite copiées du cache principal vers le cache de réplica. Le cache reste disponible pendant le processus de mise à l'échelle.

Lorsqu’un cache **Standard** est mis à l’échelle vers une taille différente, l’un des réplicas est arrêté et réapprovisionné avec la nouvelle taille tandis que les données sont transférées. L’autre réplica effectue ensuite un basculement avant d’être réapprovisionné, selon le même processus, durant un basculement de l’un des nœuds du cache.

## Vais-je perdre mes données de cache durant la mise à l’échelle ?

Lors de la mise à l’échelle d’un cache **De base** vers une nouvelle taille, toutes les données sont perdues et le cache n’est plus disponible pendant l’exécution de la mise à l’échelle.

Lorsqu’un cache **De base** est mis à l’échelle vers un cache **Standard**, les données qui se trouvent dans le cache sont généralement conservées.

Lors de la mise à l’échelle d’un cache **Standard** vers une plus grande taille, toutes les données sont généralement conservées. Lors de la mise à l'échelle d’un cache **Standard** vers une plus petite taille, il est possible que les données soient perdues si la quantité de données placée dans le cache est supérieure à la nouvelle taille du cache mis à l’échelle. En cas de pertes de données lors de la descente en puissante, les clés sont supprimées à l'aide de la stratégie d’éviction [allkeys-lru](http://redis.io/topics/lru-cache).

Notez que, si les caches Standard et Premium ont un contrat SLA proposant une disponibilité de 99,9 %, il n’existe pas de contrat SLA contre la perte de données.

## Mon cache reste-t-il disponible durant la mise à l’échelle ?

Les caches **Standard** restent disponibles pendant l’opération de mise à l’échelle.

Les caches **De base** sont hors connexion pendant les opérations de mise à l’échelle à une taille différente, mais ils restent disponibles lors de la mise à l’échelle du niveau **De base** vers le niveau **Standard**.

## Quelles sont les opérations qui ne sont pas prises en charge ?

Vous ne pouvez pas mettre à l’échelle vers ou depuis un cache **Premium**.

Vous ne pouvez pas passer d’un cache **Standard** à un cache **De base**.

Vous pouvez passer d’un cache **De base** à un cache **Standard**, mais vous ne pouvez pas modifier la taille en même temps. Si vous avez besoin d'une taille différente, vous pouvez effectuer ultérieurement une opération de mise à l'échelle vers la taille voulue.

Vous pouvez monter en puissance à partir d’un cache **C0** (250 Mo) vers une plus grande taille, mais vous ne pouvez pas descendre en puissance d’un cache volumineux vers un cache **C0**.

En cas d’échec d’une opération de mise à l’échelle, le service va essayer de rétablir l’opération et le cache reviendra à sa taille d’origine.

## Quelle est la durée d’une mise à l’échelle ?

Une mise à l’échelle prend environ 20 minutes, selon la quantité de données dans le cache.

## Comment savoir quand la mise à l’échelle est terminée ?

Le déroulement de l’opération de mise à l’échelle est affiché dans le portail Azure. Une fois la mise à l’échelle terminée, le statut passe à **En cours d’exécution**.

## Pourquoi cette fonctionnalité est-elle en version préliminaire ?

Nous publions cette fonctionnalité pour obtenir des commentaires. Selon les commentaires, nous allons accélérer la mise en disponibilité générale de cette fonctionnalité.





  
<!-- IMAGES -->
[redis-cache-pricing-tier-part]: ./media/cache-how-to-scale/redis-cache-pricing-tier-part.png

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png

<!---HONumber=AcomDC_1223_2015-->