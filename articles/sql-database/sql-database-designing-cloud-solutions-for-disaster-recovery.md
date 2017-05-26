---
title: "Concevoir des services hautement disponibles à l’aide d’Azure SQL Database | Microsoft Docs"
description: "Découvrez comment concevoir des applications pour des services hautement disponibles à l’aide d’Azure SQL Database."
keywords: "récupération d’urgence cloud, solutions de récupération d’urgence, sauvegarde de données d’application, géo-réplication, planification de la continuité des activités"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: e8a346ac-dd08-41e7-9685-46cebca04582
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 04/21/2017
ms.author: sashan
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: b1b67a83a25159414a80382030903d300aad71f7
ms.contentlocale: fr-fr
ms.lasthandoff: 05/18/2017


---
# <a name="designing-highly-available-services-using-azure-sql-database"></a>Conception de services hautement disponibles à l’aide d’Azure SQL Database

Pour créer et déployer des services hautement disponibles sur Azure SQL Database, vous utilisez des [groupes de basculement et un géoréplication active](sql-database-geo-replication-overview.md) pour assurer une tolérance aux défaillances régionales et aux pannes catastrophiques, ainsi que permettre une récupération rapide des bases de données secondaires. Cet article se concentre sur des modèles d’application courants et présente les avantages et inconvénients de chaque option en fonction des exigences liées au déploiement d’application, du contrat de niveau de service ciblé, de la latence du trafic et des coûts. Pour plus d’informations sur la géoréplication active avec des pools élastiques, voir [Stratégies de récupération d’urgence de pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

## <a name="design-pattern-1-active-passive-deployment-for-cloud-disaster-recovery-with-a-co-located-database"></a>Modèle de conception 1 : Déploiement actif/passif pour la récupération d’urgence cloud avec une base de données colocalisée
Cette option est idéale pour les applications dotées des caractéristiques suivantes :

* Instance active dans une seule région Azure
* Forte dépendance à l’accès aux données en lecture-écriture (RW)
* Connectivité dans plusieurs régions entre l’application web et la base de données non acceptable en raison de la latence et du coût du trafic    

Dans ce cas, la topologie de déploiement d’applications est optimisée pour la gestion des sinistres régionaux lorsque tous les composants d’application sont concernés et doivent être basculés en tant qu’unité. Pour la redondance géographique, la logique d’application et la base de données sont répliquées vers une autre région, mais elles ne sont pas utilisées pour la charge de travail de l’application dans des conditions normales. L’application dans la région secondaire doit être configurée pour utiliser une chaîne de connexion SQL sur la base de données secondaire. Traffic Manager est configuré pour utiliser la [méthode de routage du trafic par basculement](../traffic-manager/traffic-manager-configure-failover-routing-method.md).  

> [!NOTE]
> [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) est utilisé dans cet article aux fins d’illustration uniquement. Vous pouvez utiliser toute solution d’équilibrage de charge qui prend en charge la méthode de routage du trafic par basculement.    
>

Le diagramme suivant illustre cette configuration avant une panne.

![Configuration de la géoréplication de SQL Database. Récupération d’urgence cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-1.png)

Après une panne dans la région primaire, le service SQL Database détecte que la base de données primaire n’est pas accessible et déclenche un basculement vers la base de données secondaire conformément aux paramètres de la stratégie de basculement automatique. Selon le contrat de niveau de service (SLA) de votre application, vous pouvez choisir de configurer une période de grâce entre la détection de la panne et le basculement proprement dit. La configuration d’une période de grâce permet de réduire le risque de perte de données dans les cas où la défaillance est grave et la disponibilité dans la région ne peut pas être restaurée rapidement. Si le basculement de point de terminaison est initié par Traffic Manager avant que le groupe de basculement déclenche le basculement de la base de données, l’application web ne peut plus de se reconnecter à la base de données. La tentative de reconnexion de l’application réussit automatiquement dès que le basculement de la base de données est terminé. 

> [!NOTE]
> Pour bénéficier d’un basculement entièrement coordonné de l’application et des bases de données, vous devez concevoir votre propre méthode d’analyse et utiliser le basculement manuel des points de terminaison de l’application web et des bases de données.
>

Une fois le basculement des points de terminaison de l’application et de la base de données terminé, l’application reprend le traitement des requêtes utilisateur dans la région B et reste colocalisée avec la base de données, car la base de données primaire se trouve à présent dans la région B. Ce scénario est illustré dans le diagramme suivant. Dans tous les diagrammes, des lignes pleines indiquent les connexions actives, des lignes en pointillé indiquent les connexions suspendues et les symboles « stop » indiquent les déclencheurs d’action.

![Géoréplication : basculement vers la base de données secondaire. Sauvegarde de données d’application.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-2.png)

Si une panne se produit dans la région secondaire, le lien de réplication entre les bases de données primaire et secondaire est interrompu, mais le basculement n’est pas déclenché, car la base de données primaire n’est pas affectée. L’application reste alors disponible, mais elle est exposée et court donc un risque plus élevé en cas de défaillances successives dans les deux régions.

> [!NOTE]
> Nous recommandons les configurations de déploiement uniquement avec une seule région de récupération d’urgence. En effet, la plupart des zones géographiques Azure comptent deux régions. Ces configurations ne protègent pas votre application d’une défaillance irrémédiable des deux régions. Dans le cas peu probable d’une telle défaillance, vous pouvez restaurer vos bases de données dans une région tierce à l’aide de [l’opération de géo-restauration](sql-database-disaster-recovery.md#recover-using-geo-restore).
>

Une fois la panne résolue, la base de données secondaire se resynchronise automatiquement avec la base de données primaire. Lors de la synchronisation, les performances de la base de données primaire peuvent être légèrement affectées selon la quantité de données à synchroniser. Le diagramme suivant illustre une panne dans la région secondaire.

![Synchronisation de la base de données secondaire avec la base de données primaire. Récupération d’urgence cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-3.png)

Les **avantages** clés de ce modèle de conception sont les suivants :

* La même application web est déployée dans les deux régions sans configuration régionale spécifique et aucune logique supplémentaire pour réagir au basculement. 
* Les performances de l’application ne sont pas affectées par le basculement, car l’application web et la base de données sont toujours colocalisées.

Le principal **inconvénient** est que l’instance d’application redondante dans la région secondaire est uniquement utilisée pour la récupération d’urgence.

## <a name="design-pattern-2-active-active-deployment-for-application-load-balancing"></a>Modèle de conception 2 : Déploiement actif / actif pour l’équilibrage de charge d’applications
Cette option de récupération d’urgence cloud est idéale pour les applications dotées des caractéristiques suivantes :

* Ratio élevé de lectures de base de données par rapport aux écritures.
* La latence de lecture de la base de données est plus importante pour l’expérience de l’utilisateur final que la latence d’écriture. 
* La logique de lecture seule peut être séparée de la logique de lecture-écriture à l’aide d’une chaîne de connexion différente.
* La logique de lecture seule ne dépend pas de la synchronisation intégrale des données avec les dernières mises à jour.  

Si vos applications possèdent ces caractéristiques, l’équilibrage de charge des connexions de l’utilisateur final entre plusieurs instances d’application dans différentes régions peut améliorer considérablement l’expérience globale de l’utilisateur final. Deux des régions doivent être sélectionnées en tant que paire de récupération d’urgence et le groupe de basculement doit inclure les bases de données de ces régions. Pour implémenter l’équilibrage de charge, chaque région doit disposer d’une instance active de l’application avec la logique de lecture-écriture (RW) connectée au point de terminaison de l’écouteur de lecture-écriture du groupe de basculement. Le basculement sera ainsi lancé automatiquement si la base de données primaire est affectée par une panne. La logique de lecture seule (RO) dans l’application web doit se connecter directement à la base de données de cette région. Traffic Manager doit être configuré pour utiliser le [routage de performances](../traffic-manager/traffic-manager-configure-performance-routing-method.md) avec la [surveillance de point de terminaison](../traffic-manager/traffic-manager-monitoring.md) activée pour chaque instance d’application.

Comme dans le modèle 1, vous devez envisager le déploiement d’une application de surveillance similaire. Mais, contrairement au modèle 1, l’application de surveillance ne sera pas chargée de déclencher le basculement du point de terminaison.

> [!NOTE]
> Bien que ce modèle utilise plusieurs bases de données secondaires, seule la base de données secondaire de la région B sera utilisée pour le basculement et doit faire partie du groupe de basculement.
>

Traffic Manager doit être configuré pour que le routage de performances dirige les connexions utilisateur vers l’instance d’application la plus proche de l’emplacement géographique de l’utilisateur. Le diagramme suivant illustre cette configuration avant une panne.

![Aucune panne : routage des performances vers l’application la plus proche. Géoréplication.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-1.png)

Si une panne de base de données est détectée dans la région A, le groupe de basculement lance automatiquement le basculement de la base de données primaire de la région A vers la base de données secondaire de la région B. Il met aussi automatiquement à jour le point de terminaison de l’écouteur de lecture-écriture vers la région B afin d’empêcher toute incidence sur les connexions de lecture-écriture dans l’application web. Traffic Manager exclut le point de terminaison hors connexion de la table de routage, mais continue le routage du trafic de l’utilisateur final vers les instances en ligne restantes. Les chaînes de connexion SQL en lecture seule ne sont pas affectées, car elles pointent toujours vers la base de données de la même région. 

Le diagramme suivant illustre la nouvelle configuration après le basculement.

![Configuration après le basculement. Récupération d’urgence cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-2.png)

En cas de panne dans l’une des régions secondaires, Traffic Manager supprime automatiquement le point de terminaison hors connexion de cette région de la table de routage. Le canal de réplication vers la base de données secondaire de cette région est interrompu. Étant donné que les régions restantes récupèrent du trafic utilisateur supplémentaire dans ce scénario, les performances de l’application sont affectées pendant la panne. Une fois la panne résolue, la base de données secondaire de la région impactée est immédiatement synchronisée avec la base de données primaire. Lors de la synchronisation, les performances de la base de données primaire peuvent être légèrement affectées selon la quantité de données à synchroniser. Le diagramme suivant illustre une panne dans la région B.

![Panne dans une région secondaire. Récupération d’urgence cloud - géoréplication.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-3.png)

**L’avantage** clé de ce modèle de conception est que vous pouvez mettre à l’échelle la charge de travail de l’application sur plusieurs régions secondaires pour assurer des performances optimales à l’utilisateur final. Les **inconvénients** de cette option sont les suivants :

* Les connexions de lecture-écriture entre les instances de l’application et la base de données présentent des latences et des coûts variables.
* Les performances de l’application sont affectées pendant une panne.

> [!NOTE]
> Une approche similaire peut être utilisée pour décharger les charges de travail spécialisées telles que les travaux de création de rapports, les outils d’analyse décisionnelle ou les sauvegardes. En général, ces charges de travail consomment une quantité importante de ressources de base de données. Il est donc recommandé de désigner l’une des bases de données secondaires pour celles-ci, en mettant en correspondance le niveau de performances avec la charge de travail attendue.
>

## <a name="design-pattern-3-active-passive-deployment-for-data-preservation"></a>Modèle de conception 3 : Déploiement actif / passif pour la conservation des données
Cette option est idéale pour les applications dotées des caractéristiques suivantes :

* Toute perte de données constitue un risque élevé pour l’entreprise. Le basculement de la base de données ne peut être utilisé qu’en dernier recours si la panne est grave.
* L’application prend en charge les opérations en modes lecture seule et lecture-écriture et peut fonctionner en mode lecture seule pendant une période donnée.

Dans ce modèle, l’application bascule vers le mode lecture seule lorsque les connexions de lecture-écriture commencent à recevoir des erreurs d’expiration du délai d’attente. L’application web est déployée dans les deux régions et inclut une connexion au point de terminaison de l’écouteur de lecture-écriture et une autre connexion au point de terminaison de l’écouteur de lecture seule. Traffic Manager doit être configuré pour utiliser le [routage de basculement](../traffic-manager/traffic-manager-configure-failover-routing-method.md) avec la [surveillance de point de terminaison](../traffic-manager/traffic-manager-monitoring.md) activée pour le point de terminaison de l’application dans chaque région.

Le diagramme suivant illustre cette configuration avant une panne.

![Déploiement actif/passif avant le basculement. Récupération d’urgence cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-1.png)

Si Traffic Manager détecte une défaillance de connectivité pour la région A, il bascule automatiquement le trafic utilisateur vers l’instance de l’application dans la région B. Avec ce modèle, il est important de définir la période de grâce en cas de risque de perte de données sur une valeur suffisamment élevée, par exemple 24 heures. Vous empêcherez ainsi toute perte de données si la panne est résolue durant ce laps de temps. Lorsque l’application web de la région B est activée, les opérations en lecture-écriture se mettent à échouer. À ce stade, elle doit basculer vers le mode lecture seule. Dans ce mode, les requêtes sont automatiquement acheminées vers la base de données secondaire. En cas de défaillance grave, la panne ne sera pas résolue avant la fin de la période de grâce et le groupe de basculement déclenchera le basculement. Après cela, l’écouteur de lecture-écriture sera disponible et les appels vers celui-ci cesseront d’échouer. C’est ce qu’indique le diagramme suivant.

![Panne : l’application en mode lecture seule. Récupération d’urgence cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-2.png)

Si la panne qui affecte la région primaire est résolue avant la fin de la période de grâce, Traffic Manager détecte la restauration de la connectivité dans la région primaire et rebascule le trafic utilisateur vers l’instance de l’application dans la région A. Cette instance de l’application reprend et fonctionne en mode lecture-écriture en s’appuyant sur la base de données primaire de la région A.

En cas de panne dans la région B, Traffic Manager détecte la défaillance du point de terminaison de l’application dans la région B et le groupe de basculement bascule l’écouteur de lecture seule vers la région A. Cette panne n’affecte pas l’expérience de l’utilisateur final, mais la base de données primaire est exposée pendant toute sa durée. C’est ce qu’indique le diagramme suivant.

![Panne : base de données secondaire. Récupération d’urgence cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-3.png)

Une fois la panne résolue, la base de données secondaire est immédiatement synchronisée avec la base de données primaire l’écouteur de lecture seule est rebasculé vers la base de données secondaire de la région B. Lors de la synchronisation, les performances de la base de données primaire peuvent être légèrement affectées selon la quantité de données à synchroniser.

Ce modèle de conception offre plusieurs **avantages**:

* Il évite la perte de données pendant les pannes temporaires.
* Le temps d’arrêt dépend uniquement de la vitesse selon laquelle Traffic Manager détecte le problème de connectivité, paramètre configurable.

Le **compromis** est le suivant :

* L’application doit être en mesure de fonctionner en mode lecture seule.

> [!NOTE]
> Dans le cas d’une panne de service permanente dans la région, vous devez activer manuellement le basculement de la base de données et accepter la perte de données. L’application sera fonctionnelle dans la région secondaire avec un accès en lecture-écriture à la base de données.
>

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planification de la continuité des activités : choisir une conception d’application pour la récupération d’urgence cloud
Votre stratégie de récupération d’urgence cloud spécifique peut combiner ou étendre ces modèles de conception afin de mieux répondre aux besoins de votre application.  Comme mentionné précédemment, la stratégie que vous choisissez est basée sur le contrat de niveau de service que vous souhaitez proposer à vos clients et sur la topologie de déploiement d’applications. Pour vous aider à prendre votre décision, le tableau suivant compare les choix en fonction de la perte de données estimée ou de l’objectif de point de récupération (RPO) et du temps de récupération estimé (ERT).

| Modèle | RPO | ERT |
|:--- |:--- |:--- |
| Déploiement actif / passif pour la récupération d’urgence avec accès à la base de données colocalisée |Accès en lecture-écriture < 5 s |Temps de détection des défaillances + TTL du DNS |
| Déploiement actif / actif pour l’équilibrage de charge d’applications |Accès en lecture-écriture < 5 s |Temps de détection des défaillances + TTL du DNS |
| Déploiement actif / passif pour la conservation des données |Accès en lecture seule < 5 s | Accès en lecture seule = 0 |
||Accès en lecture-écriture = 0 | Accès en lecture-écriture = temps de détection de défaillances + période de grâce en cas de risque de perte de données |
|||

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les sauvegardes automatisées d’une base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)
* Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md)
* Pour découvrir des options de récupération plus rapides, voir [Géoréplication active](sql-database-geo-replication-overview.md).  
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, consultez [Copie de base de données](sql-database-copy.md)
* Pour plus d’informations sur la géoréplication active avec des pools élastiques, voir [Stratégies de récupération d’urgence de pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

