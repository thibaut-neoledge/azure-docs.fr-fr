<properties 
	pageTitle="Création et gestion du cache Redis Azure à l’aide de l’interface de ligne de commande Azure (Azure CLI)" 
	description="Cette rubrique décrit comment installer l’interface de ligne de commande Azure sur toute plateforme, comment l’utiliser pour se connecter à un compte Azure et comment créer un cache Redis à partir de cette interface." 
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
	ms.date="09/30/2015" 
	ms.author="sdanie"/>

# Création et gestion du cache Redis Azure à l’aide de l’interface de ligne de commande Azure (Azure CLI)

L'interface de ligne de commande Azure est un excellent moyen de gérer votre infrastructure Azure à partir de n'importe quelle plateforme. Cet article montre comment créer et gérer vos instances de cache Redis Azure à l’aide de l’interface de ligne de commande Azure.

## Composants requis

Pour créer et gérer des instances de cache Redis Azure à l’aide de l’interface de ligne de commande Azure, vous devez procéder comme suit.

-	Vous devez disposer d’un compte Azure. Si vous n’en avez pas, vous pouvez créer un [compte d’évaluation gratuit](http://azure.microsoft.com/pricing/free-trial/) dans quelques instants.
-	[Installez l'interface de ligne de commande Microsoft Azure](../xplat-cli-install.md).
-	Connectez votre installation d’interface de ligne de commande Azure à un compte Azure personnel ou à un compte Azure professionnel ou scolaire, puis connectez-vous à partir de l’interface de ligne de commande Azure à l’aide de la commande `azure login`. Pour comprendre les différences et faire votre choix, consultez [Se connecter à un abonnement Azure à partir de l'interface de ligne de commande Azure (Azure CLI)](../xplat-cli-connect.md).
-	Avant d'exécuter les commandes suivantes, basculez l'interface de ligne de commande Azure en mode Gestionnaire de ressources en exécutant la commande `azure config mode arm`. Pour plus d'informations, consultez [Définition du mode Azure Resource Manager](../virtual-machines/xplat-cli-azure-resource-manager.md#setting-the-azure-resource-manager-mode).

## Propriétés du cache Redis

Les propriétés suivantes sont utilisées lors de la création et de la mise à jour des instances de cache Redis.

| Propriété | Switch | Description |
|------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| name | -n, --name | Nom du cache Redis. |
| resource group | -g, --resource-group | Nom du groupe de ressources. |
| location | -l, --location | Emplacement où créer le cache. |
| size | -z, --size | Taille du cache Redis. Valeurs valides : [C0, C1, C2, C3, C4, C5, C6] |
| sku | -x, --sku | SKU Redis. Doit être l’une des deux valeurs : [Basic, Standard] |
| MaxMemoryPolicy | -m, --max-memory-policy | Propriété MaxMemoryPolicy du cache Redis. Valeurs valides : [AllKeysLRU, AllKeysRandom, NoEviction, VolatileLRU, VolatileRandom, VolatileTTL] |
| enableNonSslPort | -e, --enable-non-ssl-port | Propriété EnableNonSslPort du cache Redis. Ajoutez cet indicateur si vous souhaitez activer le port non-SSL pour votre cache |
| subscription | -s, --subscription | Identificateur de l’abonnement. |
| key type | -t, --key-type | Type de clé à renouveler. Valeurs valides : [Primary, Secondary] |

## Voir toutes les commandes de cache Redis

Pour afficher toutes les commandes de cache Redis et leurs paramètres, utilisez la commande `azure rediscache -h`.

	C:\>azure rediscache -h
	help:    Commands to manage your Azure Redis Cache(s)
	help:
	help:    Create a Redis Cache
	help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
	help:
	help:    Delete an existing Redis Cache
	help:      rediscache delete [--name <name> --resource-group <resource-group> ]
	help:
	help:    List all Redis Caches within your Subscription or Resource Group
	help:      rediscache list [options]
	help:
	help:    Show properties of an existing Redis Cache
	help:      rediscache show [--name <name> --resource-group <resource-group>]
	help:
	help:    Change settings of an existing Redis Cache
	help:      rediscache set [--name <name> --resource-group <resource-group> --max-memory-policy <max-memory-policy>]
	help:
	help:    Renew the authentication key for an existing Redis Cache
	help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
	help:
	help:    Lists Primary and Secondary key of an existing Redis Cache
	help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
	help:
	help:    Options:
	help:      -h, --help  output usage information
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Création d’un cache Redis

Pour créer un cache Redis, utilisez la commande suivante :

	azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Pour plus d’informations sur cette commande, exécutez la commande `azure rediscache create -h`.

	C:\>azure rediscache create -h
	help:    Create a Redis Cache
	help:
	help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
	help:
	help:    Options:
	help:      -h, --help                                   output usage information
	help:      -v, --verbose                                use verbose output
	help:      -vv                                          more verbose with debug output
	help:      --json                                       use json output
	help:      -n, --name <name>                            Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>        Name of the Resource Group
	help:      -l, --location <location>                    Location to create cache.
	help:      -z, --size <size>                            Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6]
	help:      -x, --sku <sku>                              Redis SKU. Should be one of : [Basic, Standard]
	help:      -m, --max-memory-policy <max-memory-policy>  MaxMemoryPolicy property of the Redis Cache. Valid values: [AllKeysLRU, AllKeysRandom, NoEviction, VolatileLRU, VolatileRandom, VolatileTTL]
	help:      -e, --enable-non-ssl-port                    EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
	help:      -s, --subscription <id>                      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Suppression d’un cache Redis

Pour supprimer un cache Redis, utilisez la commande suivante :

	azure rediscache delete [--name <name> --resource-group <resource-group> ]

Pour plus d’informations sur cette commande, exécutez la commande `azure rediscache delete -h`.

	C:\>azure rediscache delete -h
	help:    Delete an existing Redis Cache
	help:
	help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Liste de tous les caches Redis dans votre abonnement ou groupe de ressources

Pour répertorier tous les caches Redis dans votre abonnement ou groupe de ressources, utilisez la commande suivante :

	azure rediscache list [options]

Pour plus d’informations sur cette commande, exécutez la commande `azure rediscache list -h`.

	C:\>azure rediscache list -h
	help:    List all Redis Caches within your Subscription or Resource Group
	help:
	help:    Usage: rediscache list [options]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -g, --resource-group <resource-group>  Name of the Resource Group
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Affichage des propriétés d’un cache Redis existant

Pour afficher les propriétés d’un cache Redis existant, utilisez la commande suivante :

	azure rediscache show [--name <name> --resource-group <resource-group>]

Pour plus d’informations sur cette commande, exécutez la commande `azure rediscache show -h`.

	C:\>azure rediscache show -h
	help:    Show properties of an existing Redis Cache
	help:
	help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Modification des paramètres d’un cache Redis

Pour modifier les paramètres d’un cache Redis, utilisez la commande suivante :

	azure rediscache set [--name <name> --resource-group <resource-group> --max-memory-policy <max-memory-policy>]

Pour plus d’informations sur cette commande, exécutez la commande `azure rediscache set -h`.

	C:\>azure rediscache set -h
	help:    Change settings of an existing Redis Cache
	help:
	help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --max-memory-policy <max-memory-policy>]
	help:
	help:    Options:
	help:      -h, --help                                   output usage information
	help:      -v, --verbose                                use verbose output
	help:      -vv                                          more verbose with debug output
	help:      --json                                       use json output
	help:      -n, --name <name>                            Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>        Name of the Resource Group
	help:      -m, --max-memory-policy <max-memory-policy>  Max Memory Policy of the Redis Cache. Valid values: [AllKeysLRU, AllKeysRandom, NoEviction, VolatileLRU, VolatileRandom, VolatileTTL]
	help:      -s, --subscription <subscription>            the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Renouvellement de la clé d’authentification pour un cache Redis

Pour renouveler la clé d’authentification pour un cache Redis, utilisez la commande suivante :

	azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Spécifiez `Primary` ou `Secondary` pour `key-type`.

Pour plus d’informations sur cette commande, exécutez la commande `azure rediscache renew-key -h`.

	C:\>azure rediscache renew-key -h
	help:    Renew the authentication key for an existing Redis Cache
	help:
	help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
	help:      -t, --key-type <key-type>              type of key to renew
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Liste des clés primaire et secondaire d’un cache Redis

Pour répertorier les clés primaire et secondaire d’un cache Redis, utilisez la commande suivante :

	azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Pour plus d’informations sur cette commande, exécutez la commande `azure rediscache list-keys -h`.

	C:\>azure rediscache list-keys -h
	help:    Lists Primary and Secondary key of an existing Redis Cache
	help:
	help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

<!---HONumber=Oct15_HO3-->