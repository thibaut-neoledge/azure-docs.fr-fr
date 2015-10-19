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
	ms.date="10/01/2015" 
	ms.author="sdanie"/>

# Comment configurer la persistance des données pour un Cache Redis Azure Premium

Le Cache Redis Azure offre différents types de caches, permettant de choisir parmi plusieurs tailles et fonctionnalités de caches en toute flexibilité, y compris le nouveau niveau Premium actuellement en version préliminaire.

Le niveau Premium de Cache Redis Azure comprend le clustering, la persistance et la prise en charge de réseau virtuel. Cet article décrit comment configurer la persistance dans une instance Premium de Cache Redis Azure.

Pour plus d’informations sur les autres fonctionnalités du cache Premium, consultez [Comment configurer le clustering pour un Cache Redis Azure Premium](cache-how-to-premium-clustering.md) et [Comment configurer la prise en charge de réseau virtuel pour un Cache Redis Azure Premium](cache-how-to-premium-vnet.md).

>[AZURE.NOTE]Le niveau Premium de Cache Redis Azure est actuellement en version préliminaire. Pendant la période d’évaluation, vous ne pouvez pas utiliser la persistance conjointement avec le clustering ou les réseaux virtuels.

## Qu’est-ce que la persistance des données ?
La persistance Redis vous permet de conserver les données stockées dans Redis. Vous pouvez également prendre des instantanés et sauvegarder les données que vous pouvez charger en cas de défaillance matérielle. Il s’agit d’un avantage substantiel par rapport au niveau De base ou Standard, où toutes les données sont stockées en mémoire et il existe un risque de perte de données en cas de défaillance des nœuds de cache.

Le Cache Redis Azure offre une persistance Redis où les données sont stockées dans un compte de stockage Azure. Pour la version préliminaire publique, nous prenons en charge le [modèle RDB](http://redis.io/topics/persistence) et nous prendrons bientôt en charge [AOF](http://redis.io/topics/persistence).

## Persistance des données
Quand la persistance est configurée, le Cache Redis Azure conserve un instantané du cache Redis dans un format binaire Redis sur le disque selon une fréquence de sauvegarde configurable. Si un événement catastrophique se produit et provoque la désactivation du cache principal et du réplica, le cache est reconstruit à l’aide de l’instantané le plus récent.

La configuration de la persistance s’effectue dans le panneau **Nouveau cache Redis** lors de la création du cache. Pour créer un cache, connectez-vous au [portail Azure en version préliminaire](https://portal.azure.com) et cliquez sur **Nouveau**->**Données + stockage**>**Cache Redis**.

![Création d’un cache Redis][redis-cache-new-cache-menu]

Pour configurer la persistance, sélectionnez d’abord l’un des caches **Premium** dans le panneau **Choisir un niveau tarifaire**.

![Choisir un niveau tarifaire][redis-cache-premium-pricing-tier]

Une fois que vous avez sélectionné un niveau tarifaire Premium, cliquez sur **Persistance Redis**.

![Persistance Redis][redis-cache-persistence]

Cliquez sur **Activé** pour activer la sauvegarde RDB (base de données Redis).

Sélectionnez une **Fréquence de sauvegarde** dans la liste déroulante. Vous avez le choix entre **60 minutes**, **6 heures**, **12 heures** et **24 heures**. Cet intervalle débute au moment où l’opération de sauvegarde précédente s’est terminée correctement. Une fois l’intervalle écoulé, une nouvelle sauvegarde est lancée.

Dans le champ **Compte de stockage**, sélectionnez le compte de stockage à utiliser. La **Clé de stockage** est renseignée automatiquement, mais si la clé est régénérée pour le compte de stockage, vous pouvez la modifier ici. Nous vous recommandons d’utiliser un compte **Premium Storage**, car le stockage Premium a un débit plus élevé.

>[AZURE.NOTE]AOF n’est pas disponible pendant la période d’évaluation de niveau Premium, mais l’équipe Cache travaille actuellement à l’ajout de cette fonctionnalité. Pour plus d’informations sur RDB et AOF et leurs avantages respectifs, consultez [Persistance Redis](http://redis.io/topics/persistence).

![Persistance Redis][redis-cache-persistence-selected]

Cliquez sur **OK** pour enregistrer la configuration de persistance.

Une fois le cache créé, la première sauvegarde est lancée une fois que l’intervalle de fréquence de sauvegarde est écoulé.

## Forum aux questions sur la persistance

La liste suivante présente différentes réponses aux questions les plus fréquemment posées sur la persistance du Cache Redis Azure.

## Puis-je activer la persistance sur un cache créé précédemment ?

Pendant la période d’évaluation, vous pouvez uniquement activer et configurer la persistance lors du processus de création d’un cache Premium. Pendant la période d’évaluation publique, la mise à l’échelle de la version De base/Standard vers l’édition Premium n’est pas prise en charge, mais elle le sera bientôt.

## Puis-je modifier la fréquence de sauvegarde après avoir créé le cache ?

Pendant la période d’évaluation, vous pouvez uniquement configurer la persistance lors du processus de création du cache. Pour modifier la configuration de la persistance, supprimez le cache et créez-en un nouveau avec la configuration de persistance souhaitée. Il s’agit d’une limitation de la version préliminaire. La prise en charge de la modification de la fréquence de sauvegarde après la création du cache sera bientôt disponible.

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

<!---HONumber=Oct15_HO2-->