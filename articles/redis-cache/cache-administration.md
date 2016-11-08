---
title: Comment administrer le Cache Redis Azure | Microsoft Docs
description: Découvrez comment effectuer des tâches d’administration telles que le redémarrage et la planification de mises à jour pour le Cache Redis Azure
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn

ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 09/27/2016
ms.author: sdanie

---
# <a name="how-to-administer-azure-redis-cache"></a>Comment administrer le Cache Redis Azure
Cette rubrique décrit comment effectuer des tâches d’administration telles que le redémarrage et la planification des mises à jour de vos instances du Cache Redis Azure.

> [!IMPORTANT]
> Les paramètres et les fonctionnalités décrites dans cet article sont uniquement disponibles pour les caches de niveau Premium.
> 
> 

## <a name="administration-settings"></a>Paramètres d’administration
Les paramètres **d’administration** du Cache Redis Azure vous permettent d’effectuer les tâches administratives suivantes pour votre cache premium. Pour accéder aux paramètres d’administration, cliquez sur **Paramètres** ou **Tous les paramètres** à partir du panneau Cache Redis et faites défiler jusqu’à la section **Administration** dans le panneau **Paramètres**.

![Administration](./media/cache-administration/redis-cache-administration.png)

* [Reboot](#reboot)
* [Planification de mises à jour](#schedule-updates)

## <a name="reboot"></a>Reboot
Le panneau **Redémarrer** vous permet de redémarrer un ou plusieurs nœuds de votre cache. Cela vous permet de tester la résilience de votre application en cas d’échec.

![Reboot](./media/cache-administration/redis-cache-reboot.png)

Si vous avez un cache premium avec activation du clustering, vous pouvez sélectionner les partitions du cache à redémarrer.

![Reboot](./media/cache-administration/redis-cache-reboot-cluster.png)

Pour redémarrer un ou plusieurs nœuds de votre cache, sélectionnez les nœuds souhaités, puis cliquez sur **Reboot**. Si vous avez un cache premium avec activation du clustering, sélectionnez les partitions à redémarrer, puis cliquez sur **Reboot**. Après quelques minutes, les nœuds sélectionnés sont redémarrés et sont de nouveau en ligne.

L’impact sur les applications clientes varie selon le(s) nœud(s) que vous redémarrez.

* **Master** - lorsque le nœud principal est redémarré, le Cache Redis Azure bascule sur le nœud de réplica et le promeut au niveau Master. Pendant ce basculement, il peut y avoir un court intervalle pendant lequel les connexions au cache peuvent échouer.
* **Subordonné** - lorsque le nœud subordonné est redémarré, il n’y a généralement aucun impact sur les clients du cache.
* **Master et Subordonné** - lorsque les nœuds du cache sont redémarrés, toutes les données sont perdues dans le cache et les connexions au cache échouent jusqu’à ce que le nœud principal soit de nouveau en ligne. Si vous avez configuré la [persistance des données](cache-how-to-premium-persistence.md), la sauvegarde la plus récente est restaurée lorsque le cache repasse en ligne. Toutes les opération d’écriture dans le cache qui se sont produites après la dernière sauvegarde seront perdues.
* **Nœud(s) d’un cache premium avec activation du clustering** - lorsque vous redémarrez le(s) nœud(s) d’un cache premium avec le clustering activé, le comportement est le même que lorsque vous redémarrez un ou plusieurs nœuds d’un cache non-cluster.

> [!IMPORTANT]
> Le redémarrage est uniquement disponible pour les caches de niveau Premium.
> 
> 

## <a name="reboot-faq"></a>Forum aux questions sur le redémarrage
* [Quel nœud dois-je redémarrer pour tester mon application ?](#which-node-should-i-reboot-to-test-my-application)
* [Est-il possible de redémarrer le cache pour effacer les connexions client ?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Vais-je perdre les données dans mon cache si je redémarre ?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Est-il possible de redémarrer mon cache à l’aide de PowerShell, de l’interface de ligne de commande ou d’autres outils de gestion ?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
* [Quels niveaux tarifaires peuvent utiliser la fonctionnalité de redémarrage ?](#what-pricing-tiers-can-use-the-reboot-functionality)

### <a name="which-node-should-i-reboot-to-test-my-application?"></a>Quel nœud dois-je redémarrer pour tester mon application ?
Pour tester la résilience de votre application en cas de défaillance du nœud principal de votre cache, redémarrez le nœud **Master** . Pour tester la résilience de votre application en cas de défaillance du nœud secondaire de votre cache, redémarrez le nœud **Subordonné** . Pour tester la résilience de votre application en cas de défaillance totale de votre cache, redémarrez les **deux** nœuds.

### <a name="can-i-reboot-the-cache-to-clear-client-connections?"></a>Est-il possible de redémarrer le cache pour effacer les connexions client ?
Oui, toutes les connexions client sont effacées si vous réinitialisez le cache. Cette option est utile dans le cas où toutes les connexions client sont utilisées, par exemple en raison d’une erreur logique ou d’un bogue dans l’application cliente. Chaque niveau tarifaire a différentes [limites de connexion client](cache-configure.md#default-redis-server-configuration) pour les différentes tailles. Une fois ces limites sont atteintes, aucune autre connexion client supplémentaire n’est acceptée. Redémarrer le cache permet d’effacer toutes les connexions client.

> [!IMPORTANT]
> Si vos connexions client sont épuisées en raison d’une erreur logique ou d’un bogue dans votre code client, StackExchange.Redis se reconnectera automatiquement une fois le nœud Redis en ligne. Si le problème sous-jacent n’est pas résolu, les connexions client continueront à être utilisées.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot?"></a>Vais-je perdre les données dans mon cache si je redémarre ?
Si vous redémarrez à la fois les nœuds **Master** et **Subordonné**, toutes les données dans le cache (ou dans cette partition si vous utilisez un cache premium avec activation du clustering) sont perdues. Si vous avez configuré la [persistance des données](cache-how-to-premium-persistence.md), la sauvegarde la plus récente est restaurée lorsque le cache repasse en ligne. Toutes les opération d’écriture dans le cache qui se sont produites après la sauvegarde seront perdues.

Si vous redémarrez simplement l’un des nœuds, les données ne sont généralement pas perdues, mais elles peuvent toujours l’être. Par exemple, si le nœud principal est redémarré et qu’une opération d’écriture dans le cache est en cours, les données écrites dans le cache sont perdues. Vous êtes également susceptible de perdre des données lorsque vous redémarrez un nœud et que l’autre nœud se met hors service en même temps en raison d’une défaillance. Pour plus d’informations sur les causes possibles d’une perte de données, voir [Qu’est-il arrivé à mes données dans Redis ?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md).

### <a name="can-i-reboot-my-cache-using-powershell,-cli,-or-other-management-tools?"></a>Est-il possible de redémarrer mon cache à l’aide de PowerShell, de l’interface de ligne de commande ou d’autres outils de gestion ?
Oui, pour les instructions PowerShell, voir [Comment redémarrer un cache Redis](cache-howto-manage-redis-cache-powershell.md#to-reboot-a-redis-cache).

### <a name="what-pricing-tiers-can-use-the-reboot-functionality?"></a>Quels niveaux tarifaires peuvent utiliser la fonctionnalité de redémarrage ?
Le redémarrage n’est disponible que dans le niveau tarifaire Premium.

## <a name="schedule-updates"></a>Planification de mises à jour
Le panneau **Planification de mises à jour** vous permet de désigner une fenêtre de maintenance pour votre cache. Lorsque la fenêtre de maintenance est spécifiée, toute mise à jour du serveur Redis est effectuée pendant cet intervalle. Notez que la fenêtre de maintenance s’applique uniquement aux mises à jour du serveur Redis et non à toute mise à jour d’Azure ou du système d’exploitation des machines virtuelles qui hébergent le cache.

![Planification de mises à jour](./media/cache-administration/redis-schedule-updates.png)

Pour spécifier une fenêtre de maintenance, vérifiez les jours choisis et spécifiez l’heure de début de la fenêtre de maintenance pour chaque jour, puis cliquez sur **OK**. Notez que l’heure de la maintenance est au format UTC. 

> [!NOTE]
> La fenêtre de maintenance par défaut des mises à jour est de 5 heures. Cette valeur n’est pas configurable à partir du portail Azure, mais vous pouvez la configure dans PowerShell à l’aide du paramètre `MaintenanceWindow` de l’applet de commande [New-AzureRmRedisCacheScheduleEntry](https://msdn.microsoft.com/library/azure/mt763833.aspx) . Pour plus d’informations, voir [Puis-je gérer les mises à jour planifiées à l’aide de PowerShell, de l’interface de ligne de commande ou de tout autre outil de gestion ?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
> 
> 

## <a name="schedule-updates-faq"></a>Forum aux questions de la planification des mises à jour
* [Quand les mises à jour sont-elles effectuées si je n’utilise pas la fonctionnalité de planification des mises à jour ?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Quels types de mises à jour sont exécutés au cours de la fenêtre de maintenance planifiée ?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Puis-je gérer les mises à jour planifiées à l’aide de PowerShell, de l’interface de ligne de commande ou de tout autre outil de gestion ?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
* [Quels niveaux tarifaires peuvent utiliser la fonctionnalité de planification des mises à jour ?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-don't-use-the-schedule-updates-feature?"></a>Quand les mises à jour sont-elles effectuées si je n’utilise pas la fonctionnalité de planification des mises à jour ?
Si vous ne spécifiez pas une fenêtre de maintenance, les mises à jour peuvent être effectuées à tout moment.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window?"></a>Quels types de mises à jour sont exécutés au cours de la fenêtre de maintenance planifiée ?
Seules les mises à jour du serveur Redis sont exécutées au cours de la fenêtre de maintenance planifiée. La fenêtre de maintenance ne s’applique pas aux mises à jour d’Azure ou du système d’exploitation de la machine virtuelle.

### <a name="can-i-managed-scheduled-updates-using-powershell,-cli,-or-other-management-tools?"></a>Puis-je gérer les mises à jour planifiées à l’aide de PowerShell, de l’interface de ligne de commande ou de tout autre outil de gestion ?
Oui, vous pouvez gérer vos mises à jour planifiées à l’aide des applets de commande PowerShell suivantes.

* [Get-AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763835.aspx)
* [New-AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763834.aspx)
* [New-AzureRmRedisCacheScheduleEntry](https://msdn.microsoft.com/library/azure/mt763833.aspx)
* [Remove-AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763837.aspx)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality?"></a>Quels niveaux tarifaires peuvent utiliser la fonctionnalité de planification des mises à jour ?
La planification des mises à jour n’est disponible que dans le niveau tarifaire Premium.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez plus de fonctionnalités de [niveau Premium du cache Redis Azure](cache-premium-tier-intro.md) .

<!--HONumber=Oct16_HO2-->


