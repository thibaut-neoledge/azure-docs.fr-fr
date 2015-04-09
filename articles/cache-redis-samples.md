<properties 
	pageTitle="Exemples de Cache Redis Azure" 
	description="Découvrez comment utiliser le Cache Redis Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="sdanie"/>

# Exemples de Cache Redis Azure 

Cette rubrique fournit une liste d'exemples relatifs à Cache Redis Azure, qui couvrent les scénarios de connexion au cache, de lecture et écriture des données vers et depuis le cache, ainsi que l'utilisation des fournisseurs ASP.NET du Cache Redis. Certains de ces exemples sont des projets téléchargeables. D'autres fournissent des instructions étape par étape et incluent des extraits de code, mais ne sont pas liés à un projet téléchargeable.

## Exemples Hello world

Les exemples de cette section montrent les principes fondamentaux de la connexion à une instance du Cache Redis Azure et ceux de la lecture et de l'écriture de données dans le cache à l'aide d'une variété de langages et de clients Redis.

L'exemple [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) montre comment lire et écrire les éléments d'un cache à l'aide du client .NET [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

L'exemple [How to use Azure Redis Cache with Node.js](cache-nodejs-get-started.md) montre comment utiliser le Cache Redis Azure avec Node.js et le client [node_redis](https://github.com/mranney/node_redis).

L'exemple [How to use Azure Redis Cache with Java](cache-java-get-started.md) montre comment utiliser le Cache Redis Azure avec Java et le client [Jedis](https://github.com/xetorthio/jedis).

L'exemple [How to use Azure Redis Cache with Python](cache-python-get-started.md) montre comment utiliser le Cache Redis Azure avec Python et le client [redis-py](https://github.com/andymccurdy/redis-py).

L'[exemple PHP](https://msdn.microsoft.com/library/azure/dn690470.aspx#PHPExample) montre comment utiliser le Cache Redis Azure avec PHP et le client [predis](https://github.com/nrk/predis).

L'exemple [Work with .NET objects in the cache](https://msdn.microsoft.com/library/azure/dn690521.aspx#Objects) montre comment sérialiser des objets .NET afin de pouvoir les lire et les écrire depuis une instance du Cache Redis Azure. 

## Utiliser le Cache Redis comme un fond de panier de montée en puissance pour ASP.NET SignalR

L'exemple [Use Redis Cache as a Scale out Backplane for ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) montre comment utiliser le Cache Redis Azure comme fond de panier SignalR. Pour plus d'informations sur le fond de panier, consultez [SignalR Scaleout with Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## Exemple de requête client dans le Cache Redis

Cet exemple compare les performances entre l'accès aux données à partir d'un cache et l'accès aux données à partir d'un espace de persistance. Cet exemple comporte deux projets.

-	[Demo how Redis Cache can improve performance by Caching data](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
-	[Seed the Database and Cache for the demo](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## État de session ASP.NET et mise en cache de la sortie

L'exemple [Use Azure Redis Cache to store ASP.NET SessionState and OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) montre comment utiliser le Cache Redis Azure pour stocker la session et le cache de sortie ASP.NET à l'aide des fournisseurs SessionState et OutputCache Redis.

## Gérer le Cache Redis Azure avec MAML

L'exemple [Manage Azure Redis Cache using Azure Management Libraries](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) montre comment utiliser les bibliothèques de gestion Azure pour gérer (créer, mettre à jour et supprimer) votre cache. 

## Exemple de surveillance personnalisée

L'exemple [Access Redis Cache Monitoring data](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) montre comment accéder aux données de surveillance du Cache Redis Azure en dehors du portail Azure.



<!--HONumber=49-->