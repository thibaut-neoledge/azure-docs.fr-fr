<properties 
	pageTitle="Configuration de Cache Redis Azure"
	description="Comprendre la configuration de Redis par défaut pour Cache Redis Azure et apprendre à configurer vos instances de Cache Redis Azure"
	services="redis-cache"
	documentationCenter="na"
	authors="steved0x"
	manager="erikre"
	editor="tysonn" />
<tags 
	ms.service="cache"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="03/04/2016"
	ms.author="sdanie" />

# Configuration de Cache Redis Azure

Cette rubrique montre comment consulter et mettre à jour la configuration de vos instances de Cache Redis Azure et présente la configuration du serveur Redis par défaut pour les instances de Cache Redis Azure.

>[AZURE.NOTE] Pour plus d’informations sur la configuration et l’utilisation des fonctionnalités du cache Premium, voir [Comment configurer la persistance pour un Cache Redis Azure Premium](cache-how-to-premium-persistence.md), [Comment configurer le clustering pour un Cache Redis Azure Premium](cache-how-to-premium-clustering.md) et [Comment configurer la prise en charge de réseau virtuel pour un Cache Redis Azure Premium](cache-how-to-premium-vnet.md).

## Configuration des paramètres de cache Redis

Les caches sont accessibles dans le [portail Azure](https://portal.azure.com) à l’aide du panneau **Parcourir**.

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

## Clés d’accès

Cliquez sur **Clés d’accès** pour afficher ou régénérer les clés d’accès de votre cache. Ces clés sont utilisées avec le nom d’hôte et les ports à partir du panneau **Propriétés** par les clients se connectant à votre cache.

![Clés d’accès de Cache Redis](./media/cache-configure/IC808315.png)

## Ports d’accès

Par défaut, l’accès non SSL est désactivé pour les nouveaux caches. Pour activer le port non SSL, cliquez sur le panneau **Accès aux Ports**, puis sur **Non**.

![Ports d’accès de Cache Redis](./media/cache-configure/IC808316.png)

## Niveau de tarification

Cliquez sur **Niveau de tarification** pour afficher ou modifier le niveau de tarification de votre cache. Pour plus d’informations sur la mise à l’échelle, voir [Mise à l’échelle du cache Redis Azure](cache-how-to-scale.md).

![Niveau de tarification de Cache Redis](./media/cache-configure/pricing-tier.png)

## Diagnostics

Cliquez sur **Diagnostics** pour configurer le compte de stockage utilisé pour stocker les diagnostics de cache.

![Diagnostics de Cache Redis](./media/cache-configure/IC808317.png)

Pour plus d’informations, voir [Surveillance du cache Redis Azure](cache-how-to-monitor.md).

## Maxmemory-policy et maxmemory-reserved

Cliquez sur **Maxmemory policy** pour configurer les stratégies de mémoire pour le cache. Le paramètre **maxmemory-policy** configure la stratégie d’éviction du cache et **maxmemory-reserved** configure la mémoire réservée pour les processus non cache.

![Stratégie Maxmemory de Cache Redis](./media/cache-configure/IC808318.png)

**Maxmemory policy** vous permet de choisir parmi les stratégies d’éviction.

-	volatile-lru est la valeur par défaut.
-	allkeys-lru
-	volatile-random
-	allkeys-random
-	volatile-ttl
-	noeviction

Pour plus d’informations sur les stratégies maxmemory, voir [Stratégies d’éviction](http://redis.io/topics/lru-cache#eviction-policies).

Le paramètre **maxmemory-reserved** détermine la quantité de mémoire (en mégaoctets) réservée pour les opérations non cache telles que la réplication pendant le basculement. Il peut également être utilisé lorsque le taux de fragmentation est élevé. La définition de ce paramètre vous permet d’avoir une expérience de serveur Redis plus cohérente lorsque votre charge varie. Cette valeur doit être plus élevée pour les charges de travail comportant de nombreuses écritures. Lorsque la mémoire est réservée pour ces opérations, elle n’est pas disponible pour le stockage des données mises en cache.

>[AZURE.IMPORTANT] Le paramètre **maxmemory-reserved** est uniquement disponible pour les caches Standard et Premium.

## Notifications de Keyspace (paramètres avancés)

Cliquez sur **Paramètres avancés** pour configurer les notifications de keyspace Redis. Les notifications de keyspace permettent aux clients de recevoir des notifications lorsque certains événements se produisent.

![Paramètres avancés de Cache Redis](./media/cache-configure/IC808319.png)

>[AZURE.IMPORTANT] Les notifications de keyspace et le paramètre **notify-keyspace-events** sont uniquement disponibles pour les caches Standard et Premium.

Pour plus d’informations, voir [Notifications de keyspace Redis](http://redis.io/topics/notifications). Pour un exemple de code, voir le fichier [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) dans l’exemple [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

## Persistance des données Redis

Cliquez sur **Persistance des données Redis** pour activer, désactiver ou configurer la persistance des données de votre cache Premium.

![Persistance des données Redis](./media/cache-configure/redis-cache-persistence-settings.png)

Pour activer la persistance des données Redis, cliquez sur **Activé** pour activer la sauvegarde RDB (base de données Redis). Pour désactiver la persistance des données Redis, cliquez sur **Désactivé**.

Pour configurer l’intervalle de sauvegarde, sélectionnez une **Fréquence de sauvegarde** dans la liste déroulante. Vous avez le choix entre **15 minutes**, **30 minutes**, **60 minutes**, **6 heures**, **12 heures** et **24 heures**. Cet intervalle débute au moment où l’opération de sauvegarde précédente s’est terminée correctement. Une fois l’intervalle écoulé, une nouvelle sauvegarde est lancée.

Cliquez sur **Compte de stockage** pour sélectionner le compte de stockage à utiliser, puis, dans la liste déroulante **Clé de stockage**, choisissez la **Clé primaire** ou la **Clé secondaire** à utiliser. Vous devez choisir un compte de stockage situé dans la même région que le cache. Un compte **Premium Storage** est recommandé, car un stockage Premium offre un débit plus élevé. Chaque fois que la clé de stockage pour votre compte de persistance est régénérée, vous devez choisir de nouveau la clé souhaitée dans la liste déroulante **Clé de stockage**.

Cliquez sur **OK** pour enregistrer la configuration de la persistance.

>[AZURE.IMPORTANT] La persistance des données Redis est disponible uniquement pour les caches de niveau Premium. Pour plus d’informations, consultez [Comment configurer la persistance pour un Cache Redis Azure Premium](cache-how-to-premium-persistence.md).

<a name="cluster-size"></a>
## Taille du cluster Redis

Cliquez sur **Taille du cluster Redis (PREVIEW)** pour changer la taille du cluster pour un cache de niveau Premium en cours d’exécution avec le clustering activé.

>[AZURE.NOTE] Notez que si le niveau Premium du cache Redis Azure a été publié en disponibilité générale, la fonctionnalité Taille du cluster Redis est actuellement en version préliminaire.

![Taille du cluster Redis](./media/cache-configure/redis-cache-redis-cluster-size.png)

Pour changer la taille du cluster, utilisez le curseur ou entrez un nombre compris entre 1 et 10 dans la zone de texte **Nombre de partitions**, puis cliquez sur **OK** pour enregistrer.

>[AZURE.IMPORTANT] Le clustering Redis est disponible uniquement pour les caches de niveau Premium. Pour plus d’informations, consultez [Comment configurer le clustering pour un cache Redis Azure Premium](cache-how-to-premium-clustering.md).


## Utilisateurs et balises

![Utilisateurs et balises de Cache Redis](./media/cache-configure/IC808320.png)

La section **Utilisateurs** fournit une prise en charge pour le contrôle d’accès en fonction du rôle (RBAC) dans le portail Azure, pour aider les entreprises à répondre aux exigences de gestion des accès de façon simple et précise. Pour plus d’informations, consultez [Contrôle d’accès en fonction du rôle Azure](http://go.microsoft.com/fwlink/?LinkId=512803).

La section **Balises** vous aide à organiser vos ressources. Pour plus d’informations, voir [Organisation des ressources Azure à l’aide de balises](../resource-group-using-tags.md).

## Configuration du serveur Redis par défaut

Les nouvelles instances de Cache Redis Azure sont configurées avec les valeurs de configuration par défaut Redis suivantes.

>[AZURE.NOTE] Les paramètres de cette section ne peuvent pas être modifiés à l’aide de la méthode `StackExchange.Redis.IServer.ConfigSet`. Si cette méthode est appelée avec une des commandes de cette section, une exception semblable à la suivante est levée :
>
>`StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>  
>Toutes les valeurs configurables, comme **max-memory-policy**, peuvent être modifiées via la portail Azure ou via des outils de gestion en ligne de commande, comme Azure CLI ou PowerShell.

|Paramètre|Valeur par défaut|Description|
|---|---|---|
|bases de données|16|La base de données par défaut est DB 0 ; vous pouvez en sélectionner une autre pour chaque connexion à l’aide de connection.GetDataBase(dbid), où dbid est un nombre compris entre 0 et 15.|
|maxclients|Dépend du niveau tarifaire<sup>1</sup>|Le nombre maximal de clients connectés autorisés en même temps. Une fois la limite atteinte, Redis ferme toutes les nouvelles connexions en envoyant une erreur « nombre maximal de clients atteint ».|
|maxmemory-policy|volatile-lru|La stratégie maxmemory est le paramètre définissant la sélection par Redis des éléments à supprimer lorsque la mémoire maximale (la taille du cache que vous avez sélectionné lorsque vous avez créé le cache) est atteinte. Avec Cache Redis Azure, le paramètre par défaut est volatile-lru, qui supprime les clés avec une expiration définie à l’aide d’un algorithme dernier récemment utilisé (LRU). Ce paramètre peut être configuré dans le portail Azure. Pour plus d’informations, consultez [Maxmemory-policy et maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved).|
|maxmemory-samples|3|Les algorithmes LRU et TTL ne sont pas précis mais estimés (afin d’économiser de la mémoire), vous pouvez donc sélectionner également la taille d’échantillon à vérifier. Par exemple, Redis vérifie par défaut trois clés et choisit celle qui a été utilisée il y a le plus longtemps.|
|lua-time-limit|5 000|Temps d’exécution maximal d’un script Lua en millisecondes. Si la durée d’exécution maximale est atteinte, Redis enregistre qu’un script est toujours en cours d’exécution après la durée maximale autorisée et commence à répondre aux requêtes avec une erreur.|
|lua-event-limit|500|Il s’agit de la taille maximale de la file d’attente des événements de script.|
|client-output-buffer-limit normalclient-output-buffer-limit pubsub|0 0 032mb 8mb 60|Les limites de mémoire tampon de sortie client peuvent servir à forcer la déconnexion des clients qui ne lisent pas les données à partir du serveur suffisamment rapidement pour une raison quelconque (une raison courante est qu’un client Pub/Sub ne peut pas consommer les messages aussi rapidement que le serveur de publication les génère). Pour plus d’informations, consultez [http://redis.io/topics/clients](http://redis.io/topics/clients).|

<sup>1</sup>`maxclients` est différent pour chaque niveau tarifaire du cache Redis Azure.

-	Caches De base et Standard
	-	Cache C0 (250 Mo) : jusqu’à 256 connexions
	-	Cache C1 (1 Go) : jusqu’à 1 000 connexions
	-	Cache C2 (2,5 Go) : jusqu’à 2 000 connexions
	-	Cache C3 (6 Go) : jusqu’à 5 000 connexions
	-	Cache C4 (13 Go) : jusqu’à 10 000 connexions
	-	Cache C5 (26 Go) : jusqu’à 15 000 connexions
	-	Cache C6 (53 Go) : jusqu’à 20 000 connexions
-	Caches Premium
	-	P1 (6 Go - 60 Go) : jusqu’à 7 500 connexions
	-	P2 (13 Go - 130 Go) : jusqu’à 15 000 connexions
	-	P3 (26 Go - 260 Go) : jusqu’à 30 000 connexions
	-	P4 (53 Go - 530 Go) : jusqu’à 40 000 connexions

## Commandes Redis non prises en charge dans le Cache Redis Azure

>[AZURE.IMPORTANT] Étant donné que la configuration et la gestion des instances de cache Redis Azure sont gérées par Microsoft, les commandes suivantes sont désactivées. Si vous essayez de les utiliser, vous recevez un message d’erreur similaire à `"(error) ERR unknown command"`.
>
>-	BGREWRITEAOF
>-	BGSAVE
>-	CONFIG
>-	DEBUG
>-	MIGRATE
>-	ENREGISTRER
>-	SHUTDOWN
>-	SLAVEOF

Pour plus d’informations sur les commandes Redis, consultez [http://redis.io/commands](http://redis.io/commands).

## Console Redis

Vous pouvez adresser en toute sécurité des commandes aux instances de Cache Redis Azure à l’aide de la **console Redis** disponible pour les caches Standard et Premium.

>[AZURE.IMPORTANT] La Console Redis ne fonctionne pas avec le réseau virtuel ou le clustering.
>
>-	[Réseau virtuel](cache-how-to-premium-vnet.md) : quand votre cache fait partie d’un réseau virtuel, seuls les clients de ce réseau virtuel peuvent accéder au cache. Étant donné que la Console Redis utilise le client redis-cli.exe hébergé sur des machines virtuelles qui ne font pas partie de votre réseau virtuel, il ne peut pas se connecter à votre cache.
>-	[Clustering](cache-how-to-premium-clustering.md) : la Console Redis utilise le client redis-cli.exe, qui ne prend pas en charge le clustering à l’heure actuelle. L’utilitaire redis-cli de la branche [unstable](http://redis.io/download) du dépôt Redis sur GitHub implémente la prise en charge de base quand il est démarré avec le commutateur `-c`. Pour plus d’informations, consultez [Playing with the cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) sur [http://redis.io](http://redis.io) dans le [didacticiel de cluster Redis](http://redis.io/topics/cluster-tutorial).

Pour accéder à la console Redis, cliquez sur **Console** dans le panneau **Cache Redis**.

![Console Redis](./media/cache-configure/redis-console-menu.png)

Pour exécuter des commandes sur votre instance de cache, tapez simplement la commande souhaitée dans la console.

![Console Redis](./media/cache-configure/redis-console.png)

Pour obtenir la liste des commandes Redis désactivées pour le cache Redis Azure, consultez la section précédente intitulée [Commandes Redis non prises en charge dans le cache Redis Azure](#redis-commands-not-supported-in-azure-redis-cache). Pour plus d’informations sur les commandes Redis, consultez [http://redis.io/commands](http://redis.io/commands).

## Étapes suivantes
-	Pour plus d'informations sur l'utilisation des commandes Redis, consultez [Exécution des commandes Redis](cache-faq.md#how-can-i-run-redis-commands).

<!---HONumber=AcomDC_0309_2016-->