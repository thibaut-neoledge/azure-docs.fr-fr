<properties
   pageTitle="Solutions de récupération d’urgence cloud - Géo-réplication active des bases de données SQL | Microsoft Azure"
   description="Apprenez à concevoir des solutions de récupération d’urgence cloud pour la planification de la continuité des activités à l’aide de la géo-réplication pour la sauvegarde de données d’application avec Azure SQL Database."
   keywords="récupération d’urgence cloud, solutions de récupération d’urgence, sauvegarde de données d’application, géo-réplication, planification de la continuité des activités"
   services="sql-database"
   documentationCenter=""
   authors="anosov1960"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="07/20/2016"
   ms.author="sashan"/>

# Concevoir une application pour la récupération d’urgence cloud à l’aide de la géo-réplication active dans une base de données SQL


> [AZURE.NOTE] [Active Geo-Replication](sql-database-geo-replication-overview.md) est désormais disponible pour toutes les bases de données de tous les niveaux.



Découvrez comment utiliser la [Géoréplication active](sql-database-geo-replication-overview.md) dans une base de données SQL pour concevoir des applications de base de données résistant aux défaillances régionales et aux pannes graves. Pour la planification de la continuité des activités, prenez en compte la topologie de déploiement d’applications, le contrat de niveau de service que vous ciblez, la latence du trafic et les coûts. Dans cet article, nous allons examiner les modèles d’application courants et présenter les avantages et les inconvénients de chaque option. Pour plus d’informations sur la géo-réplication active avec des pools élastiques, consultez [Stratégies de récupération d’urgence de pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

## Modèle de conception 1 : Déploiement actif/passif pour la récupération d’urgence cloud avec une base de données colocalisée

Cette option est idéale pour les applications dotées des caractéristiques suivantes :

+ Instance active dans une seule région Azure
+ Forte dépendance à l’accès aux données en lecture-écriture (RW)
+ Connectivité dans plusieurs régions entre la logique d’application et la base de données non acceptable en raison de la latence et du coût du trafic

Dans ce cas, la topologie de déploiement d’applications est optimisée pour la gestion des sinistres régionaux lorsque tous les composants d’application sont concernés et doivent être basculés en tant qu’unité. Pour la redondance géographique, la logique d’application et la base de données sont répliquées vers une autre région, mais elles ne sont pas utilisées pour la charge de travail de l’application dans des conditions normales. L’application dans la région secondaire doit être configurée pour utiliser une chaîne de connexion SQL sur la base de données secondaire. Traffic Manager est configuré pour utiliser la [méthode de routage du trafic par basculement](../traffic-manager/traffic-manager-configure-failover-routing-method.md).

> [AZURE.NOTE] [Azure traffic manager](../traffic-manager/traffic-manager-overview.md) est utilisé dans cet article aux fins d’illustration uniquement. Vous pouvez utiliser toute solution d’équilibrage de charge qui prend en charge la méthode de routage du trafic par basculement.

Outre les instances de l’application principale, vous devez envisager de déployer une petite [application de rôle de travail](cloud-services-choose-me.md#tellmecs) qui surveille votre base de données primaire en émettant des commandes en lecture seule (RO) T-SQL périodiques. Vous pouvez l’utiliser pour déclencher automatiquement le basculement, pour générer une alerte sur la console d’administration de votre application, ou pour effectuer les deux opérations. Pour vous assurer que la surveillance n’est pas affectée par les pannes régionales, vous devez déployer les instances d’application de surveillance sur chaque région et connecter chaque instance à la base de données primaire dans la région primaire et à la base de données secondaire dans la région locale.

> [AZURE.NOTE] Les deux applications d'analyse doivent être actives et examiner les bases de données primaires et secondaires. Les bases de données primaire et secondaire peuvent servir à détecter une défaillance dans la région secondaire et à alerter lorsque l’application n’est pas protégée.

Le diagramme suivant illustre cette configuration avant une panne.

![Configuration de la géoréplication d’une base de données SQL. Récupération d’urgence cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-1.png)

Après une panne dans la région primaire, l’application de surveillance détecte que la base de données primaire n’est pas accessible et enregistre une alerte. En fonction du contrat de niveau de service de votre application, vous pouvez décider du nombre d’échecs consécutifs des sondages de surveillance avant qu’une base de données ne soit déclarée en panne. Pour obtenir un basculement coordonné du point de terminaison de l’application et de la base de données, l’application de surveillance doit effectuer les étapes suivantes :

1. [Mettre à jour le statut du point de terminaison primaire](https://msdn.microsoft.com/library/hh758250.aspx) pour déclencher le basculement du point de terminaison.
2. Appeler la base de données secondaire pour [lancer le basculement de la base de données](sql-database-geo-replication-portal.md).

Après le basculement, l’application traite les demandes des utilisateurs dans la région secondaire mais reste colocalisée avec la base de données, car la base de données primaire se trouve désormais dans la région secondaire. Ce scénario est illustré par le diagramme suivant. Dans tous les diagrammes, des lignes pleines indiquent les connexions actives, des lignes en pointillé indiquent les connexions suspendues et les symboles « stop » indiquent les déclencheurs d’action.


![Géoréplication : basculement vers la base de données secondaire. Sauvegarde de données d’application.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-2.png)

Si une panne se produit dans la région secondaire, le lien de réplication entre la base de données primaire et secondaire est interrompu et l’application de surveillance enregistre une alerte indiquant que la base de données primaire est exposée. Cela n’a aucune incidence sur les performances de l’application, mais celle-ci est exposée et court donc un risque plus élevé au cas où les deux régions échoueraient successivement.

> [AZURE.NOTE] Nous recommandons les configurations de déploiement uniquement avec une seule région de récupération d’urgence. En effet, la plupart des zones géographiques Azure comptent deux régions. Ces configurations ne protègent pas votre application d’une défaillance irrémédiable des deux régions. Dans le cas peu probable d’une telle défaillance, vous pouvez restaurer vos bases de données dans une région tierce à l’aide de [l’opération de géo-restauration](sql-database-disaster-recovery.md#recovery-using-geo-restore).

Une fois la panne atténuée, la base de données secondaire est automatiquement synchronisée avec la primaire. Lors de la synchronisation, les performances de la base de données primaire peuvent être légèrement affectées selon la quantité de données à synchroniser. Le diagramme suivant illustre une panne dans la région secondaire.

![Synchronisation de la base de données secondaire avec la base de données primaire. Récupération d’urgence cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-3.png)


Les **avantages** clés de ce modèle de conception sont les suivants :

+ La chaîne de connexion SQL est définie pendant le déploiement de l’application dans chaque région et ne change pas après le basculement.
+ Les performances de l’application ne sont pas affectées par le basculement, car l’application et la base de données sont toujours colocalisées.

Le principal **inconvénient** est que l’instance d’application redondante dans la région secondaire est uniquement utilisée pour la récupération d’urgence.

## Modèle de conception 2 : Déploiement actif / actif pour l’équilibrage de charge d’applications
Cette option de récupération d’urgence cloud est idéale pour les applications dotées des caractéristiques suivantes :

+ Ratio élevé de lectures de base de données par rapport aux écritures.
+ La latence d’écriture de la base de données n’affecte pas l’expérience de l’utilisateur final.
+ La logique de lecture seule peut être séparée de la logique de lecture-écriture à l’aide d’une chaîne de connexion différente.
+ La logique de lecture seule ne dépend pas de la synchronisation intégrale des données avec les dernières mises à jour.

Si vos applications possèdent ces caractéristiques, l’équilibrage de charge des connexions de l’utilisateur final entre plusieurs instances d’application dans différentes régions peut améliorer les performances et l’expérience de l’utilisateur final. Pour implémenter l’équilibrage de charge, chaque région doit disposer d’une instance active de l’application avec la logique de lecture-écriture (RW) connectée à la base de données primaire dans la région primaire. La logique de lecture seule (RO) doit être connectée à une base de données secondaire dans la même région que l’instance d’application. Traffic Manager doit être configuré pour utiliser [le routage de tourniquet (round robin)](../traffic-manager/traffic-manager-configure-round-robin-routing-method.md) ou le [routage de performances](../traffic-manager/traffic-manager-configure-performance-routing-method.md) avec la [surveillance du point de terminaison](../traffic-manager/traffic-manager-monitoring.md) activée pour chaque instance d’application.

Comme dans le modèle 1, vous devez envisager le déploiement d’une application de surveillance similaire. Mais, contrairement au modèle 1, l’application de surveillance ne sera pas chargée de déclencher le basculement du point de terminaison.

> [AZURE.NOTE] Bien que ce modèle utilise plus d’une base de données secondaire, seule l’une d’entre elles sera utilisée pour le basculement pour les motifs mentionnés précédemment. Étant donné que ce modèle nécessite un accès en lecture seule à la région secondaire, il nécessite la géo-réplication active.

Traffic Manager doit être configuré pour que le routage de performances dirige les connexions utilisateur vers l’instance d’application la plus proche de l’emplacement géographique de l’utilisateur. Le diagramme suivant illustre cette configuration avant une panne. ![Aucune panne : routage des performances vers l’application la plus proche. Géoréplication.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-1.png)

Si une panne de la base de données est détectée dans la région primaire, vous initiez le basculement de la base de données primaire vers l’une des régions secondaires, ce qui modifie l’emplacement de la base de données primaire. Traffic Manager exclut automatiquement le point de terminaison hors connexion de la table de routage, mais continue le routage du trafic de l’utilisateur final vers les instances en ligne restantes. Étant donné que la base de données primaire est maintenant dans une autre région, toutes les instances en ligne doivent modifier leur chaîne de connexion SQL de lecture-écriture pour se connecter à la nouvelle région primaire. Il est important que vous effectuiez cette modification avant d’initier le basculement de la base de données. Les chaînes de connexion SQL en lecture seule doivent rester inchangées car elles pointent toujours vers la base de données dans la même région. Les étapes de basculement sont les suivantes :

1. Modifier les chaînes de connexion SQL en lecture-écriture pour pointer vers la nouvelle région primaire.
2. Appeler la base de données secondaire désignée pour [lancer le basculement de la base de données](sql-database-geo-replication-portal.md).

Le diagramme suivant illustre la nouvelle configuration après le basculement. ![Configuration après le basculement. Récupération d’urgence cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-2.png)

En cas de panne dans l’une des régions secondaires, Traffic Manager supprime automatiquement le point de terminaison hors connexion dans cette région de la table de routage. Le canal de réplication vers la base de données secondaire dans cette région est interrompu. Étant donné que les régions restantes récupèrent un trafic utilisateur supplémentaire dans ce scénario, les performances de l’application sont affectées pendant la panne. Une fois la panne atténuée, la base de données secondaire dans la région impactée est immédiatement synchronisée avec la primaire. Lors de la synchronisation, les performances de la base de données primaire peuvent être légèrement affectées selon la quantité de données à synchroniser. Le diagramme suivant illustre une panne dans l’une des régions secondaires.

![Panne dans une région secondaire. Récupération d’urgence cloud - géoréplication.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-3.png)

**L’avantage** clé de ce modèle de conception est que vous pouvez mettre à l’échelle la charge de travail de l’application sur plusieurs régions secondaires pour assurer des performances optimales à l’utilisateur final. Les **inconvénients** de cette option sont les suivants :

+ Les connexions de lecture-écriture entre les instances de l’application et la base de données présentent des latences et des coûts variables.
+ Les performances de l’application sont affectées pendant une panne.
+ Les instances de l’application sont nécessaires pour modifier dynamiquement la chaîne de connexion SQL après le basculement de la base de données.

> [AZURE.NOTE] Une approche similaire peut être utilisée pour décharger les charges de travail spécialisées telles que les travaux de création de rapports, les outils d’analyse décisionnelle ou les sauvegardes. En général, ces charges de travail consomment des ressources importantes de la base de données. Il est donc recommandé de désigner l’une des bases de données secondaires pour celles-ci, en mettant en correspondance le niveau de performances avec la charge de travail attendue.

## Modèle de conception 3 : Déploiement actif / passif pour la conservation des données  
Cette option est idéale pour les applications dotées des caractéristiques suivantes :

+ Toute perte de données constitue un risque élevé pour l’entreprise. Le basculement de la base de données ne peut être utilisé qu’en dernier recours si la panne est permanente.
+ L’application peut fonctionner en mode lecture seule pour une période donnée.

Dans ce modèle, l’application bascule en mode lecture seule quand elle est connectée à la base de données secondaire. La logique d’application de la région primaire coexiste avec la base de données primaire et fonctionne en mode lecture-écriture (RW). La logique d’application de la région secondaire coexiste avec la base de données secondaire et est prête à fonctionner en mode lecture seule (RO). Traffic Manager doit être configuré pour utiliser [le routage de basculement](../traffic-manager/traffic-manager-configure-failover-routing-method.md) avec la [surveillance du point de terminaison](../traffic-manager/traffic-manager-monitoring.md) activée pour les deux instances d’application.

Le diagramme suivant illustre cette configuration avant une panne. ![Déploiement actif/passif avant le basculement. Récupération d’urgence cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-1.png)

Lorsque Traffic Manager détecte un problème de connectivité dans la région primaire, il bascule automatiquement le trafic utilisateur vers l’instance d’application dans la région secondaire. Avec ce modèle, il est important que vous n’initiiez **pas** le basculement de la base de données après la détection de la panne. L’application dans la région secondaire est activée et fonctionne en mode lecture seule à l’aide de la base de données secondaire. C’est ce qu’indique le diagramme suivant.

![Panne : l’application en mode lecture seule. Récupération d’urgence cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-2.png)

Une fois la panne dans la région primaire atténuée, Traffic Manager détecte la restauration de la connectivité dans la région primaire et rebascule le trafic utilisateur vers l’instance d’application dans la région primaire. Cette instance d’application reprend et fonctionne en mode lecture-écriture à l’aide de la base de données primaire.

> [AZURE.NOTE] Étant donné que ce modèle nécessite un accès en lecture seule à la région secondaire, il nécessite la géoréplication active.

En cas de panne dans la région secondaire, Traffic Manager marque le point de terminaison de l’application dans la région primaire comme étant dégradé, et le canal de réplication est suspendu. Cependant, les performances de l’application ne sont pas affectées pendant cette panne. Une fois la panne atténuée, la base de données secondaire est immédiatement synchronisée avec la primaire. Lors de la synchronisation, les performances de la base de données primaire peuvent être légèrement affectées selon la quantité de données à synchroniser.

![Panne : base de données secondaire. Récupération d’urgence cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-3.png)

Ce modèle de conception offre plusieurs **avantages** :

+ Il évite la perte de données pendant les pannes temporaires.
+ Il ne nécessite pas le déploiement d’une application de surveillance étant donné que la récupération est déclenchée par Traffic Manager.
+ Le temps d’arrêt dépend uniquement de la vitesse selon laquelle Traffic Manager détecte le problème de connectivité, paramètre configurable.

Les **inconvénients** sont les suivants :

+ L’application doit être en mesure de fonctionner en mode lecture seule.
+ Il est nécessaire de basculer dynamiquement vers l’application quand elle est connectée à une base de données en lecture seule.
+ La reprise des opérations de lecture-écriture implique la récupération de la région primaire, ce qui signifie que l’accès complet aux données peut être désactivé pour quelques heures, voire quelques jours.

> [AZURE.NOTE] Dans le cas d’une panne de service permanente dans la région, vous devez activer manuellement le basculement de la base de données et accepter la perte de données. L’application sera fonctionnelle dans la région secondaire avec un accès en lecture-écriture à la base de données.

## Planification de la continuité des activités : choisir une conception d’application pour la récupération d’urgence cloud

Votre stratégie de récupération d’urgence cloud spécifique peut combiner ou étendre ces modèles de conception afin de mieux répondre aux besoins de votre application. Comme mentionné précédemment, la stratégie que vous choisissez est basée sur le contrat de niveau de service que vous souhaitez proposer à vos clients et sur la topologie de déploiement d’applications. Pour vous aider à prendre votre décision, le tableau suivant compare les choix en fonction de la perte de données estimée ou de l’objectif de point de récupération (RPO) et du temps de récupération estimé (ERT).

| Modèle | RPO | ERT
| :--- |:--- | :---
| Déploiement actif / passif pour la récupération d’urgence avec accès à la base de données colocalisée | Accès en lecture-écriture < 5 s | Temps de détection de défaillance + appel des API de basculement + test de vérification des applications
| Déploiement actif / actif pour l’équilibrage de charge d’applications | Accès en lecture-écriture < 5 s | Temps de détection de défaillance + appel des API de basculement + modification des chaînes de connexion SQL + test de vérification des applications
| Déploiement actif / passif pour la conservation des données | Accès en lecture seule < 5 s Accès en lecture-écriture = zéro | Accès en lecture seule = temps de détection de défaillance de connectivité + test de vérification des applications <br>Accès en lecture-écriture = temps pour atténuer la panne

## Étapes suivantes

- Pour en savoir plus sur les sauvegardes automatisées d’une base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)
- Pour une vue d’ensemble de la continuité d’activité et des scénarios, consultez [Vue d’ensemble de la continuité d’activité](sql-database-business-continuity.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md)
- Pour en savoir plus sur les options de récupération plus rapides, consultez [Géo-réplication active](sql-database-geo-replication-overview.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, consultez [Copie de base de données](sql-database-copy.md)
- Pour plus d’informations sur la géo-réplication active avec des pools élastiques, consultez [Stratégies de récupération d’urgence de pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

<!---HONumber=AcomDC_0817_2016-->