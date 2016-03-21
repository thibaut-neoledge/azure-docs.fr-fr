<properties 
	pageTitle="Comment configurer la prise en charge de réseau virtuel pour un Cache Redis Azure Premium" 
	description="Découvrez comment créer et gérer la prise en charge de réseau virtuel pour vos instances de Cache Redis Azure de niveau Premium" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/04/2016" 
	ms.author="sdanie"/>

# Comment configurer la prise en charge de réseau virtuel pour un Cache Redis Azure Premium
Le Cache Redis Azure offre différents types de caches, permettant de choisir parmi plusieurs tailles et fonctionnalités de cache en toute flexibilité, y compris le nouveau niveau Premium.

Le niveau Premium de Cache Redis Azure comprend le clustering, la persistance et la prise en charge de réseau virtuel (VNET). Un réseau virtuel est une représentation de votre propre réseau dans le cloud. Lorsqu’une instance de Cache Redis Azure est configurée avec un réseau virtuel, elle n’est pas adressable publiquement et est accessible uniquement à partir de clients sur le réseau virtuel. Cet article décrit comment configurer la prise en charge de réseau virtuel pour une instance Premium de Cache Redis Azure.

Pour plus d’informations sur les autres fonctionnalités du cache Premium, voir [Comment configurer la persistance pour un Cache Redis Azure Premium](cache-how-to-premium-persistence.md) et [Comment configurer le clustering pour un Cache Redis Azure Premium](cache-how-to-premium-clustering.md).

## Pourquoi un réseau virtuel ?
le déploiement de [réseau virtuel Azure (VNET)](https://azure.microsoft.com/services/virtual-network/) fournit une sécurité et un isolement renforcés pour votre Cache Redis Azure, ainsi que des sous-réseaux, des stratégies de contrôle d’accès et d’autres fonctionnalités permettant de restreindre davantage l’accès au Cache Redis Azure.

## Prise en charge des réseaux virtuels
La configuration de la prise en charge du réseau virtuel s’effectue dans le panneau **Nouveau cache Redis** lors de la création du cache. Pour créer un cache, connectez-vous au [portail Azure](https://portal.azure.com) et cliquez sur **Nouveau**->**Données + stockage**>**Cache Redis**.

![Création d’un cache Redis][redis-cache-new-cache-menu]

Pour configurer la prise en charge de réseau virtuel, sélectionnez d’abord l’un des caches **Premium** dans le panneau **Choisir un niveau tarifaire**.

![Choisir un niveau tarifaire][redis-cache-premium-pricing-tier]

La configuration de l’intégration de réseau virtuel Cache Redis Azure s’effectue dans le panneau **Réseau virtuel (classique)**. Depuis ce panneau vous pouvez sélectionner un réseau virtuel classique existant. Pour utiliser un nouveau réseau virtuel, procédez de la manière décrite dans [Créer un réseau virtuel (classique) à l’aide du portail Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md), puis revenez au panneau **Réseau virtuel de Cache Redis** pour le sélectionner.

>[AZURE.NOTE] Cache Redis Azure fonctionne avec les réseaux virtuels classiques. Pour plus d’informations sur la création d’un réseau virtuel classique, consultez [Créer un réseau virtuel (classique) à l’aide du portail Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Pour obtenir des informations sur la connexion de réseaux virtuels classiques à des réseaux virtuels ARM, consultez [Connexion de réseaux virtuels classiques aux nouveaux réseaux virtuels](../virtual-network/virtual-networks-arm-asm-s2s.md).

Cliquez sur **Réseau virtuel (classique)** dans le panneau **Nouveau cache Redis**, puis sélectionnez le réseau souhaité dans la liste déroulante pour sélectionner et configurer votre réseau virtuel.

![Réseau virtuel][redis-cache-vnet]

Sélectionnez le sous-réseau souhaité dans la liste déroulante **Sous-réseau**.

![Réseau virtuel][redis-cache-vnet-ip]

Le champ **Adresse IP statique** champ est facultatif. Si rien n’est spécifié, une option sera choisie dans le sous-réseau sélectionné. Si vous souhaitez indiquer une adresse IP statique spécifique, entrez l’**Adresse IP statique** souhaitée, puis cliquez sur **OK** pour enregistrer la configuration du réseau virtuel. Si l’adresse IP statique sélectionnée est déjà utilisée, un message d’erreur s’affiche.

Une fois le cache est créé, vous pouvez afficher l'adresse IP et d’autres informations concernant le réseau virtuel en cliquant sur **Réseau virtuel** dans le panneau **Paramètres**.

![Réseau virtuel][redis-cache-vnet-info]

>[AZURE.IMPORTANT] Pour accéder à votre instance de Cache Redis Azure lors de l’utilisation d’un réseau virtuel, passez l’adresse IP statique du cache dans le réseau virtuel comme premier paramètre, puis passez un paramètre `sslhost` avec le point de terminaison de votre cache. Dans l’exemple suivant, l’adresse IP statique est `172.160.0.99` et le point de terminaison du cache est `contoso5.redis.cache.windows.net`.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("172.160.0.99,sslhost=contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

## Forum aux questions sur le réseau virtuel de Cache Redis Azure

La liste suivante présente différentes réponses aux questions les plus fréquemment posées sur la mise à l’échelle du Cache Redis Azure.

## Quels sont les problèmes de configuration les plus courants au niveau du Cache Redis Azure et des réseaux virtuels ?

Lorsque le Cache Redis Azure est hébergé dans un réseau virtuel, les ports du tableau suivant sont utilisés. Si ces ports sont bloqués, le cache risque de ne pas fonctionner correctement. Le blocage d’un ou plusieurs de ces ports constitue le problème de configuration le plus courant lorsque vous utilisez le Cache Redis Azure dans un réseau virtuel.

| Port(s) | Direction | Protocole de transfert | Objectif | Adresse IP distante |
|-------------|------------------|--------------------|-----------------------------------------------------------------------------------|-------------------------------------|
| 80, 443 | Règle de trafic sortant | TCP | Dépendances Redis sur Azure Storage/l’infrastructure à clé publique (Internet) | * |
| 53 | Règle de trafic sortant | TCP/UDP | Dépendances Redis sur le DNS (Internet/réseau virtuel) | * |
| 6379, 6380 | Trafic entrant | TCP | Communication client avec Redis, équilibrage de charge Azure | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 8443 | Entrant/sortant | TCP | Détail d’implémentation de Redis | VIRTUAL\_NETWORK |
| 8500 | Trafic entrant | TCP/UDP | Équilibrage de charge Azure | AZURE\_LOADBALANCER |
| 10221-10231 | Entrant/sortant | TCP | Détail d’implémentation de Redis (peut restreindre le point de terminaison distant à VIRTUAL\_NETWORK) | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 13000-13999 | Trafic entrant | TCP | Communication client avec les clusters Redis, équilibrage de charge Azure | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 15000-15999 | Trafic entrant | TCP | Communication client avec les clusters Redis, équilibrage de charge Azure | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 16001 | Trafic entrant | TCP/UDP | Équilibrage de charge Azure | AZURE\_LOADBALANCER |
| 20226 | Entrant+sortant | TCP | Détail d’implémentation des clusters Redis | VIRTUAL\_NETWORK |




## Puis-je utiliser des réseaux virtuels avec un cache De base ou Standard ?

Vous ne pouvez utiliser des réseaux virtuels qu’avec les caches de niveau Premium.

## Étapes suivantes
Découvrez comment utiliser davantage de fonctionnalités de cache de niveau Premium.

-	[Comment configurer la persistance pour un Cache Redis Azure Premium](cache-how-to-premium-persistence.md)
-	[Comment configurer le clustering pour un Cache Redis Azure Premium](cache-how-to-premium-clustering.md)





  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-vnet/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-vnet/redis-cache-premium-pricing-tier.png

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

<!---HONumber=AcomDC_0309_2016-->