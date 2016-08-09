<properties
   pageTitle="Continuité des activités cloud - récupération de base de données - SQL Database | Microsoft Azure"
   description="Découvrez comment Azure SQL Database prend en charge la continuité des activités cloud et la récupération de base de données et vous aide à maintenir les applications cloud opérationnelles."
   keywords="continuité des activités, continuité des activités cloud, récupération d’urgence de base de données, récupération de base de données"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/20/2016"
   ms.author="carlrab"/>

# Vue d’ensemble de la continuité de l’activité avec la base de données Azure SQL

Cette vue d’ensemble décrit les fonctionnalités de la base de données SQL Azure en matière de continuité d’activité et de récupération d’urgence. Elle fournit des options, des recommandations et des didacticiels pour la récupération à partir d’événements d’interruption qui pourraient entraîner une perte de données ou l’indisponibilité de votre base de données et de votre application. La discussion indique la procédure à suivre lorsqu’un utilisateur ou qu’une erreur d’application affecte l’intégrité des données, lorsqu’une région Azure subit une panne ou que votre application nécessite une maintenance.

## Fonctionnalités de la base de données SQL que vous pouvez utiliser pour garantir une continuité d’activité

La base de données SQL fournit plusieurs fonctionnalités de continuité d’activité, notamment des sauvegardes automatisées et une réplication facultative de la base de données. Chacune de ces fonctionnalités possède des caractéristiques spécifiques concernant le temps de récupération estimé (ERT) et le risque de perte de données pour les transactions récentes. Une fois que vous avez compris ces options, vous pouvez choisir celles qui vous conviennent et, dans la plupart des scénarios, les utiliser ensemble dans différents scénarios. Lorsque vous élaborez votre plan de continuité d’activité, vous devez comprendre le délai maximal acceptable nécessaire à la récupération complète de l’application après l’événement d’interruption : votre objectif de délai de récupération (RTO). Vous devez également comprendre la quantité maximale des récentes mises à jour de données (intervalle) que l’application peut accepter de perdre lors de la récupération après l’événement d’interruption : l’objectif de point de récupération (RPO).

### Utiliser des sauvegardes de base de données pour récupérer une base de données

La base de données SQL effectue automatiquement une combinaison de sauvegardes de bases de données complètes (toutes les semaines), de sauvegardes de bases de données différentielles (toutes les heures), et de sauvegardes de journaux de transactions (toutes les 5 minutes) pour protéger votre entreprise contre la perte de données. Ces sauvegardes sont stockées dans le stockage localement redondant pendant 35 jours pour les bases de données avec les niveaux de service Standard et Premium, et pendant 7 jours pour les bases de données avec le niveau de service De base - voir les [niveaux de service](sql-database-service-tiers.md) pour plus d’informations. Si la période de rétention de votre niveau de service ne répond pas aux besoins de votre entreprise, vous pouvez augmenter la période de rétention en [modifiant le niveau de service](sql-database-scale-up.md). Les sauvegardes complètes et différentielles de bases de données sont également répliquées vers un [Centre de données jumelé](../best-practices-availability-paired-regions.md) pour une protection contre une panne du centre de données - voir [sauvegardes de bases de données automatiques](sql-database-automated-backups.md) pour plus de détails.

Vous pouvez utiliser ces sauvegardes automatiques pour récupérer une base de données après divers événements d’interruption, à la fois dans votre propre centre de données et dans un autre centre de données. Lorsque vous utilisez des sauvegardes automatiques, le délai estimé de récupération dépend de plusieurs facteurs, notamment du nombre total de bases de données à récupérer dans la même région au même moment, de la taille de la base de données, de la taille du journal des transactions et de la bande passante réseau. Dans la plupart des cas, le délai de récupération est inférieur à 12 heures. Lorsque vous effectuez une récupération vers une autre région de données, le risque de perte de données est limité à 1 heure par le stockage géo-redondant des sauvegardes de bases de données différentielle (toutes les heures).

> [AZURE.IMPORTANT] Pour effectuer une récupération à l’aide de sauvegardes automatisées, vous devez avoir le rôle de collaborateur SQL Server ou être le propriétaire de l’abonnement - voir [RBAC : rôles intégrés](../active-directory/role-based-access-built-in-roles.md). Vous pouvez effectuer une récupération en utilisant le portail Azure, PowerShell ou l’API REST. Vous ne pouvez pas utiliser Transact-SQL.

Utilisez des sauvegardes automatisées comme mécanisme de continuité d’activité et de récupération si votre application :

- N'est pas essentielle.
- N'a pas de contrat SLA contraignant. Donc, l'interruption de service de 24 heures ou plus n'entraîne pas la responsabilité financière.
- Affiche un faible taux de modification des données (par exemple, des transactions par heure) et accepte une perte de données correspondant à une heure de modifications.
- Fait l’objet d’un suivi rigoureux des coûts.

Si vous avez besoin d’une récupération plus rapide, utilisez la [géo-réplication active](sql-database-geo-replication-overview.md) (abordée plus loin). Si vous devez pouvoir récupérer les données d’une période antérieure à 35 jours, archivez votre base de données régulièrement vers un fichier BACPAC (fichier compressé contenant votre schéma de base de données et les données associées) stocké dans le stockage d’objets blob Azure ou dans l’emplacement de votre choix. Pour plus d’informations sur la façon de créer une archive de base de données cohérente d’un point de vue transactionnel, consultez les rubriques [Créer une copie de base de données](sql-database-copy.md) et [Exporter la copie de base de données](sql-database-export.md).

### Utiliser la géo-réplication active pour réduire le délai de récupération et les pertes de données associées à une récupération

Outre l’utilisation de sauvegardes de base de données pour récupérer une base de données en cas d’une interruption de service, vous pouvez utiliser la [géo-réplication active](sql-database-geo-replication-overview.md) pour configurer une base de données contenant jusqu'à 4 bases de données secondaires accessibles en lecture dans les régions de votre choix. Ces bases de données secondaires sont synchronisées avec la base de données primaire à l’aide d’un mécanisme de réplication asynchrone. Cette fonctionnalité sert à vous protéger contre l’interruption de service en cas de panne du centre de données ou de mise à niveau de l’application. La géo-réplication active peut également fournir de meilleures performances pour les requêtes en lecture seule adressées à des utilisateurs géographiquement dispersés.

Si la base de données primaire se déconnecte de manière inattendue ou si vous devez la mettre hors ligne pour des activités de maintenance, vous pouvez rapidement promouvoir un serveur secondaire en serveur principal (opération également appelée basculement) et configurer les applications pour une connexion au serveur principal nouvellement promu. Le basculement planifié évite toute perte de données. Avec un basculement non planifié, quelques pertes de données peuvent survenir pour les transactions très récentes en raison de la nature de la réplication asynchrone. Après un basculement, vous pouvez effectuer une restauration automatique ultérieurement, en fonction d’un plan ou lorsque le centre de données redevient disponible. Dans tous les cas, les utilisateurs seront confrontés à quelques interruptions de service minimes et devront se reconnecter.

> [AZURE.IMPORTANT] Pour utiliser la géo-réplication active, vous devez être le propriétaire ou disposer d’autorisations administratives dans SQL Server. Vous pouvez configurer et effectuer un basculement à l’aide du portail Azure, de PowerShell ou de l’API REST, en utilisant les autorisations concernant l’abonnement ou à l’aide de Transact-SQL en utilisant des autorisations dans SQL Server.

Utilisez la géo-réplication active si votre application répond à l’un des critères suivants :

- Est essentielle.
- A un contrat de niveau de service (SLA) qui n’autorise pas plus de 24 heures d’interruption de service.
- Toute interruption de service engagera la responsabilité financière.
- Affiche un taux élevé de données modifiées et la perte d’une heure de données n’est pas acceptable.
- Le coût supplémentaire lié à l'utilisation de la géo-réplication est plus faible que la responsabilité financière potentielle et la perte d'activité associée.

## Récupérer une base de données après une erreur d’utilisateur ou d’application

* Personne n’est parfait ! Un utilisateur peut supprimer par inadvertance des données, un tableau important voire une base de données toute entière. Ou, une erreur d’application peut accidentellement remplacer les données correctes par des données incorrectes.

Dans ce scénario, voici les options de récupération dont vous disposez.

### Effectuer une limite de restauration dans le temps

Vous pouvez utiliser les sauvegardes automatisées pour récupérer une copie de votre base de données à un point clairement identifié dans le temps, à condition que ce point figure dans la période de rétention de la base de données. Une fois la base de données restaurée, vous pouvez remplacer la base de données d’origine par la base de données restaurée ou copier les informations nécessaires des données restaurées vers la base de données d’origine. Si la base de données utilise la géo-réplication active, nous vous recommandons de copier les données requises de la copie restaurée vers la base de données d’origine. Si vous remplacez la base de données d’origine par la base de données restaurée, vous devrez reconfigurer et resynchroniser la géo-réplication active (ce qui peut prendre un certain temps avec des bases de données volumineuses).

Pour plus d’informations et obtenir la procédure détaillée pour la restauration d’une base de données à une limite dans le temps à l’aide du portail Azure ou de PowerShell, consultez la rubrique [Limite de restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore). Vous ne pouvez effectuer une récupération à l’aide de Transact-SQL.

### restauration d’une base de données supprimée.

Si la base de données est supprimée mais pas le serveur logique, vous pouvez restaurer cette base de données au point où sa suppression s’est produite. Cette opération restaure une sauvegarde de la base de données sur le même serveur SQL logique d’où elle a été supprimée. Vous pouvez la restaurer en utilisant son nom d’origine, ou attribuer un nouveau nom à la base de données restaurée.

Pour plus d’informations et obtenir la procédure détaillée pour la restauration d’une base de données supprimée à l’aide du portail Azure ou de PowerShell, consultez la rubrique [Restauration d’une base de données supprimée](sql-database-recovery-using-backups.md#deleted-database-restore). Vous ne pouvez pas restaurer à l’aide de Transact-SQL.

> [AZURE.IMPORTANT] Si le serveur logique est supprimé, vous ne pouvez pas récupérer une base de données supprimée.

### Importer à partir d’une archive de base de données

Si la perte de données s’est produite en dehors de la période de rétention actuelle pour des sauvegardes automatisées et que vous avez archivé la base de données, vous pouvez [importer un fichier BACPAC archivé](sql-database-import.md) dans une nouvelle base de données. À ce stade, vous pouvez remplacer la base de données d’origine par la base de données importée ou copier les informations nécessaires des données importées vers la base de données d’origine.

## Récupérer une base de données vers une autre région suite à une panne du centre de données régional Azure

<!-- Explain this scenario -->

Bien que rare, un centre de données Azure peut subir une panne. En cas de panne, il entraîne une interruption de service qui peut durer que quelques minutes à plusieurs heures.

- Vous pouvez attendre que votre base de données redevienne disponible une fois la panne réparée au niveau du centre de données. Cette méthode fonctionne pour les applications qui peuvent se permettre d’avoir la base de données déconnectée. C’est le cas, par exemple, d’un projet de développement ou d’une version d’évaluation gratuite sur lesquels vous n’avez pas à travailler en permanence. Lorsqu’un centre de données subit une panne, vous ne savez combien de temps cette panne durera. Cette méthode fonctionne donc uniquement si vous n’avez pas à travailler sur votre base de données pendant un certain temps.
- Une autre solution consiste à basculer vers une autre région de données si vous utilisez la géo-réplication active ou la restauration à l’aide de sauvegardes de bases de données géo-redondantes (restauration géographique). Le basculement ne prend que quelques secondes tandis la récupération à partir de sauvegardes dure plusieurs heures.

Lorsque vous prenez une décision, le délai de la récupération et la quantité de données perdues en cas de panne du centre de données dépendent de la façon dont vous décidez d’utiliser les fonctionnalités de continuité d’activité décrites ci-dessus dans votre application. En effet, vous pouvez choisir d’utiliser une combinaison de sauvegardes de bases de données et de géo-réplication active en fonction des besoins de votre application. Pour plus d’informations sur la conception d’applications pour des bases de données autonomes et des pools élastiques à l’aide de ces fonctionnalités de continuité d’activité, consultez les sections [Concevoir une application pour la récupération d’urgence cloud ](sql-database-designing-cloud-solutions-for-disaster-recovery.md) et [Stratégies de récupération d’urgence de pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

Les sections suivantes fournissent une vue d’ensemble des étapes de la récupération à l’aide de sauvegardes de bases de données ou de la géo-réplication active. Pour des instructions détaillées, y compris les exigences de planification, les étapes de post-récupération et des informations sur la simulation d’une panne pour effectuer un exercice de récupération d’urgence, consultez la section [Récupération d’une base de données SQL après une panne](sql-database-disaster-recovery.md).

### Se préparer à une panne

Quelle que soit la fonctionnalité de continuité d’activité que vous utiliserez, vous devez :

- Identifier et préparer le serveur cible, y compris les règles de pare-feu au niveau du serveur, les connexions et les autorisations au niveau de la base de données master.
- Déterminer comment vous allez rediriger les clients et les applications clientes vers le nouveau serveur
- Documenter les autres dépendances, notamment les paramètres d’audit et les alertes
 
Si la planification et la préparation ne sont pas effectuées correctement, la mise en ligne de vos applications après un basculement ou une restauration prendra plus de temps et nécessitera probablement de résoudre certains problèmes dans une situation de stress : une combinaison très risquée.

### Basculement vers une base de données secondaire géo-répliquée 

Si vous utilisez la géo-réplication active comme mécanisme de récupération, [forcez un basculement vers un serveur secondaire géo-répliqué](sql-database-disaster-recovery.md#failover-to-geo-replicated-secondary-database). En quelques secondes, le serveur secondaire est promu comme nouveau serveur principal et peut alors enregistrer de nouvelles transactions et répondre à toutes les requêtes, avec seulement quelques secondes de perte de données pour les informations qui n’avaient pas encore été répliquées. Pour plus d’informations sur l’automatisation du processus de basculement, consultez la section [Concevoir une application pour la récupération d’urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [AZURE.NOTE] Lorsque le centre de données redevient disponible, vous pouvez effectuer une restauration automatique vers le serveur principal d’origine (ou pas).

### Effectuer une restauration géographique 

Si vous utilisez des sauvegardes automatisées avec une réplication de stockage géo-redondant comme mécanisme de récupération, [lancez une récupération de base de données à l’aide de la restauration géographique](sql-database-disaster-recovery.md#recover-using-geo-restore). La récupération interviendra sous 12 heures dans la plupart des cas, avec une perte de données d’1 heure maximum, selon le moment où la dernière sauvegarde différentielle horaire a été effectuée et répliquée. Tant que la récupération n’est pas terminée, la base de données ne pourra enregistrer aucune transaction ni répondre à aucune requête.

> [AZURE.NOTE] Si le centre de données redevient disponible avant que vous ne transfériez votre application vers la base de données récupérée, vous pouvez simplement annuler la récupération.

### Exécution de tâches de post-basculement/récupération 

Après la récupération à l’aide d’un de ces mécanismes de récupération, vous devez effectuer les tâches supplémentaires suivantes afin que les utilisateurs et les applications soient de nouveau opérationnels :

- Rediriger les clients et les applications clientes vers le nouveau serveur et la base de données restaurée
- Vérifier que les règles de pare-feu appropriées au niveau du serveur sont en place pour permettre aux utilisateurs de se connecter (ou utiliser le [pare-feu au niveau de la base de données](sql-database-firewall-configure.md#creating-database-level-firewall-rules))
- Vérifier que les connexions et les autorisations appropriées au niveau de la base de données master sont en place (ou utiliser des [utilisateurs contenus](https://msdn.microsoft.com/library/ff929188.aspx))
- Configurer l’audit, selon les besoins
- Configurer les alertes, selon les besoins

## Mettre à niveau une application avec un temps d’arrêt minimal

Parfois, une application doit être déconnectée en raison d’une maintenance planifiée, par exemple une mise à niveau. La section [Gestion des mises à niveau des applications](sql-database-manage-application-rolling-upgrade.md) explique comment utiliser la géo-réplication active pour activer les mises à niveau propagées de votre application cloud afin de réduire le temps d’arrêt pendant les mises à niveau et de fournir un chemin de récupération en cas de problème. Cet article présente deux méthodes différentes permettant d’orchestrer le processus de mise à niveau propagée, et précise les avantages et inconvénients de chaque option.

## Étapes suivantes

Pour plus d’informations sur la conception d’applications pour des bases de données autonomes et des pools élastiques, consultez les sections [Concevoir une application pour la récupération d’urgence cloud ](sql-database-designing-cloud-solutions-for-disaster-recovery.md) et [Stratégies de récupération d’urgence de pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

<!---HONumber=AcomDC_0727_2016-->