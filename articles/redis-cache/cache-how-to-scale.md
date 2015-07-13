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
	ms.date="06/18/2015" 
	ms.author="sdanie"/>

# Mise à l’échelle du cache Azure Redis

>[AZURE.NOTE]La fonctionnalité de mise à l’échelle du cache Redis Azure est actuellement en version préliminaire.

Le cache Redis Azure offre différents types de caches, permettant de choisir parmi plusieurs tailles et fonctionnalités de caches en toute flexibilité. Si les prérequis de votre application changent après la création d’un cache, vous pouvez mettre à l’échelle la taille du cache en utilisant le panneau **Changer le niveau de tarification** dans le [Portail Azure](https://portal.azure.com).

>[AZURE.NOTE]Lorsque vous mettez à l’échelle un cache Redis Azure, vous pouvez modifier sa taille, mais vous ne pouvez pas passer d’un cache Standard à un cache De base et vice versa.

## Quand mettre à l’échelle ?

Les fonctionnalités de [surveillance](cache-how-to-monitor.md) du cache Redis Azure permettent de surveiller l’intégrité et les performances de vos applications de cache pour déterminer la nécessité de mettre à l’échelle le cache.

Vous pouvez surveiller les mesures suivantes pour déterminer si vous avez besoin d’effectuer une mise à l’échelle.

-	Charge du serveur Redis
-	Utilisation de la mémoire
-	Bande passante réseau
-	Utilisation du processeur

Si vous déterminez que votre cache ne répond plus aux besoins de votre application, vous pouvez passer à un niveau de tarification de cache plus important ou plus réduit, adapté à votre application. Pour plus d’informations sur la détermination des niveaux de tarification de cache à utiliser, consultez la section [Quelle offre et quelle taille de cache Redis utiliser ?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## Mise à l’échelle d’un cache
Pour mettre à l’échelle votre cache, [accédez au cache](https://msdn.microsoft.com/library/azure/dn793612.aspx#RedisCacheConfiguration) dans le [portail Azure](https://portal.azure.com) et cliquez sur la section **Niveau Standard** ou **Niveau De base** du panneau **Cache Redis**.

![Niveau de tarification][redis-cache-pricing-tier-part]

Sélectionnez le niveau de tarification souhaité dans le panneau **Niveau de tarification**, puis cliquez sur **Sélectionner**.

![Niveau de tarification][redis-cache-pricing-tier-blade]

>[AZURE.NOTE]Vous ne pouvez pas passer d’un plan de cache De base à un plan Standard ou vice versa, ni descendre en puissance vers un cache de 250 Mo à partir d’un cache de plus grande taille. Vous pouvez monter en puissance à partir d’un cache de 250 Mo vers une taille plus importante, mais vous ne pourrez pas descendre en puissance par la suite, pour revenir au niveau de tarification de 250 Mo. Si vous devez passer du niveau De base au niveau Standard ou descendre en puissance vers une taille de 250 Mo, vous devez créer un nouveau cache.

Lorsqu’un cache est mis à l’échelle vers un nouveau niveau de tarification, le statut **Mise à l’échelle** s’affiche sur le panneau **Cache Redis**.

![Mise à l'échelle][redis-cache-scaling]

Une fois la mise à l’échelle terminée, le statut passe de **Mise à l’échelle** à **En cours d'exécution**.

>[AZURE.IMPORTANT]Durant les opérations de mise à l’échelle, les caches De base sont déconnectés et toutes les données du cache sont perdues. Une fois l’opération de mise à l’échelle terminée, le cache De base est reconnecté, sans données. Les caches Standard restent connectés pendant leurs opérations de mise à l'échelle et, généralement, aucune donnée n’est perdue lors de la mise à l’échelle d’un cache Standard vers une plus grande taille. Lors de la mise à l’échelle d’un cache Standard vers une plus petite taille, certaines données peuvent être perdues si la nouvelle taille est inférieure à la quantité de données mises en cache. En cas de pertes de données lors de la descente en puissante, les clés sont supprimées à l'aide de la stratégie d’éviction [allkeys-lru](http://redis.io/topics/lru-cache). Notez que, si les caches Standard ont un contrat SLA proposant une disponibilité de 99,9 %, il n’existe pas de contrat SLA contre la perte de données.

## Automatisation d’une opération de mise à l’échelle

En plus de la mise à l’échelle de votre instance de cache Redis Azure dans le portail Azure, vous pouvez effectuer une mise à l’échelle en utilisant les [Bibliothèques de gestion Microsoft Azure](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/). Pour mettre à l’échelle votre cache, appelez la méthode `IRedisOperations.CreateOrUpdate` et transmettez la nouvelle taille pour la `RedisProperties.SKU.Capacity`.

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://msdn.microsoft.com/fr-fr/library/azure/dn790557.aspx#bk_portal
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

Lorsqu’un cache **De base** est mis à l’échelle, il est arrêté et un nouveau cache est configuré avec la nouvelle taille définie. Pendant ce temps, le cache n’est pas disponible et toutes les données qu’il contenait sont perdues.

Lorsqu’un cache **Standard** est mis à l’échelle, l’un des réplicas est arrêté et réapprovisionné vers la nouvelle taille tandis que les données sont transférées, puis l’autre réplica effectue un basculement avant d’être réapprovisionné, selon le même processus, durant un basculement d’un des nœuds du cache.

## Vais-je perdre mes données de cache durant la mise à l’échelle ?

Lors de la mise à l’échelle d’un cache **De base**, toutes les données sont perdues et le cache n’est plus disponible pendant le déroulement de la mise à l’échelle.

Lors de la mise à l’échelle d’un cache **Standard** vers une plus grande taille, généralement, toutes les données sont conservées. Lors de la mise à l'échelle d’un cache **Standard** vers une plus petite taille, il est possible que les données soient perdues si la quantité de données placée dans le cache est supérieure à la nouvelle taille du cache mis à l’échelle. En cas de pertes de données lors de la descente en puissante, les clés sont supprimées à l'aide de la stratégie d’éviction [allkeys-lru](http://redis.io/topics/lru-cache). Notez que, si les caches Standard ont un contrat SLA proposant une disponibilité de 99,9 %, il n’existe pas de contrat SLA contre la perte de données.

## Mon cache reste-t-il disponible durant la mise à l’échelle ?

Les caches **Standard** restent disponibles pendant l’opération de mise à l’échelle.

Les caches **De base** sont déconnectés pendant l’opération de mise à l’échelle.

## Quelles sont les opérations qui ne sont pas prises en charge ?

Vous ne pouvez pas remplacer un cache **De base** par un cache **Standard** ou vice versa lors d’une opération de mise à l’échelle.

Vous pouvez monter en puissance à partir d’un cache **C0** (250 Mo) vers une plus grande taille, mais vous ne pouvez pas descendre en puissance d’un cache volumineux vers un cache **C0**.

En cas d’échec d’une opération de mise à l’échelle, le service va essayer de rétablir l’opération et le cache reviendra à sa taille d’origine.

## Quelle est la durée d’une mise à l’échelle ?

Une mise à l’échelle prend environ 20 minutes, selon la quantité de données dans le cache.

## Comment savoir quand la mise à l’échelle est terminée ?

Le déroulement de l’opération de mise à l’échelle est affiché dans le portail. Une fois la mise à l’échelle terminée, le statut passe à **En cours d’exécution**.

## Pourquoi cette fonctionnalité est-elle en version préliminaire ?

Nous publions cette fonctionnalité pour obtenir des commentaires. Selon les commentaires, nous allons accélérer la mise en disponibilité générale de cette fonctionnalité.





  
<!-- IMAGES -->
[redis-cache-pricing-tier-part]: ./media/cache-how-to-scale/redis-cache-pricing-tier-part.png

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png

<!---HONumber=July15_HO1-->