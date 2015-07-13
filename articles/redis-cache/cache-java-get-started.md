<properties
   pageTitle="Utilisation du Cache Redis Azure avec Java"
   description="Prise en main du Cache Redis Azure avec Java"
   services="redis-cache"
   documentationCenter=""
   authors="MikeWasson"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="java"
   ms.topic="hero-article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="required"
   ms.date="04/30/2015"
   ms.author="mwasson"/>

# Utilisation du Cache Redis Azure avec Java

Le Cache Redis Azure permet d'accéder à un cache Redis sécurisé et dédié, géré par Microsoft. Votre cache est accessible à partir de n'importe quelle application dans Microsoft Azure.

Cette rubrique montre comment utiliser le Cache Redis Azure avec Java.


## Composants requis

[Jedis](https://github.com/xetorthio/jedis) : client Java pour Redis

Ce didacticiel utilise Jedis, mais vous pouvez utiliser n'importe quel client Java parmi ceux répertoriés ici : [http://redis.io/clients](http://redis.io/clients).


## Créer un Cache Redis sur Azure

Dans le [Portail Azure en version préliminaire](http://go.microsoft.com/fwlink/?LinkId=398536), cliquez sur **Nouveau**, sur **Stockage + Données**, puis sélectionnez **Cache Redis**.

  ![][1]

Entrez un nom d'hôte DNS. Il se présente comme suit : `<name>.redis.cache.windows.net`. Cliquez sur **Create**.

  ![][2]


Une fois le cache créé, cliquez dessus dans le portail pour afficher ses paramètres. Cliquez sur le lien en dessous de **Clés**, puis copiez la clé primaire. Vous en aurez besoin pour authentifier les demandes.

  ![][4]


## Activer le point de terminaison non SSL


Cliquez sur le lien en dessous de **Ports**, puis cliquez sur **Non** pour l'option « Autoriser l'accès uniquement via SSL ». Cela activera le port non SSL pour le cache. Le client Jedis ne prend pas en charge le SSL.

  ![][3]


## Ajouter un élément au cache et le récupérer

	package com.mycompany.app;
	import redis.clients.jedis.Jedis;
	import redis.clients.jedis.JedisShardInfo;

	/* Make sure your turn on non SSL port in Azure Redis using the Configuration section in the Azure portal */
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


<!--Image references-->
[1]: ./media/cache-java-get-started/cache01.png
[2]: ./media/cache-java-get-started/cache02.png
[3]: ./media/cache-java-get-started/cache03.png
[4]: ./media/cache-java-get-started/cache04.png

<!---HONumber=July15_HO1-->