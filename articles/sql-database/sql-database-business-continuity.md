<properties
   pageTitle="Continuité des activités cloud - récupération de base de données - SQL Database | Microsoft Azure"
   description="Découvrez comment Azure SQL Database prend en charge la continuité des activités cloud et la récupération de base de données et vous aide à maintenir les applications cloud opérationnelles."
   keywords="continuité des activités, continuité des activités cloud, récupération d’urgence de base de données, récupération de base de données"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-bcdr"
   ms.date="06/09/2016"
   ms.author="carlrab"/>

# Continuité de l’activité avec Azure SQL Database

Azure SQL Database offre un certain nombre de solutions de continuité des activités. La continuité des activités concerne la conception, le déploiement et l’exécution d’une application de façon résiliente à des événements d’interruption planifiés ou non planifiés qui entraînent une perte permanente ou temporaire de la capacité de l’application à exécuter sa fonction commerciale. Les événements non planifiés vont des erreurs humaines aux pannes permanentes ou temporaires, en passant par les sinistres régionaux qui peuvent entraîner la perte à grande échelle de fonctionnalité dans une région Azure spécifique. Les événements planifiés incluent le redéploiement d’application dans une autre région et les mises à niveau d’application. L'objectif de la continuité des activités est que votre application continuer de fonctionner pendant ces événements avec un impact minime sur la fonction commerciale.

Pour discuter des solutions de continuité des activités cloud de SQL Database, vous devez vous familiariser avec plusieurs concepts. Ces composants sont les suivants :

* La **récupération d'urgence (DR) :** processus consistant à restaurer la fonction commerciale normale de l'application

* Le **temps de récupération estimé (ERT) :** durée estimée nécessaire pour que la base de données soit entièrement disponible après une requête de restauration ou de basculement.

* L’**objectif de délai de récupération (RTO) :** délai maximal acceptable avant la récupération complète de l’application après l’événement d’interruption. RTO mesure la perte maximale de disponibilité pendant les échecs.

* L’**objectif de point de récupération (RPO) :** quantité maximale des dernières mises à jour (intervalle de temps) que l’application peut perdre jusqu’à sa récupération complète après l’événement d’interruption. RPO mesure la perte maximale de données pendant les échecs.


## Scénarios de continuité des activités cloud de SQL Database

Les principaux scénarios à prendre en compte pendant la planification de la continuité des activités et de la récupération de base de données sont les suivants :

### Concevoir les applications pour la continuité des activités

L'application que je crée est essentielle pour mon entreprise. Je souhaite la concevoir et la configurer afin qu'elle puisse supporter un sinistre régional de défaillance catastrophique du service. Je connais les exigences RPO et RTO pour mon application et je choisirai la configuration qui répond à ces exigences.

### Récupération après une erreur humaine

Je dispose des droits d'administration pour accéder à la version de production de l'application. Dans le cadre du processus de maintenance régulière, j'ai commis une erreur et supprimé certaines données critiques en production. Je souhaite restaurer rapidement les données afin d'atténuer l'impact de l'erreur.

### Récupération après une panne

J'exécute mon application en production et je reçois une alerte suggérant qu'il y a une panne dans la région où mon application est déployée. Je souhaite lancer le processus de récupération pour la transférer dans une autre région afin d'atténuer l'impact sur l'entreprise.

### Simulation de récupération d'urgence

Étant donné que la récupération en cas de panne déplacera la couche de données de l'application vers une autre région, je souhaite tester régulièrement le processus de récupération et évaluer son impact sur l'application afin d'être toujours prêt.

### Mise à niveau de l'application sans interruption de service

Je publie une mise à niveau majeure de mon application. Elle implique des modifications du schéma de la base de données, le déploiement de procédures stockées supplémentaires, etc. Ce processus nécessite l'arrêt de l'accès utilisateur à la base de données. Parallèlement, je veux m'assurer que la mise à niveau ne provoque pas une interruption des opérations importante.

## Fonctionnalités de continuité des activités d’une base de données SQL Azure

Le tableau suivant montre les fonctionnalités de continuité des activités et leurs différences à travers les [niveaux de service](sql-database-service-tiers.md) :

| Fonctionnalité | Niveau de base | Niveau standard |Niveau Premium
| --- |--- | --- | ---
| Limite de restauration dans le temps | Tout point de restauration dans un délai de 7 jours | Tout point de restauration dans un délai de 35 jours | Tout point de restauration dans un délai de 35 jours
| Restauration géographique | ERT < 12 h, RPO < 1 h | ERT < 12 h, RPO < 1 h | ERT < 12 h, RPO < 1 h
| Géo-réplication active | ERT < 30s, RPO < 5s | ERT < 30s, RPO < 5s | ERT < 30s, RPO < 5s

Ces fonctionnalités sont fournies pour résoudre les scénarios répertoriés ci-dessus.

> [AZURE.NOTE] Les valeurs ERT et RPO sont des objectifs d’ingénierie et sont indiquées à titre de conseil uniquement. Elles ne font pas partie du [contrat SLA pour Base de données SQL](https://azure.microsoft.com/support/legal/sla/sql-database/v1_0/)


###Restauration dans le temps

La [limite de restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore) est conçue pour restaurer votre base de données à un point antérieur dans le temps. Elle utilise les sauvegardes de base de données, les sauvegardes incrémentielles et les sauvegardes de journaux de transactions que le service gère automatiquement pour chaque base de données utilisateur. Cette fonctionnalité est disponible pour tous les niveaux de service. Vous pouvez revenir en arrière 7 jours avec le niveau de base, 35 jours avec le niveau standard et 35 jours avec le niveau Premium.

### Restauration géographique

La [géo-restauration](sql-database-recovery-using-backups.md#geo-restore) est également disponible avec les bases de données De base, Standard et Premium. Elle fournit l'option de récupération par défaut lorsque la base de données est également indisponible en raison d'un incident dans la région où la base de données est hébergée. Similaire à la Limite de restauration dans le temps, la géo-restauration s'appuie sur les sauvegardes de base de données dans le stockage Azure redondant au niveau géographique. Elle restaure à partir de la copie de sauvegarde répliquées au niveau géographique. Donc, elle est résistante aux pannes de stockage dans la région principale.

### Géo-réplication active

La [géo-réplication active](sql-database-geo-replication-overview.md) est disponible avec tous les niveaux de bases de données. Elle est conçue pour les applications qui ont des exigences de récupération plus agressives que celles proposées par la géo-restauration. À l'aide de la géo-réplication active, vous pouvez créer jusqu'à quatre répliques secondaires sur des serveurs dans différentes régions. Vous pouvez lancer le basculement sur n’importe quelle base de données secondaire. En outre, la géo-réplication active peut être utilisée pour prendre en charge les [scénarios de mise à niveau ou de déplacement d'application](sql-database-manage-application-rolling-upgrade.md), ainsi que l'équilibrage de charge pour les charges de travail en lecture seule.

## Sélection des fonctionnalités de continuité des activités

La conception de votre application pour la continuité des activités nécessite que vous répondiez aux questions suivantes :

1. Quelle fonctionnalité de continuité des activités est appropriée pour protéger mon application contre les pannes ?
2. Quel niveau de topologie de réplication et de redondance utiliser ?

### Utilisation de la géo-restauration

La [géo-restauration](sql-database-recovery-using-backups.md#geo-restore) constitue l’option de récupération par défaut lorsque la base de données est indisponible en raison d’un incident dans la région où elle est hébergée. La base de données SQL fournit une protection intégrée de base de chaque base de données par défaut. Cela se fait en exécutant et en stockant des [sauvegardes de bases de données](sql-database-automated-backups.md) dans le stockage Azure redondant au niveau géographique (GRS). Si vous choisissez cette méthode, aucune configuration particulière ni allocation de ressources supplémentaire n'est nécessaire. Vous pouvez récupérer votre base de données vers n’importe quelle région en effectuant une restauration à partir de ces sauvegardes automatisées géo-redondantes vers une nouvelle base de données.

Nous vous recommandons d'utiliser la protection intégrée si votre application répond aux critères suivants :

1. Elle n'est pas essentielle. Elle n'a pas de contrat SLA contraignant. Donc, l'interruption de service de 24 heures ou plus n'entraîne pas la responsabilité financière.
2. Le taux de modification des données est faible (par exemple, en transactions par heure). Le RPO de 1 heure n'entraîne pas une perte de données massive.
3. L'application est sensible aux coûts et ne peut pas justifier le coût supplémentaire de la géo-réplication

> [AZURE.NOTE] La géo-restauration n'alloue pas au préalable la capacité de calcul dans une région particulière pour restaurer les bases de données actives à partir de la sauvegarde pendant la panne. Le service gère la charge de travail associée aux requêtes de géo-restauration d'une manière qui réduit l'impact sur les bases de données existantes dans cette région et leurs exigences en termes de capacité auront la priorité. Par conséquent, le temps de récupération de votre base de données dépendra du nombre de bases de données en phase de récupération dans la même région en même temps, ainsi que de la taille de la base de données, du nombre de journaux de transactions, de la bande passante réseau, etc.

### Utilisation de la géo-réplication active

La [géo-réplication active](sql-database-geo-replication-overview.md) permet la création et la maintenance de bases de données accessibles en lecture (secondaires) dans une autre région à partir de votre base principale, en les maintenant à jour à l’aide d’un mécanisme de réplication asynchrone. Elle garantit que votre base de données aura les données et ressources de calcul nécessaires pour supporter la charge de travail de l'application après la restauration.

Nous vous recommandons d’utiliser la géo-réplication active si votre application répond aux critères suivants :

1. Elle est essentielle. La perte de données et de disponibilité entraînent une responsabilité financière.
2. Le taux de modification des données est élevé (par exemple, en transactions par minute ou seconde). Le RPO de 1 heure associé à la protection par défaut entraînera sans doute une perte de données inacceptable.
3. Le coût associé à l'utilisation de la géo-réplication est nettement plus faible que la responsabilité financière potentielle et la perte d'activité associée.

## Concevez des solutions cloud pour la récupération d’urgence. 

Lorsque vous concevez votre application pour la continuité des activités, vous devez envisager plusieurs options de configuration. Le choix dépendra de la topologie de déploiement de l'application et des parties de vos applications les plus vulnérables à une défaillance. Consultez [Conception de solutions Cloud pour la récupération d'urgence à l'aide de la géo-réplication](sql-database-designing-cloud-solutions-for-disaster-recovery.md) pour obtenir des instructions.

Pour plus d’informations sur les stratégies de récupération lors de l’utilisation d’un pool élastique, consultez [Stratégies de récupération d’urgence pour les applications utilisant le pool élastique de bases de données SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

## Étapes suivantes

- Pour en savoir plus sur les sauvegardes automatisées d’une base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)
- Pour en savoir plus sur la conception de la continuité des activités et les scénarios de récupération, consultez [Scénarios de continuité des activités](sql-database-business-continuity-scenarios.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md)
- Pour en savoir plus sur les options de récupération plus rapides, consultez [Géo-réplication active](sql-database-geo-replication-overview.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, consultez [Copie de base de données](sql-database-copy.md)

<!---HONumber=AcomDC_0720_2016-->