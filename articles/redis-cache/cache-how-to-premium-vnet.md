<properties 
	pageTitle="Comment configurer la prise en charge de réseau virtuel pour un Cache Redis Azure Premium" 
	description="Découvrez comment créer et gérer la prise en charge de réseau virtuel pour vos instances de Cache Redis Azure de niveau Premium" 
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
	ms.date="12/02/2015" 
	ms.author="sdanie"/>

# Comment configurer la prise en charge de réseau virtuel pour un Cache Redis Azure Premium
Le Cache Redis Azure offre différents types de caches, permettant de choisir parmi plusieurs tailles et fonctionnalités de cache en toute flexibilité, y compris le nouveau niveau Premium.

Le niveau Premium de Cache Redis Azure comprend le clustering, la persistance et la prise en charge de réseau virtuel (VNET). Un réseau virtuel est une représentation de votre propre réseau dans le cloud. Lorsqu’une instance de Cache Redis Azure est configurée avec un réseau virtuel, elle n’est pas adressable publiquement et est accessible uniquement à partir de clients sur le réseau virtuel. Cet article décrit comment configurer la prise en charge de réseau virtuel pour une instance Premium de Cache Redis Azure.

Pour plus d’informations sur les autres fonctionnalités du cache Premium, voir [Comment configurer la persistance pour un Cache Redis Azure Premium](cache-how-to-premium-persistence.md) et [Comment configurer le clustering pour un Cache Redis Azure Premium](cache-how-to-premium-clustering.md).

## Pourquoi un réseau virtuel ?
le déploiement de [réseau virtuel Azure (VNET)](https://azure.microsoft.com/services/virtual-network/) fournit une sécurité et un isolement renforcés pour votre Cache Redis Azure, ainsi que des sous-réseaux, des stratégies de contrôle d’accès et d’autres fonctionnalités permettant de restreindre davantage l’accès au Cache Redis Azure.

## Prise en charge des réseaux virtuels
La configuration de la prise en charge du réseau virtuel s’effectue dans le panneau **Nouveau cache Redis** lors de la création du cache. Pour créer un cache, connectez-vous au [portail Azure en version préliminaire](https://portal.azure.com), puis cliquez sur **Nouveau**->**Données + stockage**>**Cache Redis**.

![Création d’un cache Redis][redis-cache-new-cache-menu]

Pour configurer la prise en charge de réseau virtuel, commencez par sélectionner l’un des caches **Premium** dans le panneau **Choisir un niveau tarifaire**.

![Choisir un niveau tarifaire][redis-cache-premium-pricing-tier]

La configuration de l’intégration de réseau virtuel Cache Redis Azure s’effectue dans le panneau **Réseau virtuel**. Depuis ce panneau vous pouvez sélectionner un réseau virtuel classique existant. Pour utiliser un nouveau réseau virtuel, procédez de la manière décrite dans [Création d’un réseau virtuel (classique) à l’aide du portail Azure en version préliminaire](../virtual-network/virtual-networks-create-vnet-classic-pportal.md), puis revenez au panneau **Réseau virtuel de Cache Redis** pour le sélectionner.

>[AZURE.NOTE]Cache Redis Azure fonctionne avec les réseaux virtuels classiques. Pour plus d’informations sur la création d’un réseau virtuel classique, voir [Création d’un réseau virtuel (classique) à l’aide du portail Azure en version préliminaire](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

![Réseau virtuel][redis-cache-vnet]

Cliquez sur **Réseau virtuel** dans le panneau **Réseau virtuel** pour sélectionner et configurer votre réseau virtuel.

![Réseau virtuel][redis-cache-vnet-select]

Cliquez sur le réseau virtuel souhaité pour le sélectionner.

![Réseau virtuel][redis-cache-vnet-subnet]

Cliquez sur Sous-réseau pour sélectionner le sous-réseau souhaité.

![Réseau virtuel][redis-cache-vnet-ip]

Tapez l’**Adresse IP statique** souhaitée, puis cliquez sur **OK** pour enregistrer la configuration du réseau virtuel. Si l’adresse IP statique sélectionnée est déjà utilisée, un message d’erreur s’affiche.

Une fois le cache créé, il est accessible uniquement par les clients du même réseau virtuel.

>[AZURE.IMPORTANT]Pour accéder à votre instance Cache Redis Azure lors de l’utilisation d’un réseau virtuel, transmettez l’adresse IP statique du cache dans le réseau virtuel comme premier paramètre, puis transmettez un paramètre `sslhost` avec le point de terminaison de votre cache. Dans l’exemple suivant, l’adresse IP statique est `10.10.1.5` et le point de terminaison du cache est `contoso5.redis.cache.windows.net`.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("10.10.1.5,sslhost=contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

## Forum aux questions sur le réseau virtuel de Cache Redis Azure

La liste suivante présente différentes réponses aux questions les plus fréquemment posées sur la mise à l’échelle du Cache Redis Azure.

## Quels sont les problèmes de configuration les plus courants au niveau du Cache Redis Azure et des réseaux virtuels ?

La liste suivante présente des erreurs de configuration courantes qui peuvent empêcher le bon fonctionnement du Cache Redis Azure.

-	Blocage des ports TCP utilisés par les clients pour se connecter à Redis, c’est-à-dire 6379 ou 6380.
-	Blocage ou interception du trafic HTTPS sortant à partir du réseau virtuel. Le Cache Redis Azure utilise le trafic HTTPS sortant vers les services Azure, notamment le service de stockage.
-	Machines virtuelles d’instance de rôle Redis incapables de communiquer entre elles à l’intérieur du sous-réseau. Les instances de rôle Redis doivent être autorisées à communiquer entre elles à l’aide du protocole TCP sur n’importe lequel des ports utilisés (qui peuvent être soumis à modification, mais doivent au moins correspondre à tous les ports utilisés dans le fichier CSDEF Redis).
-	Impossibilité pour l’équilibreur de charge Azure de se connecter aux machines virtuelles Redis sur le port TCP/HTTP 16001. Le Cache Redis Azure dépend de la sonde d’équilibreur de charge Azure par défaut pour déterminer quelles instances de rôle sont actives. La sonde d’équilibreur de charge Azure par défaut envoie une requête ping à l’Agent invité Azure sur le port 16001. Seules les instances de rôle qui répondent à la requête ping sont placées en rotation pour recevoir le trafic transféré par l’équilibrage de charge. Quand aucune instance n’est en rotation, car les requêtes ping échouent à cause du blocage des ports, l’équilibrage de charge n’accepte aucune connexion TCP entrante.
-	Blocage du trafic web de l’application cliente utilisé pour la validation de la clé publique SSL. Les clients de Redis (sur le réseau virtuel) doivent pouvoir envoyer du trafic HTTP à l’Internet public pour télécharger des certificats d’autorité de certification et des listes de révocation de certificat pour effectuer la validation de certificat SSL quand ils utilisent le port 6380 pour se connecter à Redis et procéder à l’authentification de serveur SSL.
-	Impossibilité pour l’équilibreur de charge Azure de se connecter aux machines virtuelles Redis dans un cluster via TCP sur le port les 1300x (13 000, 13001, etc.) ou 1500x (15000, 15001, etc.). Les réseaux virtuels sont configurés dans le fichier csdef avec une sonde d’équilibreur de charge pour ouvrir ces ports. L’équilibreur de charge Azure doit être autorisé par les groupes de sécurité réseau. Les groupes de sécurité réseau par défaut effectuent cette opération à l’aide de la balise AZURE\_LOADBALANCER. L’équilibreur de charge Azure a une seule adresse IP statique de 168.63.126.16. Pour plus d’informations, voir [Présentation du groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md).

## Puis-je utiliser des réseaux virtuels avec un cache De base ou Standard ?

Vous ne pouvez utiliser des réseaux virtuels qu’avec les caches de niveau Premium.

## Étapes suivantes
Découvrez comment utiliser davantage de fonctionnalités de cache de niveau Premium.

-	[Comment configurer la persistance pour un Cache Redis Azure Premium](cache-how-to-premium-persistence.md)
-	[Comment configurer le clustering pour un Cache Redis Azure Premium](cache-how-to-premium-clustering.md)





  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-vnet/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-vnet/redis-cache-premium-pricing-tier.png

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-select]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-select.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-subnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-subnet.png

<!---HONumber=AcomDC_1203_2015-->