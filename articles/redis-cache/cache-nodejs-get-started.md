<properties
	pageTitle="Utilisation du Cache Redis Azure avec Node.js | Microsoft Azure"
	description="Prise en main du Cache Redis Azure avec Node.js et node_redis"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="erikre"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="03/04/2016"
	ms.author="sdanie"/>

# Utilisation du Cache Redis Azure avec Node.js

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.JS](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Le Cache Redis Azure permet d'accéder à un cache Redis sécurisé et dédié, géré par Microsoft. Votre cache est accessible à partir de n'importe quelle application dans Microsoft Azure.

Cette rubrique montre comment utiliser le Cache Redis Azure avec Node.js. Pour obtenir un autre exemple d'utilisation du Cache Redis Azure avec Node.js, consultez la rubrique [Créer une application de conversation instantanée Node.js avec Socket.IO sur un site web Azure][].


## Conditions préalables

Installez [node\_redis](https://github.com/mranney/node_redis) :

    npm install redis

Ce didacticiel utilise [node\_redis](https://github.com/mranney/node_redis), mais vous pouvez utiliser n'importe quel client Node.js parmi ceux répertoriés ici : [http://redis.io/clients](http://redis.io/clients).

## Créer un Cache Redis sur Azure

Dans le [portail Azure](http://go.microsoft.com/fwlink/?LinkId=398536), cliquez sur **Nouveau** puis sur **Données + stockage**, et sélectionnez **Cache Redis**.

  ![][1]

Entrez un nom d'hôte DNS. Il se présente comme suit : `<name>.redis.cache.windows.net`. Cliquez sur **Create**.

  ![][2]


  Une fois le cache créé, [accédez-y](cache-configure.md#configure-redis-cache-settings) pour afficher ses paramètres. Cliquez sur le lien en dessous de **Clés**, puis copiez la clé primaire. Vous en aurez besoin pour authentifier les demandes.

  ![][4]

## Ajouter un élément au cache et le récupérer

```js
var redis = require("redis");

// Add your cache name and access key.
var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

client.set("foo", "bar", function(err, reply) {
  console.log(reply);
});

client.get("foo",  function(err, reply) {
  console.log(reply);
});
```

Output:

	OK
	bar


## Étapes suivantes

- [Activez les diagnostics du cache](cache-how-to-monitor.md#enable-cache-diagnostics) afin de pouvoir [surveiller](cache-how-to-monitor.md) l’intégrité de votre cache.
- Lisez la [documentation Redis](http://redis.io/documentation) officielle.


<!--Image references-->
[1]: ./media/cache-nodejs-get-started/cache01.png
[2]: ./media/cache-nodejs-get-started/cache02.png
[3]: ./media/cache-nodejs-get-started/cache03.png
[4]: ./media/cache-nodejs-get-started/cache04.png

[Créer une application de conversation instantanée Node.js avec Socket.IO sur un site web Azure]: ../app-service-web/web-sites-nodejs-chat-app-socketio.md

<!---HONumber=AcomDC_0309_2016-->