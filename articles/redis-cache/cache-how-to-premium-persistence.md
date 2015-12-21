<properties 
	pageTitle="Comment configurer la persistance des données pour un Cache Redis Azure Premium" 
	description="Découvrez comment configurer et gérer la persistance des données pour vos instances de Cache Redis Azure de niveau Premium" 
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
	ms.date="12/03/2015" 
	ms.author="sdanie"/>

# Comment configurer la persistance des données pour un Cache Redis Azure Premium

Le Cache Redis Azure offre différents types de caches, permettant de choisir parmi plusieurs tailles et fonctionnalités de cache en toute flexibilité, y compris le nouveau niveau Premium.

Le niveau Premium de Cache Redis Azure comprend le clustering, la persistance et la prise en charge de réseau virtuel. Cet article décrit comment configurer la persistance dans une instance Premium de Cache Redis Azure.

Pour plus d’informations sur les autres fonctionnalités du cache Premium, consultez [Comment configurer le clustering pour un Cache Redis Azure Premium](cache-how-to-premium-clustering.md) et [Comment configurer la prise en charge de réseau virtuel pour un Cache Redis Azure Premium](cache-how-to-premium-vnet.md).

## Qu’est-ce que la persistance des données ?
La persistance Redis vous permet de conserver les données stockées dans Redis. Vous pouvez également prendre des instantanés et sauvegarder les données que vous pouvez charger en cas de défaillance matérielle. Il s’agit d’un avantage substantiel par rapport au niveau De base ou Standard, où toutes les données sont stockées en mémoire et il existe un risque de perte de données en cas de défaillance des nœuds de cache.

Le Cache Redis Azure offre une persistance Redis à l'aide du [modèle RDB](http://redis.io/topics/persistence) où les données sont stockées dans un compte de stockage Azure. Quand la persistance est configurée, le Cache Redis Azure conserve un instantané du cache Redis dans un format binaire Redis sur le disque selon une fréquence de sauvegarde configurable. Si un événement catastrophique se produit et provoque la désactivation du cache principal et du réplica, le cache est reconstruit à l’aide de l’instantané le plus récent.

La persistance peut être configurée à partir du panneau **Nouveau Cache Redis** lors de la création du cache et le panneau **Paramètres** pour les caches Premium existants.

## Création d'un cache Premium

Pour créer un cache et configurer la persistance, connectez-vous au [portail Azure](https://portal.azure.com) et cliquez sur **Nouveau**->**Données + stockage**>**Cache Redis**.

![Création d’un cache Redis][redis-cache-new-cache-menu]

Pour configurer la persistance, sélectionnez d'abord l'un des caches **Premium** dans le panneau **Choisir un niveau tarifaire**.

![Choisir un niveau tarifaire][redis-cache-premium-pricing-tier]

Une fois que vous avez sélectionné un niveau tarifaire Premium, cliquez sur **Persistance Redis**.

![Persistance Redis][redis-cache-persistence]

Les étapes de la section suivante décrivent comment configurer la persistance Redis sur votre nouveau cache Premium. Une fois la persistance Redis configurée, cliquez sur **Créer** pour créer votre cache Premium avec la persistance Redis.

## Configuration de la persistance Redis

Le panneau **Persistance des données Redis** permet de configurer la persistante Redis. Pour les nouveaux caches, ce panneau est accessible pendant la création du cache, comme décrit dans la section précédente. Pour les caches existants, le panneau **Persistance des données Redis** est accessible à partir du panneau **Paramètres** de votre cache.

![Paramètres Redis][redis-cache-settings]

Pour activer la persistance des données Redis, cliquez sur **Activé** pour activer la sauvegarde RDB (base de données Redis). Pour désactiver la persistance Redis sur un cache Premium précédemment activé, cliquez sur **Désactivé**.

Pour configurer l'intervalle de sauvegarde, sélectionnez une **Fréquence de sauvegarde** dans la liste déroulante. Vous avez le choix entre **15 minutes**, **30 minutes**, **60 minutes**, **6 heures**, **12 heures** et **24 heures**. Cet intervalle débute au moment où l’opération de sauvegarde précédente s’est terminée correctement. Une fois l’intervalle écoulé, une nouvelle sauvegarde est lancée.

Cliquez sur **Compte de stockage** pour sélectionner le compte de stockage à utiliser, puis, dans la liste déroulante **Clé de stockage**, choisissez la **Clé primaire** ou la **Clé secondaire** à utiliser. Vous devez choisir un compte de stockage situé dans la même région que le cache. Un compte **Premium Storage** est recommandé, car un stockage Premium offre un débit plus élevé.

>[AZURE.IMPORTANT]Si la clé de stockage pour votre compte de persistance est régénérée, vous devez choisir de nouveau la clé souhaitée dans la liste déroulante **Clé de stockage**.

![Persistance Redis][redis-cache-persistence-selected]

Cliquez sur **OK** pour enregistrer la configuration de persistance.

La sauvegarde suivante (ou première sauvegarde pour les nouveaux caches) est lancée une fois que l'intervalle de fréquence de sauvegarde est écoulé.



## Forum aux questions sur la persistance

La liste suivante présente différentes réponses aux questions les plus fréquemment posées sur la persistance du Cache Redis Azure.

## Puis-je activer la persistance sur un cache créé précédemment ?

Oui, la persistance Redis peut être configuré lors de la création du cache ou sur les caches Premium existants.

## Puis-je modifier la fréquence de sauvegarde après avoir créé le cache ?

Oui, vous pouvez modifier la fréquence de sauvegarde dans le panneau **Persistance des données Redis**. Pour obtenir des instructions, consultez la page [Configuration de la persistance Redis](#configure-redis-persistence).

## Pourquoi, si la fréquence de sauvegarde est de 60 minutes, y a-t-il un délai supérieur à 60 minutes entre les sauvegardes ?

L’intervalle de fréquence de sauvegarde ne démarre qu’une fois le processus de sauvegarde précédent terminé. Si la fréquence de sauvegarde est de 60 minutes et que l’exécution d’un processus de sauvegarde prend 15 minutes, la sauvegarde suivante ne démarre que 75 minutes après l’heure de début de la sauvegarde précédente.

## Qu’advient-il des anciennes sauvegardes quand une nouvelle sauvegarde est effectuée ?

Toutes les sauvegardes à l’exception de la plus récente sont supprimées automatiquement. Cette suppression peut ne pas avoir lieu immédiatement, mais les anciennes sauvegardes ne sont pas conservées indéfiniment.

## Étapes suivantes
Découvrez comment utiliser davantage de fonctionnalités de cache de niveau Premium.

-	[Comment configurer le clustering pour un Cache Redis Azure Premium](cache-how-to-premium-clustering.md)
-	[Comment configurer la prise en charge de réseau virtuel pour un Cache Redis Azure Premium](cache-how-to-premium-vnet.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-persistence/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-persistence-selected]: ./media/cache-how-to-premium-persistence/redis-cache-persistence-selected.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png

<!---HONumber=AcomDC_1210_2015-->