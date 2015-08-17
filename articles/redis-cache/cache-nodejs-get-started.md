<properties
   pageTitle="Utilisation du Cache Redis Azure avec Node.js"
   description="Prise en main du Cache Redis Azure avec Node.js et node_redis"
   services="redis-cache"
   documentationCenter=""
   authors="MikeWasson"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="nodejs"
   ms.topic="hero-article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="required"
   ms.date="08/04/2015"
   ms.author="mwasson"/>

# Utilisation du Cache Redis Azure avec Node.js

Le Cache Redis Azure permet d'accéder à un cache Redis sécurisé et dédié, géré par Microsoft. Votre cache est accessible à partir de n'importe quelle application dans Microsoft Azure.

Cette rubrique montre comment utiliser le Cache Redis Azure avec Node.js. Pour obtenir un autre exemple d’utilisation du Cache Redis Azure avec Node.js, consultez la rubrique [Développement d’une application de conversation instantanée Node.js avec Socket.IO sur un site web Azure][].


## Composants requis

Installez [node\_redis](https://github.com/mranney/node_redis) :

    npm install redis

Ce didacticiel utilise [node\_redis](https://github.com/mranney/node_redis), mais vous pouvez utiliser n'importe quel client Node.js parmi ceux répertoriés ici : [http://redis.io/clients](http://redis.io/clients).

## Créer un Cache Redis sur Azure

Dans le [Portail Azure en version préliminaire](http://go.microsoft.com/fwlink/?LinkId=398536), cliquez sur **Nouveau**, sur **Stockage + Données**, puis sélectionnez **Cache Redis**.

  ![][1]

Entrez un nom d'hôte DNS. Il se présente comme suit : `<name>.redis.cache.windows.net`. Cliquez sur **Create**.

  ![][2]


Une fois le cache créé, cliquez dessus dans le portail pour afficher ses paramètres. Cliquez sur le lien en dessous de **Clés**, puis copiez la clé primaire. Vous en aurez besoin pour authentifier les demandes.

  ![][4]


## Activer le point de terminaison non SSL


Cliquez sur le lien en dessous de **Ports**, puis cliquez sur **Non** pour l'option « Autoriser l'accès uniquement via SSL ». Cela activera le port non SSL pour le cache. Le client node\_redis ne prend pas en charge le SSL.

  ![][3]


## Ajouter un élément au cache et le récupérer

	var redis = require("redis");

    // Put in your cache name and access key.
	var client = redis.createClient(6379,'<name>.redis.cache.windows.net', {auth_pass: '<key>' });

	client.set("foo", "bar", function(err, reply) {
	    console.log(reply);
	});

	client.get("foo",  function(err, reply) {
	    console.log(reply);
	});


Output:

	OK
	bar


## Étapes suivantes

- [Activez les diagnostics du cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) afin de pouvoir [surveiller](https://msdn.microsoft.com/library/azure/dn763945.aspx) l’intégrité de votre cache.
- Lisez la [documentation Redis](http://redis.io/documentation) officielle.


<!--Image references-->
[1]: ./media/cache-nodejs-get-started/cache01.png
[2]: ./media/cache-nodejs-get-started/cache02.png
[3]: ./media/cache-nodejs-get-started/cache03.png
[4]: ./media/cache-nodejs-get-started/cache04.png

[Développement d’une application de conversation instantanée Node.js avec Socket.IO sur un site web Azure]: ../app-service-web/web-sites-nodejs-chat-app-socketio.md

<!---HONumber=August15_HO6-->