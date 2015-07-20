<properties 
   pageTitle="Configuration de Cache Redis Azure"
   description="Comprendre la configuration de Redis par défaut pour Cache Redis Azure et apprendre à configurer vos instances de Cache Redis Azure"
   services="redis-cache"
   documentationCenter="na"
   authors="steved0x"
   manager="dwrede"
   editor="tysonn" />
<tags 
   ms.service="cache"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="tbd"
   ms.date="06/29/2015"
   ms.author="sdanie" />

# Configuration de Cache Redis Azure

Cette rubrique montre comment consulter et mettre à jour la configuration de vos instances de Cache Redis Azure et présente la configuration du serveur Redis par défaut pour les instances de Cache Redis Azure.

## Configuration des paramètres de cache Redis

Les caches sont accessibles dans le [portail Microsoft Azure en version préliminaire](https://portal.azure.com) à l'aide du panneau **Parcourir**.

![Panneau de navigation Cache Redis Azure](./media/cache-configure/IC796920.png)

Cliquez sur **Caches Redis** pour afficher vos caches.

![Liste de cache de navigation Cache Redis Azure](./media/cache-configure/IC796921.png)

Sélectionnez le cache souhaité pour afficher ses propriétés.

![Tous les paramètres de Cache Redis](./media/cache-configure/IC808312.png)

Cliquez sur **Paramètres** ou **Tous les paramètres** pour afficher et configurer votre cache.

![Paramètres de Cache Redis](./media/cache-configure/IC808313.png)

## Properties

Cliquez sur **Propriétés** pour afficher des informations sur le cache, y compris le point de terminaison et les ports du cache.

![Propriétés de Cache Redis](./media/cache-configure/IC808314.png)

## Clés d'accès

Cliquez sur **Clés d'accès** pour afficher ou régénérer les clés d'accès de votre cache. Ces clés sont utilisées avec le nom d'hôte et les ports à partir du panneau **Propriétés** par les clients se connectant à votre cache.

![Clés d'accès de Cache Redis](./media/cache-configure/IC808315.png)

## Ports d'accès

Par défaut, l'accès non SSL est désactivé pour les nouveaux caches. Pour activer le port non SSL, cliquez sur le panneau **Accès aux Ports**, puis sur **Non**.

![Ports d’accès de Cache Redis](./media/cache-configure/IC808316.png)

## Diagnostics

Cliquez sur **Diagnostics** pour configurer le compte de stockage utilisé pour stocker les diagnostics de cache.

![Diagnostics de Cache Redis](./media/cache-configure/IC808317.png)

Pour plus d'informations, consultez [Surveillance de Cache Redis Azure](cache-how-to-monitor.md).

## Maxmemory-policy et maxmemory-reserved

Cliquez sur **Maxmemory policy** pour configurer les stratégies de mémoire pour le cache. Le paramètre **maxmemory-policy** configure la stratégie d'éviction du cache et **maxmemory-reserved** configure la mémoire réservée pour les processus non cache.

![Stratégie Maxmemory de Cache Redis](./media/cache-configure/IC808318.png)

**Maxmemory policy** vous permet de choisir parmi les stratégies d'éviction.

-	volatile-lru est la valeur par défaut.
-	allkeys-lru
-	volatile-random
-	allkeys-random
-	volatile-ttl
-	noeviction

Pour plus d'informations sur les stratégies maxmemory, consultez [Stratégies d'éviction](http://redis.io/topics/lru-cache#eviction-policies).

Le paramètre **maxmemory-reserved** détermine la quantité de mémoire (en mégaoctets) réservée pour les opérations non cache telles que la réplication pendant le basculement. Il peut également être utilisé lorsque le taux de fragmentation est élevé. La définition de ce paramètre vous permet d'avoir une expérience de serveur Redis plus cohérente lorsque votre charge varie. Cette valeur doit être plus élevée pour les charges de travail comportant de nombreuses écritures. Lorsque la mémoire est réservée pour ces opérations, elle n'est pas disponible pour le stockage des données mises en cache.

>[AZURE.IMPORTANT]Le paramètre **maxmemory-reserved** est uniquement disponible pour les caches Standard.

## Notifications de Keyspace (paramètres avancés)

Cliquez sur **Paramètres avancés** pour configurer les notifications de keyspace Redis. Les notifications de keyspace permettent aux clients de recevoir des notifications lorsque certains événements se produisent.

![Paramètres avancés de Cache Redis](./media/cache-configure/IC808319.png)

>[AZURE.IMPORTANT]Les notifications de keyspace et le paramètre **notify-keyspace-events** sont uniquement disponibles pour les caches Standard.

Pour plus d'informations, consultez [Notifications de keyspace Redis](http://redis.io/topics/notifications). Pour un exemple de code, consultez le fichier [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) dans l’exemple [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

## Utilisateurs et balises

![Utilisateurs et balises de Cache Redis](./media/cache-configure/IC808320.png)

La section **Utilisateurs** fournit une prise en charge pour le contrôle d’accès en fonction du rôle (RBAC) dans le portail pour aider les entreprises à répondre aux exigences de gestion des accès de façon simple et précise. Pour plus d'informations, consultez la page [Contrôle d'accès en fonction du rôle dans le portail Microsoft Azure en version préliminaire](http://go.microsoft.com/fwlink/?LinkId=512803).

La section **Balises** vous aide à organiser vos ressources. Pour plus d’informations, voir [Organisation des ressources Azure à l’aide de balises](../resource-group-using-tags.md).

## Configuration du serveur Redis par défaut

Les nouvelles instances de Cache Redis Azure sont configurées avec les valeurs de configuration par défaut Redis suivantes.

>[AZURE.NOTE]Les paramètres de cette section ne peuvent pas être modifiés à l'aide de la méthode `StackExchange.Redis.IServer.ConfigSet`. Si cette méthode est appelée avec une des commandes de cette section, une exception semblable à la suivante est levée :
>
>`StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>  
>Toutes les valeurs configurables, telles que **max-memory-policy**, sont configurables via le portail.

|Paramètre|Valeur par défaut|Description|
|---|---|---|
|bases de données|16|La base de données par défaut est DB 0 ; vous pouvez en sélectionner une autre pour chaque connexion à l'aide de connection.GetDataBase(dbid), où dbid est un nombre compris entre 0 et 15.|
|maxclients|10 000|Le nombre maximal de clients connectés autorisés en même temps. Une fois la limite atteinte, Redis ferme toutes les nouvelles connexions en envoyant une erreur « nombre maximal de clients atteint ».|
|maxmemory-policy|volatile-lru|La stratégie maxmemory est le paramètre définissant la sélection par Redis des éléments à supprimer lorsque la mémoire maximale (la taille du cache que vous avez sélectionné lorsque vous avez créé le cache) est atteinte. Avec Cache Redis Azure, le paramètre par défaut est volatile-lru, qui supprime les clés avec une expiration définie à l'aide d'un algorithme dernier récemment utilisé (LRU). Ce paramètre peut être configuré dans le portail. Pour plus d'informations, consultez [Maxmemory-policy et maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved).|
|maxmemory-samples|3|Les algorithmes LRU et TTL ne sont pas précis mais estimés (afin d’économiser de la mémoire), vous pouvez donc sélectionner également la taille d’échantillon à vérifier. Par exemple, Redis vérifie par défaut trois clés et choisit celle qui a été utilisée il y a le plus longtemps.|
|lua-time-limit|5 000|Temps d'exécution maximal d'un script Lua en millisecondes. Si la durée d'exécution maximale est atteinte, Redis enregistre qu'un script est toujours en cours d’exécution après la durée maximale autorisée et commence à répondre aux requêtes avec une erreur.|
|lua-event-limit|500|Il s'agit de la taille maximale de la file d'attente des événements de script.|
|client-output-buffer-limit normalclient-output-buffer-limit pubsub|0 0 032mb 8mb 60|Les limites de mémoire tampon de sortie client peuvent servir à forcer la déconnexion des clients qui ne lisent pas les données à partir du serveur suffisamment rapidement pour une raison quelconque (une raison courante est qu'un client Pub/Sub ne peut pas consommer les messages aussi rapidement que le serveur de publication les génère). Pour plus d'informations, consultez [http://redis.io/topics/clients](http://redis.io/topics/clients).|

>[AZURE.IMPORTANT]Étant donné que la configuration et la gestion des instances de Cache Redis Azure s'effectuent à l'aide du portail Azure, les commandes suivantes sont désactivées. Si vous essayez de les utiliser, vous recevez un message d'erreur semblable à `"(error) ERR unknown command"`.
>
>-	BGREWRITEAOF
>-	BGSAVE
>-	CONFIG
>-	DEBUG
>-	MIGRATE
>-	ENREGISTRER
>-	SHUTDOWN
>-	SLAVEOF

Pour plus d'informations sur les commandes Redis, voir [http://redis.io/commands](http://redis.io/commands).

## Étapes suivantes
-	Pour plus d'informations sur l'utilisation des commandes Redis, voir [Exécution des commandes Redis](cache-faq.md#how-can-i-run-redis-commands).

<!---HONumber=July15_HO2-->