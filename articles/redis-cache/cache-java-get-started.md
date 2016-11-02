<properties
   pageTitle="Utilisation du Cache Redis Azure avec Java | Microsoft Azure"
	description="Prise en main du Cache Redis Azure avec Java"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="douge"
	editor=""/>

<tags
	ms.service="cache"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="08/24/2016"
	ms.author="sdanie"/>

# Utilisation du Cache Redis Azure avec Java

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.JS](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Le Cache Redis Azure permet d’accéder à un cache Redis dédié géré par Microsoft. Votre cache est accessible à partir de n'importe quelle application dans Microsoft Azure.

Cette rubrique montre comment utiliser le Cache Redis Azure avec Java.

## Composants requis

[Jedis](https://github.com/xetorthio/jedis) : client Java pour Redis

Ce didacticiel utilise Jedis, mais vous pouvez utiliser n'importe quel client Java parmi ceux répertoriés ici : [http://redis.io/clients](http://redis.io/clients).

## Créer un Cache Redis sur Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Récupérer les clés d’accès et le nom hôte

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## Activer le point de terminaison non SSL

Certains clients Redis ne prennent pas en charge SSL. Le [port non-SSL est désactivé par défaut pour les nouvelles instances Cache Redis Azure](cache-configure.md#access-ports). Au moment de la rédaction de cet article, le client [Jedis](https://github.com/xetorthio/jedis) ne prend pas en charge SSL.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]




## Ajouter un élément au cache et le récupérer

	package com.mycompany.app;
	import redis.clients.jedis.Jedis;
	import redis.clients.jedis.JedisShardInfo;

	/* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the Azure Portal */
	public class App
	{
	  public static void main( String[] args )
	  {
        /* In this line, replace <name> with your cache name: */
	    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
	    shardInfo.setPassword("<key>"); /* Use your access key. */
	    Jedis jedis = new Jedis(shardInfo);
     	jedis.set("foo", "bar");
     	String value = jedis.get("foo");
	  }
	}


## Étapes suivantes

- [Activez les diagnostics du cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) afin de pouvoir [surveiller](https://msdn.microsoft.com/library/azure/dn763945.aspx) l’intégrité de votre cache.
- Lisez la [documentation Redis](http://redis.io/documentation) officielle.

<!---HONumber=AcomDC_0824_2016-->