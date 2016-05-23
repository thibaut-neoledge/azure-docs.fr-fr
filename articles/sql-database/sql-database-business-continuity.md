<properties
   pageTitle="Continuité des activités cloud - récupération de base de données | Microsoft Azure"
   description="Découvrez comment Azure SQL Database prend en charge la continuité des activités cloud et la récupération de base de données et vous aide à maintenir les applications cloud opérationnelles."
   keywords="continuité des activités, continuité des activités cloud, récupération d’urgence de base de données, récupération de base de données"
   services="sql-database"
   documentationCenter=""
   authors="elfisher"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="05/10/2016"
   ms.author="elfish"/>

# Vue d’ensemble : continuité des activités cloud et récupération d’urgence d’une base de données avec SQL Database

La continuité des activités concerne la conception, le déploiement et l’exécution d’une application de façon résiliente à des événements d’interruption planifiés ou non planifiés qui entraînent une perte permanente ou temporaire de la capacité de l’application à exécuter sa fonction commerciale. Les événements non planifiés vont des erreurs humaines aux pannes permanentes ou temporaires, en passant par les sinistres régionaux qui peuvent entraîner la perte à grande échelle de fonctionnalité dans une région Azure spécifique. Les événements planifiés incluent le redéploiement d’application dans une autre région et les mises à niveau d’application. L'objectif de la continuité des activités est que votre application continuer de fonctionner pendant ces événements avec un impact minime sur la fonction commerciale.

Pour discuter des solutions de continuité des activités cloud, vous devez vous familiariser avec plusieurs concepts :

* La **récupération d'urgence (DR) :** processus consistant à restaurer la fonction commerciale normale de l'application

* Le **temps de récupération estimé (ERT) :** durée estimée nécessaire pour que la base de données soit entièrement disponible après une requête de restauration ou de basculement.

* L’**objectif de délai de récupération (RTO) :** délai maximal acceptable avant la récupération complète de l’application après l’événement d’interruption. RTO mesure la perte maximale de disponibilité pendant les échecs.

* L’**objectif de point de récupération (RPO) :** quantité maximale des dernières mises à jour (intervalle de temps) que l’application peut perdre jusqu’à sa récupération complète après l’événement d’interruption. RPO mesure la perte maximale de données pendant les échecs.


## Scénarios de continuité des activités cloud

Voici les principaux scénarios à prendre en compte pendant la planification de la continuité des activités et de la récupération de base de données.

###Concevoir les applications pour la continuité des activités

L'application que je crée est essentielle pour mon entreprise. Je souhaite la concevoir et la configurer afin qu'elle puisse supporter un sinistre régional de défaillance catastrophique du service. Je connais les exigences RPO et RTO pour mon application et je choisirai la configuration qui répond à ces exigences.

###Récupération après une erreur humaine

Je dispose des droits d'administration pour accéder à la version de production de l'application. Dans le cadre du processus de maintenance régulière, j'ai commis une erreur et supprimé certaines données critiques en production. Je souhaite restaurer rapidement les données afin d'atténuer l'impact de l'erreur.

###Récupération après une panne

J'exécute mon application en production et je reçois une alerte suggérant qu'il y a une panne dans la région où mon application est déployée. Je souhaite lancer le processus de récupération pour la transférer dans une autre région afin d'atténuer l'impact sur l'entreprise.

###Simulation de récupération d'urgence

Étant donné que la récupération en cas de panne déplacera la couche de données de l'application vers une autre région, je souhaite tester régulièrement le processus de récupération et évaluer son impact sur l'application afin d'être toujours prêt.

###Mise à niveau de l'application sans interruption de service

Je publie une mise à niveau majeure de mon application. Elle implique des modifications du schéma de la base de données, le déploiement de procédures stockées supplémentaires, etc. Ce processus nécessite l'arrêt de l'accès utilisateur à la base de données. Parallèlement, je veux m'assurer que la mise à niveau ne provoque pas une interruption des opérations importante.

##Fonctionnalités de continuité des activités

Le tableau suivant compare les fonctionnalités de continuité des activités cloud en fonction des niveaux de service :

| Fonctionnalité | Niveau de base | Niveau standard |Niveau Premium
| --- |--- | --- | ---
| Limite de restauration dans le temps | Tout point de restauration dans un délai de 7 jours | Tout point de restauration dans un délai de 14 jours | Tout point de restauration dans un délai de 35 jours
| Restauration géographique | ERT < 12 h, RPO < 1 h | ERT < 12 h, RPO < 1 h | ERT < 12 h, RPO < 1 h
| Géo-réplication active | ERT < 30s, RPO < 5s | ERT < 30s, RPO < 5s | ERT < 30s, RPO < 5s

Ces fonctionnalités sont fournies pour résoudre les scénarios répertoriés ci-dessus. Reportez-vous à la section [Conception pour la continuité des activités](sql-database-business-continuity-design.md) pour savoir comment sélectionner la fonctionnalité spécifique.

> [AZURE.NOTE] Les valeurs ERT et RPO sont des objectifs d’ingénierie et sont indiquées à titre de conseil uniquement. Elles ne font pas partie du [contrat SLA pour Base de données SQL](https://azure.microsoft.com/support/legal/sla/sql-database/v1_0/)


###Limite de restauration dans le temps

La [limite de restauration dans le temps](sql-database-point-in-time-restore.md) est conçue pour restaurer votre base de données à un point antérieur dans le temps. Elle utilise les sauvegardes de base de données, les sauvegardes incrémentielles et les sauvegardes de journaux de transactions que le service gère automatiquement pour chaque base de données utilisateur. Cette fonctionnalité est disponible pour tous les niveaux de service. Vous pouvez revenir en arrière 7 jours avec le niveau de base, 14 jours avec le niveau standard et 35 jours avec le niveau Premium. Consultez [Récupération après une erreur humaine](sql-database-user-error-recovery.md) pour savoir comment utiliser la limite de restauration dans le temps.

###Restauration géographique

La [géo-restauration](sql-database-geo-restore.md) est également disponible avec les bases de données De base, Standard et Premium. Elle fournit l'option de récupération par défaut lorsque la base de données est également indisponible en raison d'un incident dans la région où la base de données est hébergée. Similaire à la Limite de restauration dans le temps, la géo-restauration s'appuie sur les sauvegardes de base de données dans le stockage Azure redondant au niveau géographique. Elle restaure à partir de la copie de sauvegarde répliquées au niveau géographique. Donc, elle est résistante aux pannes de stockage dans la région principale. Consultez [Récupération après une panne](sql-database-disaster-recovery.md) pour obtenir des détails sur l'utilisation de la géo-restauration.

###Géo-réplication active

La [géo-réplication active](sql-database-geo-replication-overview.md) est disponible avec tous les niveaux de bases de données. Elle est conçue pour les applications qui ont des exigences de récupération plus agressives que celles proposées par la géo-restauration. À l'aide de la géo-réplication active, vous pouvez créer jusqu'à quatre répliques secondaires sur des serveurs dans différentes régions. Vous pouvez lancer le basculement sur n’importe quelle base de données secondaire. En outre, la géo-réplication active peut être utilisée pour prendre en charge les scénarios de mise à niveau ou de déplacement d'application, ainsi que l'équilibrage de charge pour les charges de travail en lecture seule. Pour plus d’informations sur la [configuration de la géo-réplication](sql-database-business-continuity-design.md) et le [basculement vers la base de données secondaire](sql-database-geo-replication-portal.md), consultez l’article [Conception pour la continuité des activités](sql-database-geo-replication-failover-portal.md). Consultez [Mise à niveau de l'application sans interruption de service](sql-database-business-continuity-application-upgrade.md) pour plus d'informations sur la mise en œuvre de la mise à niveau de l'application sans interruption.

<!---HONumber=AcomDC_0511_2016-->