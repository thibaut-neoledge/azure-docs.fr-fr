<properties 
	pageTitle="Introduction au niveau Premium du Cache Redis Azure" 
	description="Découvrez comment créer et gérer la persistance et le clustering Redis, ainsi que la prise de charge VNET pour vos instances de Cache Redis Azure de niveau Premium." 
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
	ms.date="04/27/2016" 
	ms.author="sdanie"/>

# Introduction au niveau Premium du Cache Redis Azure
le Cache Redis Azure est un cache distribué et géré qui vous permet de développer des applications hautement évolutives et réactives en fournissant un accès ultra-rapide aux données.

Le nouveau niveau Premium est un niveau professionnel qui inclut toutes les fonctionnalités de niveau Standard, auxquelles s’ajoutent entre-autre une amélioration des performances, un accroissement des charges de travail, une récupération d’urgence plus réactive et une sécurité renforcée. Poursuivez la lecture pour en savoir plus sur les fonctionnalités supplémentaires du niveau de cache Premium.

## Amélioration des performances par rapport au niveau Standard ou De base
**Amélioration des performances par rapport au niveau Standard ou De base.** Les caches au niveau Premium sont déployés sur du matériel qui dispose de processeurs plus rapides et offrent de meilleures performances par rapport au niveau De base ou Standard. Les caches de niveau Premium ont un débit supérieur et des latences moindres.

**Pour un cache de même dimension, le débit du niveau Premium est plus élevé que celui du niveau Standard.** Par exemple, pour un cache de 53 Go, le débit P4 (Premium) est de 250 000 demandes par seconde par rapport à 150 000 demandes par seconde pour le débit C6 (Standard).

Pour plus d’informations sur la taille, le débit et la bande passante des caches Premium, consultez le [Forum aux questions sur le Cache Redis Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## Persistance des données Redis
Le niveau Premium vous permet de conserver les données du cache dans un compte de stockage Azure. Dans un cache De base/Standard, toutes les données sont stockées uniquement dans la mémoire. En cas de problème lié à l’infrastructure sous-jacente, il existe un risque de perte de données. Nous vous recommandons d’utiliser la fonctionnalité de persistance des données Redis disponible au niveau Premium pour accroître la résilience contre la perte de données. Le Cache Redis Azure propose des options RDB et AOF (à venir) de [persistance Redis](http://redis.io/topics/persistence).

Pour obtenir des instructions sur la configuration de la persistance, consultez [Comment configurer la persistance pour un Cache Redis Azure Premium](cache-how-to-premium-persistence.md).

##Cluster Redis
Si vous souhaitez créer des caches de plus de 53 Go ou partager des données parmi plusieurs nœuds Redis, vous pouvez utiliser le clustering Redis disponible au niveau Premium. Chaque nœud se compose d’une paire de caches principal/réplica géré par Azure pour la haute disponibilité.

**Le clustering Redis permet un débit et une capacité d'évolutivité maximum.** Le débit augmente de façon linéaire à mesure que vous augmentez le nombre de partitions (nœuds) dans le cluster. Par exemple, si vous créez un cluster P4 de 10 partitions, le débit disponible est alors de 250 000 * 10 = 2,5 millions de demandes par seconde. Pour plus d’informations sur la taille, le débit et la bande passante des caches Premium, consultez le [Forum aux questions sur le Cache Redis Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

Pour commencer votre clustering, consultez [Comment configurer le clustering pour un Cache Redis Azure Premium](cache-how-to-premium-clustering.md).

##Amélioration de la sécurité et de l’isolation
Les caches créés avec les niveau De base ou Standard sont accessibles sur Internet. L’accès au cache est limité en fonction de la clé d’accès. Avec le niveau Premium, vous pouvez vous assurer que seuls les clients d’un réseau spécifié peuvent accéder au cache. Vous pouvez déployer le Cache Redis dans un [réseau virtuel Azure (VNet)](https://azure.microsoft.com/services/virtual-network/). Vous pouvez utiliser toutes les fonctionnalités de VNet, comme les sous-réseaux, les stratégies de contrôle d’accès et d’autres fonctionnalités pour améliorer la restriction d’accès à Redis.

Pour plus d’informations, consultez [Comment configurer la prise en charge de réseau virtuel pour un Cache Redis Azure Premium](cache-how-to-premium-vnet.md).

## Étapes suivantes

Créez un cache et explorez les nouvelles fonctionnalités de niveau Premium.

-	[Comment configurer la persistance pour un Cache Redis Azure Premium](cache-how-to-premium-persistence.md)
-	[Comment configurer la prise en charge de réseau virtuel pour un Cache Redis Azure Premium](cache-how-to-premium-vnet.md)
-	[Comment configurer le clustering pour un Cache Redis Azure Premium](cache-how-to-premium-clustering.md)
  

<!---HONumber=AcomDC_0504_2016-->