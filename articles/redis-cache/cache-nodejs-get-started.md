<properties
	pageTitle="Utilisation du Cache Redis Azure avec Node.js | Microsoft Azure"
	description="Prise en main du Cache Redis Azure avec Node.js et node_redis"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="douge"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="08/16/2016"
	ms.author="sdanie"/>

# Utilisation du Cache Redis Azure avec Node.js

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.JS](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Le Cache Redis Azure permet d'accéder à un cache Redis sécurisé et dédié, géré par Microsoft. Votre cache est accessible à partir de n'importe quelle application dans Microsoft Azure.

Cette rubrique montre comment utiliser le Cache Redis Azure avec Node.js. Pour obtenir un autre exemple d'utilisation du Cache Redis Azure avec Node.js, consultez la rubrique [Créer une application de conversation instantanée Node.js avec Socket.IO sur un site web Azure](../app-service-web/web-sites-nodejs-chat-app-socketio.md).


## Composants requis

Installez [node\_redis](https://github.com/mranney/node_redis) :

    npm install redis

Ce didacticiel utilise [node\_redis](https://github.com/mranney/node_redis). Pour obtenir des exemples d’utilisation des autres clients Node.js, consultez la documentation individuelle pour les [clients Redis Node.js](http://redis.io/clients#nodejs) répertoriés.

## Créer un Cache Redis sur Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Récupérer les clés d’accès et le nom hôte

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## Connexion au cache en toute sécurité à l’aide de SSL

Les dernières versions de [node\_redis](https://github.com/mranney/node_redis) prennent en charge la connexion au Cache Redis Azure avec SSL. L’exemple suivant montre comment se connecter au Cache Redis Azure à l’aide du point de terminaison SSL 6380. Remplacez `<name>` par le nom de votre cache et `<key>` avec votre clé primaire ou secondaire tel que décrit dans la section précédente [Récupérer les clés d’accès et le nom hôte](#retrieve-the-host-name-and-access-keys).

	 var redis = require("redis");
	
	  // Add your cache name and access key.
	var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});


## Ajouter un élément au cache et le récupérer

L’exemple suivant montre comment se connecter à une instance du Cache Redis Azure et de stocker ou récupérer un élément dans le cache. Pour plus d’exemples d’utilisation de Redis avec le client [node\_redis](https://github.com/mranney/node_redis), consultez [http://redis.js.org/](http://redis.js.org/).

	 var redis = require("redis");
	
	  // Add your cache name and access key.
	var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});
	
	client.set("key1", "value", function(err, reply) {
		    console.log(reply);
		});
	
	client.get("key1",  function(err, reply) {
		    console.log(reply);
		});

Output:

	OK
	value


## Étapes suivantes

- [Activez les diagnostics du cache](cache-how-to-monitor.md#enable-cache-diagnostics) afin de pouvoir [surveiller](cache-how-to-monitor.md) l’intégrité de votre cache.
- Lisez la [documentation Redis](http://redis.io/documentation) officielle.

<!---HONumber=AcomDC_0817_2016-->