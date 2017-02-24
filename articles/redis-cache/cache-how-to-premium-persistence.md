---
title: "Comment configurer la persistance des données pour un Cache Redis Azure Premium"
description: "Découvrez comment configurer et gérer la persistance des données pour vos instances de Cache Redis Azure de niveau Premium"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: b01cf279-60a0-4711-8c5f-af22d9540d38
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 50d8db29ccce1244387f1fe0e3e42e610575e483
ms.openlocfilehash: bc8c54b51f9eee653fbe84351081dcef562e62d4


---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-redis-cache"></a>Comment configurer la persistance des données pour un Cache Redis Azure Premium
Le Cache Redis Azure offre différents types de caches permettant de choisir en toute flexibilité parmi plusieurs tailles et fonctionnalités de caches en toute flexibilité, notamment les fonctionnalités de couche Premium telles que le clustering, la persistance et la prise en charge du réseau virtuel. Cet article décrit comment configurer la persistance dans une instance Premium de Cache Redis Azure.

Pour plus d’informations sur les autres fonctionnalités du cache Premium, consultez [Introduction au niveau Premium du Cache Redis Azure](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>Qu’est-ce que la persistance des données ?
La persistance Redis vous permet de conserver les données stockées dans Redis. Vous pouvez également prendre des instantanés et sauvegarder les données que vous pouvez charger en cas de défaillance matérielle. Il s’agit d’un avantage substantiel par rapport au niveau De base ou Standard, où toutes les données sont stockées en mémoire et il existe un risque de perte de données en cas de défaillance des nœuds de cache. 

Le Cache Redis Azure offre une persistance Redis à l'aide du [modèle RDB](http://redis.io/topics/persistence)où les données sont stockées dans un compte de stockage Azure. Quand la persistance est configurée, le Cache Redis Azure conserve un instantané du cache Redis dans un format binaire Redis sur le disque selon une fréquence de sauvegarde configurable. Si un événement catastrophique se produit et provoque la désactivation du cache principal et du réplica, le cache est reconstruit à l’aide de l’instantané le plus récent.

La persistance peut être configurée à partir du panneau **Nouveau Cache Redis** lors de la création du cache et du **menu Ressource** pour les caches Premium existants.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Une fois que vous avez sélectionné un niveau tarifaire Premium, cliquez sur **Persistance Redis**.

![Persistance Redis][redis-cache-persistence]

Les étapes de la section suivante décrivent comment configurer la persistance Redis sur votre nouveau cache Premium. Une fois la persistance Redis configurée, cliquez sur **Créer** pour créer votre cache Premium avec la persistance Redis.

## <a name="configure-redis-persistence"></a>Configuration de la persistance Redis
Le panneau **Persistance des données Redis** permet de configurer la persistante Redis. Pour les nouveaux caches, ce panneau est accessible pendant la création du cache, comme décrit dans la section précédente. Pour les caches existants, le panneau **Persistance des données Redis** est accessible à partir du **menu Ressource** de votre cache.

![Paramètres Redis][redis-cache-settings]

Pour activer la persistance des données Redis, cliquez sur **Activé** pour activer la sauvegarde RDB (base de données Redis). Pour désactiver la persistance Redis sur un cache Premium précédemment activé, cliquez sur **Désactivé**.

Pour configurer l’intervalle de sauvegarde, sélectionnez une **Fréquence de sauvegarde** dans la liste déroulante. Vous avez le choix entre **15 minutes**, **30 minutes**, **60 minutes**, **6 heures**, **12 heures** et **24 heures**. Cet intervalle débute au moment où l’opération de sauvegarde précédente s’est terminée correctement. Une fois l’intervalle écoulé, une nouvelle sauvegarde est lancée.

Cliquez sur **Compte de stockage** pour sélectionner le compte de stockage à utiliser, puis, dans la liste déroulante **Clé de stockage**, choisissez d’utiliser la **Clé primaire** ou la **Clé secondaire**. Vous devez choisir un compte de stockage situé dans la même région que le cache. Un compte **Stockage Premium** est recommandé, car ce type de stockage offre un débit plus élevé. 

> [!IMPORTANT]
> Si la clé de stockage pour votre compte de persistance est régénérée, vous devez reconfigurer la clé souhaitée dans la liste déroulante **Clé de stockage** .
> 
> 

![Persistance Redis][redis-cache-persistence-selected]

Cliquez sur **OK** pour enregistrer la configuration de persistance.

La sauvegarde suivante (ou première sauvegarde pour les nouveaux caches) est lancée une fois que l'intervalle de fréquence de sauvegarde est écoulé.

## <a name="persistence-faq"></a>Forum aux questions sur la persistance
La liste suivante présente différentes réponses aux questions les plus fréquemment posées sur la persistance du Cache Redis Azure.

* [Puis-je activer la persistance sur un cache créé précédemment ?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Puis-je modifier la fréquence de sauvegarde après avoir créé le cache ?](#can-i-change-the-backup-frequency-after-i-create-the-cache)
* [Pourquoi, si la fréquence de sauvegarde est de 60 minutes, y a-t-il un délai supérieur à 60 minutes entre les sauvegardes ?](#why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Qu’advient-il des anciennes sauvegardes quand une nouvelle sauvegarde est effectuée ?](#what-happens-to-the-old-backups-when-a-new-backup-is-made)
* [Que se passe-t-il si j’ai mis à l’échelle vers une taille différente et si une sauvegarde antérieure à l’opération de mise à l’échelle, est restaurée ?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Puis-je activer la persistance sur un cache créé précédemment ?
Oui, la persistance Redis peut être configurée lors de la création du cache ou sur les caches Premium existants.

### <a name="can-i-change-the-backup-frequency-after-i-create-the-cache"></a>Puis-je modifier la fréquence de sauvegarde après avoir créé le cache ?
Oui, vous pouvez modifier la fréquence de sauvegarde dans le panneau **Persistance des données Redis** . Pour obtenir des instructions, consultez la page [Configuration de la persistance Redis](#configure-redis-persistence).

### <a name="why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Pourquoi, si la fréquence de sauvegarde est de 60 minutes, y a-t-il un délai supérieur à 60 minutes entre les sauvegardes ?
L’intervalle de fréquence de sauvegarde ne démarre qu’une fois le processus de sauvegarde précédent terminé. Si la fréquence de sauvegarde est de 60 minutes et que l’exécution d’un processus de sauvegarde prend 15 minutes, la sauvegarde suivante ne démarre que 75 minutes après l’heure de début de la sauvegarde précédente.

### <a name="what-happens-to-the-old-backups-when-a-new-backup-is-made"></a>Qu’advient-il des anciennes sauvegardes quand une nouvelle sauvegarde est effectuée ?
Toutes les sauvegardes à l’exception de la plus récente sont supprimées automatiquement. Cette suppression peut ne pas avoir lieu immédiatement, mais les anciennes sauvegardes ne sont pas conservées indéfiniment.

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Que se passe-t-il si j’ai mis à l’échelle vers une taille différente et si une sauvegarde antérieure à l’opération de mise à l’échelle, est restaurée ?
* Si vous avez mis à l’échelle vers une plus grande taille, cela n’a aucun impact.
* Si vous avez mis à l’échelle vers une taille plus petite et que vous avez un paramètre personnalisé de [bases de données](cache-configure.md#databases) supérieur à la [limite des bases de données](cache-configure.md#databases) pour votre nouvelle taille, les données de ces bases de données ne sont pas restaurées. Pour en savoir plus, voir [Les paramètres personnalisés de mes bases de données sont-ils affectés au cours de la mise à l’échelle ?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Si vous avez mis à l’échelle vers une plus petite taille et que l’espace est insuffisant pour contenir toutes les données issues de la dernière sauvegarde, les clés sont supprimées lors du processus de restauration, généralement à l’aide de la stratégie d’éviction [allkeys-lru](http://redis.io/topics/lru-cache) .

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment utiliser davantage de fonctionnalités de cache de niveau Premium.

* [Introduction au niveau Premium du Cache Redis Azure](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-persistence/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-persistence-selected]: ./media/cache-how-to-premium-persistence/redis-cache-persistence-selected.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png



<!--HONumber=Feb17_HO2-->


