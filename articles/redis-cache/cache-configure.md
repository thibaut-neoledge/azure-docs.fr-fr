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
	ms.date="03/10/2016"
	ms.author="sdanie" />

# Configuration de Cache Redis Azure

Cette rubrique montre comment consulter et mettre à jour la configuration de vos instances de Cache Redis Azure et présente la configuration du serveur Redis par défaut pour les instances de Cache Redis Azure.

>[AZURE.NOTE] Pour plus d’informations sur la configuration et l’utilisation des fonctionnalités du cache Premium, voir [Comment configurer la persistance pour un Cache Redis Azure Premium](cache-how-to-premium-persistence.md), [Comment configurer le clustering pour un Cache Redis Azure Premium](cache-how-to-premium-clustering.md) et [Comment configurer la prise en charge de réseau virtuel pour un Cache Redis Azure Premium](cache-how-to-premium-vnet.md).

## Configuration des paramètres de cache Redis

Les caches sont accessibles dans le [portail Azure](https://portal.azure.com) à l’aide du panneau **Parcourir**.

![Panneau de navigation Cache Redis Azure](./media/cache-configure/redis-cache-browse.png)

Cliquez sur **Caches Redis** pour afficher vos caches. Si vous avez récemment accédé à un cache Redis, vous pouvez cliquer sur les **caches Redis** directement dans la liste sans cliquer sur **Parcourir**.

![Liste de cache de navigation Cache Redis Azure](./media/cache-configure/redis-caches.png)

Sélectionnez le cache souhaité pour afficher ses propriétés.

![Tous les paramètres de Cache Redis](./media/cache-configure/redis-cache-blade.png)

Cliquez sur **Paramètres** ou **Tous les paramètres** pour afficher et configurer votre cache.

![Paramètres de Cache Redis](./media/cache-configure/redis-cache-settings.png)

Cache Redis Azure fournit les paramètres suivants dans le panneau **Paramètres**.

-	[Paramètres de support et dépannage](#support-amp-troubleshooting-settings)
-	[Paramètres généraux :](#general-settings)
	-	[Propriétés](#properties)
	-	[Clés d’accès](#access-keys)
	-	[Ports d’accès](#access-ports)
	-	[Stratégie Maxmemory](#maxmemory-policy-and-maxmemory-reserved)
	-	[Paramètres avancés (notifications d’espace de clés)](#keyspace-notifications-advanced-settings)
	-	[Recommandations](#recommendations)
-	[Paramètres de gestion des données](#data-management-settings)
	-	[Persistance des données Redis](#redis-data-persistence)
-	[Paramètres réseau](#network-settings)
-	[Paramètres de diagnostic](#diagnostics-settings)
-	[Paramètres de mise à l'échelle](#scale-settings)
	-	[Niveau de tarification](#pricing-tier)
	-	[Taille du cluster Redis](#cluster-size)
-	[Paramètres de gestion des ressources](#resource-management-settings)

## Paramètres de support et dépannage

Cliquez sur **Journaux d’audit** pour afficher les actions effectuées sur votre cache. Vous pouvez également utiliser le filtrage pour développer cette vue afin d’inclure d’autres ressources. Pour plus d’informations sur l’utilisation des journaux d’audit, consultez [Affichage des événements et journaux d’audit](../azure-portal/insights-debugging-with-events.md) et [Opérations d’audit avec Resource Manager](../resource-group-audit.md). Pour plus d’informations sur la surveillance des événements du Cache Redis Azure, consultez [Opérations et alertes](cache-how-to-monitor.md#operations-and-alerts).

## Paramètres généraux :

Les paramètres de la section **Général** vous permettent d’accéder aux paramètres suivants et de les configurer pour votre cache.

![Paramètres généraux :](./media/cache-configure/redis-cache-general-settings.png)

### Properties

Cliquez sur **Propriétés** pour afficher des informations sur le cache, y compris le point de terminaison et les ports du cache.

![Propriétés de Cache Redis](./media/cache-configure/redis-cache-properties.png)

### Clés d’accès

Cliquez sur **Clés d’accès** pour afficher ou régénérer les clés d’accès de votre cache. Ces clés sont utilisées avec le nom d’hôte et les ports à partir du panneau **Propriétés** par les clients se connectant à votre cache.

![Clés d’accès de Cache Redis](./media/cache-configure/redis-cache-manage-keys.png)

### Ports d’accès

Par défaut, l’accès non SSL est désactivé pour les nouveaux caches. Pour activer le port non SSL, cliquez sur le panneau **Accès aux Ports**, puis sur **Non**.

![Ports d’accès de Cache Redis](./media/cache-configure/redis-cache-access-ports.png)

### Maxmemory-policy et maxmemory-reserved

Cliquez sur **Maxmemory policy** pour configurer les stratégies de mémoire pour le cache. Le paramètre **maxmemory-policy** configure la stratégie d’éviction du cache et **maxmemory-reserved** configure la mémoire réservée pour les processus non cache.

![Stratégie Maxmemory de Cache Redis](./media/cache-configure/redis-cache-maxmemory-policy.png)

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

### Notifications de Keyspace (paramètres avancés)

Cliquez sur **Paramètres avancés** pour configurer les notifications de keyspace Redis. Les notifications de keyspace permettent aux clients de recevoir des notifications lorsque certains événements se produisent.

![Paramètres avancés de Cache Redis](./media/cache-configure/redis-cache-advanced-settings.png)

>[AZURE.IMPORTANT] Les notifications de keyspace et le paramètre **notify-keyspace-events** sont uniquement disponibles pour les caches Standard et Premium.

Pour plus d’informations, voir [Notifications de keyspace Redis](http://redis.io/topics/notifications). Pour un exemple de code, voir le fichier [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) dans l’exemple [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

### Recommandations

Le panneau **Recommandations** affiche des recommandations pour votre cache. Pendant les opérations normales, aucune recommandation n’est affichée.

![Recommandations](./media/cache-configure/redis-cache-no-recommendations.png)

Si une condition se produit pendant les opérations de votre cache, par exemple utilisation élevée de la mémoire, bande passante réseau ou charge du serveur, une alerte s’affiche dans le panneau **Cache Redis**.

![Recommandations](./media/cache-configure/redis-cache-recommendations-alert.png)

Des informations complémentaires sont disponibles dans le panneau **Recommandations**.

![Recommandations](./media/cache-configure/redis-cache-recommendations.png)

Vous pouvez surveiller ces mesures à l’aide des sections [Graphiques de surveillance](cache-how-to-monitor.md#monitoring-charts) et [Graphiques d’utilisation](cache-how-to-monitor.md#usage-charts) du panneau **Cache Redis**.

Chaque niveau tarifaire est associé à des limites spécifiques concernant les connexions clientes, la mémoire et la bande passante. Si votre cache est proche de la capacité maximale pour ces mesures pendant une période prolongée, une recommandation est créée. Pour plus d’informations sur les mesures et les limites vérifiées par l’outil **Recommandations**, consultez le tableau suivant.

| Mesure du Cache Redis | Pour plus d’informations, consultez : |
|-------------------------|---------------------------------------------------------------------------|
| Utilisation de la bande passante réseau | [Performances du cache - Bande passante disponible](cache-faq.md#cache-performance) |
| Clients connectés | [Configuration du serveur Redis par défaut - maxclients](#maxclients) |
| Charge du serveur | [Graphiques d’utilisation - Charge du serveur Redis](cache-how-to-monitor.md#usage-charts) |
| Utilisation de la mémoire | [Performance du cache - Taille](cache-faq.md#cache-performance) |

Pour mettre à niveau votre cache, cliquez sur **Mettre à niveau maintenant** afin de modifier le [niveau tarifaire](#pricing-tier) et de mettre votre cache à l’échelle. Pour plus d’informations sur le choix d’un niveau tarifaire, consultez la réponse à la question [Que propose Cache Redis et quelle taille dois-je utiliser ?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## Paramètres de gestion des données

Les paramètres de la section **Gestion des données** vous permettent d’accéder aux paramètres suivants et de les configurer pour votre cache.

![Gestion des données](./media/cache-configure/redis-cache-data-management.png)

### Persistance des données Redis

Cliquez sur **Persistance des données Redis** pour activer, désactiver ou configurer la persistance des données de votre cache Premium.

![Persistance des données Redis](./media/cache-configure/redis-cache-persistence-settings.png)

Pour activer la persistance des données Redis, cliquez sur **Activé** pour activer la sauvegarde RDB (base de données Redis). Pour désactiver la persistance des données Redis, cliquez sur **Désactivé**.

Pour configurer l’intervalle de sauvegarde, sélectionnez une **Fréquence de sauvegarde** dans la liste déroulante. Vous avez le choix entre **15 minutes**, **30 minutes**, **60 minutes**, **6 heures**, **12 heures** et **24 heures**. Cet intervalle débute au moment où l’opération de sauvegarde précédente s’est terminée correctement. Une fois l’intervalle écoulé, une nouvelle sauvegarde est lancée.

Cliquez sur **Compte de stockage** pour sélectionner le compte de stockage à utiliser, puis, dans la liste déroulante **Clé de stockage**, choisissez la **Clé primaire** ou la **Clé secondaire** à utiliser. Vous devez choisir un compte de stockage situé dans la même région que le cache. Un compte **Premium Storage** est recommandé, car un stockage Premium offre un débit plus élevé. Chaque fois que la clé de stockage pour votre compte de persistance est régénérée, vous devez choisir de nouveau la clé souhaitée dans la liste déroulante **Clé de stockage**.

Cliquez sur **OK** pour enregistrer la configuration de la persistance.

>[AZURE.IMPORTANT] La persistance des données Redis est disponible uniquement pour les caches de niveau Premium. Pour plus d’informations, consultez [Comment configurer la persistance pour un Cache Redis Azure Premium](cache-how-to-premium-persistence.md).

## Paramètres réseau

Les paramètres de la section **Réseau** vous permettent d’accéder aux paramètres suivants et de les configurer pour votre cache.

![Réseau](./media/cache-configure/redis-cache-network.png)

>[AZURE.IMPORTANT] Les paramètres de réseau virtuel sont disponibles uniquement pour les caches Premium qui ont été configurés avec la prise en charge de réseau virtuel lors de la création du cache. Pour plus d’informations sur la création d’un cache Premium avec la prise en charge de réseau virtuel et sur la mise à jour de ses paramètres, consultez [Comment configurer la prise en charge de réseau virtuel pour un Cache Redis Azure Premium](cache-how-to-premium-vnet.md).

## Paramètres de diagnostic

La section **Diagnostics** vous permet de configurer les diagnostics de votre Cache Redis.

![Diagnostics](./media/cache-configure/redis-cache-diagnostics.png)

Cliquez sur **Diagnostics** pour configurer le compte de stockage utilisé pour stocker les diagnostics de cache.

![Diagnostics de Cache Redis](./media/cache-configure/redis-cache-diagnostics-settings.png)

Pour plus d’informations, voir [Surveillance du cache Redis Azure](cache-how-to-monitor.md).

## Paramètres de mise à l'échelle

Les paramètres de la section **Mise à l’échelle** vous permettent d’accéder aux paramètres suivants et de les configurer pour votre cache.

![Réseau](./media/cache-configure/redis-cache-scale.png)

### Niveau de tarification

Cliquez sur **Niveau de tarification** pour afficher ou modifier le niveau de tarification de votre cache. Pour plus d’informations sur la mise à l’échelle, voir [Mise à l’échelle du cache Redis Azure](cache-how-to-scale.md).

![Niveau de tarification de Cache Redis](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>
### Taille du cluster Redis

Cliquez sur **Taille du cluster Redis (PREVIEW)** pour changer la taille du cluster pour un cache de niveau Premium en cours d’exécution avec le clustering activé.

>[AZURE.NOTE] Notez que si le niveau Premium du cache Redis Azure a été publié en disponibilité générale, la fonctionnalité Taille du cluster Redis est actuellement en version préliminaire.

![Taille du cluster Redis](./media/cache-configure/redis-cache-redis-cluster-size.png)

Pour changer la taille du cluster, utilisez le curseur ou entrez un nombre compris entre 1 et 10 dans la zone de texte **Nombre de partitions**, puis cliquez sur **OK** pour enregistrer.

>[AZURE.IMPORTANT] Le clustering Redis est disponible uniquement pour les caches de niveau Premium. Pour plus d’informations, consultez [Comment configurer le clustering pour un cache Redis Azure Premium](cache-how-to-premium-clustering.md).


## Paramètres de gestion des ressources

![Utilisateurs et balises de Cache Redis](./media/cache-configure/redis-cache-resource-management.png)

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

<a name="maxclients"></a> <sup>1</sup>`maxclients` est différent pour chaque niveau tarifaire de Cache Redis Azure.

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

## Déplacement du cache vers un nouvel abonnement

Pour déplacer votre cache vers un nouvel abonnement, cliquez sur **Déplacer**.

![Déplacer le Cache Redis](./media/cache-configure/redis-cache-move.png)

Pour plus d’informations sur le déplacement des ressources d’un groupe de ressources vers un autre et d’un abonnement vers un autre, consultez [Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement](../resource-group-move-resources.md).

## Étapes suivantes
-	Pour plus d'informations sur l'utilisation des commandes Redis, consultez [Exécution des commandes Redis](cache-faq.md#how-can-i-run-redis-commands).

<!-----HONumber=AcomDC_0316_2016-->