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
	ms.date="09/10/2015" 
	ms.author="sdanie"/>

# Mise à l’échelle du cache Azure Redis

>[AZURE.NOTE]La fonctionnalité de mise à l’échelle du cache Redis Azure est actuellement en version préliminaire.

Le cache Redis Azure offre différents types de caches, permettant de choisir parmi plusieurs tailles et fonctionnalités de caches en toute flexibilité. Si les prérequis de votre application changent après la création d’un cache, vous pouvez mettre à l’échelle la taille du cache en utilisant le panneau **Changer le niveau de tarification** dans la [version préliminaire du portail Azure](https://portal.azure.com).

## Quand mettre à l’échelle ?

Les fonctionnalités de [surveillance](cache-how-to-monitor.md) du cache Redis Azure permettent de surveiller l’intégrité et les performances de vos applications de cache pour déterminer la nécessité de mettre à l’échelle le cache.

Vous pouvez surveiller les mesures suivantes pour déterminer si vous avez besoin d’effectuer une mise à l’échelle.

-	Charge du serveur Redis
-	Utilisation de la mémoire
-	Bande passante réseau
-	Utilisation du processeur

Si vous déterminez que votre cache ne répond plus aux besoins de votre application, vous pouvez passer à un niveau de tarification de cache plus important ou plus réduit, adapté à votre application. Pour plus d’informations sur la détermination des niveaux de tarification de cache à utiliser, consultez la section [Quelle offre et quelle taille de cache Redis utiliser ?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## Mise à l’échelle d’un cache
Pour mettre votre cache à l'échelle, [accédez au cache](cache-configure.md#configure-redis-cache-settings) dans le [portail en version préliminaire](https://portal.azure.com), puis cliquez sur **Paramètres**, **Niveau de tarification**.

Vous pouvez également cliquer sur le **Niveau Standard** ou le **Niveau de base** dans le panneau **Cache Redis**.

![Niveau de tarification][redis-cache-pricing-tier-part]

Sélectionnez le niveau de tarification souhaité dans le panneau **Niveau de tarification**, puis cliquez sur **Sélectionner**.

![Niveau de tarification][redis-cache-pricing-tier-blade]

>[AZURE.NOTE]Vous pouvez choisir un niveau de tarification différent avec les restrictions suivantes.
>
>-	Vous ne pouvez pas passer d'un cache **Standard** à un cache **De base**.
>-	Vous pouvez passer d'un cache **De base** à un cache **Standard**, mais vous ne pouvez pas modifier la taille en même temps. Si vous avez besoin d'une taille différente, vous pouvez effectuer ultérieurement une opération de mise à l'échelle vers la taille voulue.
>-	Vous ne pouvez pas mettre à l'échelle à partir d'une taille supérieure vers la taille **C0 (250 Mo)**.

Lorsqu’un cache est mis à l’échelle vers un nouveau niveau de tarification, le statut **Mise à l’échelle** s’affiche sur le panneau **Cache Redis**.

![Mise à l'échelle][redis-cache-scaling]

Une fois la mise à l’échelle terminée, le statut passe de **Mise à l’échelle** à **En cours d'exécution**.

## Automatisation d’une opération de mise à l’échelle

En plus de la mise à l'échelle de votre instance de cache Redis Azure dans le portail en version préliminaire, vous pouvez effectuer une mise à l'échelle en utilisant les [Bibliothèques de gestion Microsoft Azure](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/). Pour mettre à l’échelle votre cache, appelez la méthode `IRedisOperations.CreateOrUpdate` et transmettez la nouvelle taille pour la `RedisProperties.SKU.Capacity`.

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

## Après la mise à l’échelle, dois-je modifier le nom ou les clés d’accès de mon cache ?

Non, le nom et les clés d’accès de votre cache n’ont pas à être modifiés durant une opération de mise à l’échelle.

## Comment fonctionne la mise à l’échelle ?

Lorsqu’un cache **De base** est mis à l'échelle vers une taille différente, il est arrêté et un nouveau cache est configuré avec la nouvelle taille définie. Pendant ce temps, le cache n’est pas disponible et toutes les données qu’il contenait sont perdues.

Lorsqu'un cache **De base** est mis à l'échelle vers un cache **Standard**, un cache de réplica est configuré et les données sont copiées à partir du cache principal vers le cache de réplica. Le cache reste disponible pendant le processus de mise à l'échelle.

Lorsqu'un cache **Standard** est mis à l'échelle vers une taille différente, l'un des réplicas est arrêté et reconfiguré avec la nouvelle taille tandis que les données sont transférées, puis l'autre réplica effectue un basculement avant d'être réapprovisionné, selon le même processus, durant un basculement de l'un des nœuds du cache.

## Vais-je perdre mes données de cache durant la mise à l’échelle ?

Lors de la mise à l'échelle d'un cache **De base** vers une nouvelle taille, toutes les données sont perdues et le cache n’est plus disponible pendant l'exécution de la mise à l’échelle.

Lorsqu'un cache **De base** est mis à l'échelle vers un cache **Standard**, les données qui se trouvent dans le cache sont généralement conservées.

Lors de la mise à l'échelle d'un cache **Standard** vers une plus grande taille, toutes les données sont généralement conservées. Lors de la mise à l'échelle d’un cache **Standard** vers une plus petite taille, il est possible que les données soient perdues si la quantité de données placée dans le cache est supérieure à la nouvelle taille du cache mis à l’échelle. En cas de pertes de données lors de la descente en puissante, les clés sont supprimées à l'aide de la stratégie d’éviction [allkeys-lru](http://redis.io/topics/lru-cache).

Notez que, si les caches Standard ont un contrat SLA proposant une disponibilité de 99,9 %, il n’existe pas de contrat SLA contre la perte de données.

## Mon cache reste-t-il disponible durant la mise à l’échelle ?

Les caches **Standard** restent disponibles pendant l’opération de mise à l’échelle.

Les caches **De base** sont hors ligne pendant les opérations de mise à l'échelle à une taille différente, mais ils restent disponibles lors de la mise à l'échelle d'un cache **De base** vers **Standard**.

## Quelles sont les opérations qui ne sont pas prises en charge ?

Vous ne pouvez pas modifier un cache **Standard** en un cache **De base**.

Vous pouvez passer d'un cache **De base** à un cache **Standard**, mais vous ne pouvez pas modifier la taille en même temps. Si vous avez besoin d'une taille différente, vous pouvez effectuer ultérieurement une opération de mise à l'échelle vers la taille voulue.

Vous pouvez monter en puissance à partir d’un cache **C0** (250 Mo) vers une plus grande taille, mais vous ne pouvez pas descendre en puissance d’un cache volumineux vers un cache **C0**.

En cas d’échec d’une opération de mise à l’échelle, le service va essayer de rétablir l’opération et le cache reviendra à sa taille d’origine.

## Quelle est la durée d’une mise à l’échelle ?

Une mise à l’échelle prend environ 20 minutes, selon la quantité de données dans le cache.

## Comment savoir quand la mise à l’échelle est terminée ?

Le déroulement de l’opération de mise à l’échelle est affiché dans la version préliminaire du portail. Une fois la mise à l’échelle terminée, le statut passe à **En cours d’exécution**.

## Pourquoi cette fonctionnalité est-elle en version préliminaire ?

Nous publions cette fonctionnalité pour obtenir des commentaires. Selon les commentaires, nous allons accélérer la mise en disponibilité générale de cette fonctionnalité.





  
<!-- IMAGES -->
[redis-cache-pricing-tier-part]: ./media/cache-how-to-scale/redis-cache-pricing-tier-part.png

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png

<!---HONumber=Sept15_HO2-->