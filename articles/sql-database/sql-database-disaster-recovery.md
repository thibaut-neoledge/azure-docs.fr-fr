<properties 
   pageTitle="Récupération d’urgence de la base de données SQL | Microsoft Azure" 
   description="Découvrez comment récupérer une base de données en cas de panne d’un centre de données régional grâce aux fonctionnalités de géo-réplication active et de restauration géographique du service Base de données SQL Azure." 
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
   ms.workload="data-management" 
   ms.date="06/16/2016"
   ms.author="carlrab"/>

# Restaurer une base de données SQL Azure ou basculer vers une base de données secondaire

Le service Base de données SQL Azure offre les fonctionnalités suivantes pour la récupération après une panne :

- [Géo-réplication active](sql-database-geo-replication-overview.md)
- [Restauration géographique](sql-database-geo-restore.md)

Pour en savoir plus sur la préparation aux sinistres et découvrir quand récupérer votre base de données, visitez notre page [Conception pour la continuité des activités](sql-database-business-continuity-design.md).

## Quand initier la récupération ?

L'opération de récupération a un impact sur l'application. Elle requiert la modification de la chaîne de connexion SQL et peut entraîner une perte de données définitive. Par conséquent, elle doit être effectuée uniquement quand la défaillance est susceptible de durer plus longtemps que le RTO de votre application. Lorsque l'application est déployée en production, vous devez effectuer une surveillance régulière de l'intégrité de l'application et vous assurer des points suivants pour déclarer que la récupération est garantie :

1.	Échec permanent de la connectivité de la couche d'application à la base de données.
2.	Le portail Azure affiche une alerte concernant un incident ayant un impact important dans la région.
3.	Le serveur de base de données SQL Azure est marqué comme étant détérioré. 

Selon la tolérance de votre application aux temps d’arrêt et la mise en cause potentielle de la responsabilité de votre entreprise, vous pouvez envisager les options de récupération suivantes.

Utilisez [Obtenir une base de données récupérable](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) pour obtenir le dernier point de restauration avec réplication géographique.

## Attendre le rétablissement du service

Les équipes Azure mettent tous les efforts en œuvre pour restaurer le service aussi rapidement que possible, mais cela peut prendre plusieurs heures ou plusieurs jours selon la cause principale du problème. Si votre application peut tolérer des temps d’arrêt importants, vous pouvez simplement attendre que le service soit rétabli. Dans ce cas, aucune action n’est requise de votre part. Vous pouvez consulter l’état actuel du service dans notre [tableau de bord d’état du service Azure](https://azure.microsoft.com/status/). Une fois le service rétabli dans la région, la disponibilité de votre application sera restaurée.

## Basculement vers la base de données secondaire géo-répliquée

Si les temps d’arrêt peuvent mettre en cause la responsabilité de votre entreprise, vous devez utiliser des bases de données géo-répliquées dans votre application. Cela permet de restaurer rapidement la disponibilité de l’application dans une autre région en cas de panne. Découvrez comment [configurer la réplication géographique](sql-database-geo-replication-portal.md).

Pour restaurer la disponibilité des bases de données, vous devez lancer le basculement vers la base de données secondaire géo-répliquée à l’aide d’une des méthodes prises en charge.


Utilisez l’un des guides suivants pour effectuer le basculement vers une base de données secondaire géo-répliquée :

- [Basculement vers une base de données secondaire géo-répliquée à l’aide du portail Azure](sql-database-geo-replication-portal.md)
- [Basculement vers une base de données secondaire géo-répliquée à l’aide de PowerShell](sql-database-geo-replication-powershell.md)
- [Basculement vers une base de données secondaire géo-répliquée à l’aide de T-SQL](sql-database-geo-replication-transact-sql.md) 



## Récupération à l’aide de la géo-restauration

Si les temps d’arrêt ne mettent pas en cause la responsabilité de votre entreprise, vous pouvez utiliser la géo-restauration pour récupérer les bases de données de votre application. Cela permet de créer une copie de la base de données à partir de la dernière sauvegarde géo-redondante.

Utilisez l’un des guides suivants pour géo-restaurer une base de données dans une nouvelle région :

- [Géo-restaurer une base de données dans une nouvelle région à l’aide du portail Azure](sql-database-geo-restore-portal.md)
- [Géo-restaurer une base de données dans une nouvelle région à l’aide de PowerShell](sql-database-geo-restore-powershell.md) 


## Configurer votre base de données après récupération

Si vous utilisez le basculement par géo-réplication ou la géo-restauration à des fins de restauration après une panne, vous devez vous assurer que la connectivité aux nouvelles bases de données est correctement configurée pour garantir la reprise du fonctionnement normal de l’application. Voici une liste de contrôle de tâches pour vous aider à remettre votre base de données restaurée en service.

### Mettre à jour les chaînes de connexion

Comme votre base de données restaurée se trouve sur un autre serveur, vous devez mettre à jour la chaîne de connexion de votre application de sorte qu’elle pointe vers celui-ci.

Pour plus d’informations sur la modification des chaînes de connexion, consultez le langage de développement approprié pour votre [bibliothèque de connectivité](sql-database-libraries.md).

### Configurer les règles de pare-feu

Vous devez vous assurer que les règles de pare-feu configurées sur le serveur et sur la base de données correspondent à celles du serveur principal et de la base de données primaire. Pour plus d’informations, voir [Procédure : configuration des paramètres du pare-feu (Base de données SQL Azure)](sql-database-configure-firewall-settings.md).


### Configurer les identifiants de connexion et les utilisateurs de la base de données

Vous devez vous assurer que tous les identifiants de connexion utilisés par votre application existent sur le serveur qui héberge votre base de données restaurée. Pour plus d’informations, voir [Configuration de la sécurité de la géo-réplication](sql-database-geo-replication-security-config.md).

>[AZURE.NOTE] Vous devez configurer et tester les règles et les connexions (et leurs autorisations) du pare-feu de votre serveur pendant un exercice de récupération d’urgence. Ces objets au niveau du serveur et leur configuration peuvent ne pas être disponibles pendant la panne. Pour plus d’informations, voir [Exécution de tests de récupération d’urgence](sql-database-disaster-recovery-drills.md).

### Configurer les alertes de télémétrie

Vous devez vous assurer que vos paramètres de règles d’alerte existants sont mis à jour de manière à mapper à la base de données restaurée et à l’autre serveur.

Pour en savoir plus, voir [Réception de notifications d'alerte](../azure-portal/insights-receive-alert-notifications.md) et [Suivi de l’intégrité du service](../azure-portal/insights-service-health.md).

### Activer la fonction d’audit

Si la fonction d’audit doit accéder à votre base de données, vous devez l’activer après la restauration de la base de données. Un bon indicateur de la nécessité d’activer l’audit est l’utilisation, par les applicatives clientes, de chaînes de connexion sécurisées dans un modèle *.database.secure.windows.net. Pour en savoir plus, voir [Prise en main de l’audit de base de données SQL](sql-database-auditing-get-started.md).


## Étapes suivantes

- Pour plus d’informations sur l’utilisation et la configuration de la géo-réplication active pour la récupération d’urgence, consultez [Géo-réplication active](sql-database-geo-replication-overview.md)
- Pour plus d’informations sur l’utilisation de la restauration géographique pour la récupération d’urgence, consultez [Restauration géographique](sql-database-geo-restore.md)

## Ressources supplémentaires

- [Continuité des activités et récupération d’urgence d’une base de données SQL Azure](sql-database-business-continuity.md)
- [Limite de restauration dans le temps](sql-database-point-in-time-restore.md)
- [Restauration géographique](sql-database-geo-restore.md)
- [Géo-réplication active](sql-database-geo-replication-overview.md)
- [Conception d'applications pour la récupération d'urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finaliser la base de données SQL Microsoft Azure restaurée](sql-database-recovered-finalize.md)
- [Configuration de la sécurité de la géo-réplication](sql-database-geo-replication-security-config.md)
- [FAQ sur la continuité d’activité et la récupération d’urgence des bases de données SQL](sql-database-bcdr-faq.md)

<!---HONumber=AcomDC_0622_2016-->