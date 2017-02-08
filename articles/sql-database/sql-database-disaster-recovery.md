---
title: "Récupération d’urgence de SQL Database | Microsoft Docs"
description: "Découvrez comment récupérer une base de données en cas de panne d’un centre de données régional grâce aux fonctionnalités de géo-réplication active et de restauration géographique du service Base de données SQL Azure."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: 4800960e-3f9d-40ce-9e55-fb7f2784c067
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2016
ms.author: sashan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8b898e56a9f7f76d7a342c102fb6912408116c5a


---
# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Restaurer une base de données SQL Azure ou basculer vers une base de données secondaire
Le service Base de données SQL Azure offre les fonctionnalités suivantes pour la récupération après une panne :

* [Géo-réplication active](sql-database-geo-replication-overview.md)
* [Restauration géographique](sql-database-recovery-using-backups.md#point-in-time-restore)

Pour en savoir plus sur les scénarios de continuité d’activité et les fonctionnalités prenant en charge ces scénarios, consultez [Continuité des activités](sql-database-business-continuity.md).

### <a name="prepare-for-the-event-of-an-outage"></a>Préparation à une panne
Pour réussir la récupération vers une autre région de données à l’aide de la géo-réplication active ou de sauvegardes géo-redondantes, vous devez préparer un serveur d’un autre centre de données qui deviendra le nouveau serveur principal en cas de besoin. Vous devez également suivre des étapes précises, documentées et éprouvées pour garantir une récupération optimale. Les étapes de préparation sont les suivantes :

* Identifiez le serveur logique d’une autre région qui deviendra le nouveau serveur principal. La géo-réplication active permet d’utiliser au moins un serveur voire tous les serveurs secondaires. Pour la géo-restauration, il s’agit généralement d’un serveur dans la [région jumelée](../best-practices-availability-paired-regions.md) de la région dans laquelle se trouve votre base de données.
* Identifiez, et éventuellement définissez, les règles de pare-feu nécessaires au niveau du serveur pour permettre aux utilisateurs d’accéder à la nouvelle base de données primaire.
* Déterminez la façon dont vous souhaitez rediriger les utilisateurs vers le nouveau serveur principal, par exemple en modifiant des chaînes de connexion ou des entrées DNS.
* Identifiez, et éventuellement créez, les connexions d’accès qui doivent être présentes dans la base de données master sur le nouveau serveur principal, puis vérifiez que ces connexions disposent des autorisations appropriées dans la base de données master, le cas échéant. Pour plus d’informations, consultez [Gestion de la sécurité de la base de données SQL après la récupération d’urgence](sql-database-geo-replication-security-config.md)
* Identifiez les règles d’alerte qui devront être mises à jour pour le mappage à la nouvelle base de données primaire.
* Documentation de la configuration de l’audit sur la base de données primaire actuelle
* Effectuez une [simulation d’une récupération d'urgence](sql-database-disaster-recovery-drills.md). Pour simuler une panne de restauration géographique, vous pouvez supprimer ou renommer la base de données source pour empêcher l’application de se connecter. Pour simuler une panne de géo-réplication active, vous pouvez désactiver l’application web ou une machine virtuelle connecté à la base de données ou basculer la base de données pour empêcher l’application de se connecter.

## <a name="when-to-initiate-recovery"></a>Quand initier la récupération ?
L'opération de récupération a un impact sur l'application. Elle requiert la modification de la chaîne de connexion SQL et la redirection avec DNS, et peut entraîner une perte de données définitive. Par conséquent, elle doit être effectuée uniquement quand la défaillance est susceptible de durer plus longtemps que l’objectif de délai de récupération de votre application. Lorsque l'application est déployée en production, vous devez effectuer une surveillance régulière de l'intégrité de l'application et vous assurer des points suivants pour déclarer que la récupération est garantie :

1. Échec permanent de la connectivité de la couche d'application à la base de données.
2. Le portail Azure affiche une alerte concernant un incident ayant un impact important dans la région.
3. Le serveur de base de données SQL Azure est marqué comme étant détérioré.

Selon la tolérance de votre application aux temps d’arrêt et la mise en cause potentielle de la responsabilité de votre entreprise, vous pouvez envisager les options de récupération suivantes.

Utilisez [Obtenir une base de données récupérable](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) pour obtenir le dernier point de restauration avec réplication géographique.

## <a name="wait-for-service-recovery"></a>Attendre le rétablissement du service
Les équipes Azure mettent tous les efforts en œuvre pour restaurer le service aussi rapidement que possible, mais cela peut prendre plusieurs heures ou plusieurs jours selon la cause principale du problème.  Si votre application peut tolérer des temps d’arrêt importants, vous pouvez simplement attendre que le service soit rétabli. Dans ce cas, aucune action n’est requise de votre part. Vous pouvez consulter l’état actuel du service dans notre [tableau de bord d’état du service Azure](https://azure.microsoft.com/status/). Une fois le service rétabli dans la région, la disponibilité de votre application sera restaurée.

## <a name="failover-to-geo-replicated-secondary-database"></a>Basculement vers la base de données secondaire géo-répliquée
Si les temps d’arrêt peuvent mettre en cause la responsabilité de votre entreprise, vous devez utiliser des bases de données géo-répliquées dans votre application. Cela permet de restaurer rapidement la disponibilité de l’application dans une autre région en cas de panne. Découvrez comment [configurer la géo-réplication](sql-database-geo-replication-portal.md).

Pour restaurer la disponibilité des bases de données, vous devez lancer le basculement vers la base de données secondaire géo-répliquée à l’aide d’une des méthodes prises en charge.

Utilisez l’un des guides suivants pour effectuer le basculement vers une base de données secondaire géo-répliquée :

* [Basculement vers une base de données secondaire géo-répliquée à l’aide du portail Azure](sql-database-geo-replication-portal.md)
* [Basculement vers une base de données secondaire géo-répliquée à l’aide de PowerShell](sql-database-geo-replication-powershell.md)
* [Basculement vers une base de données secondaire géo-répliquée à l’aide de T-SQL](sql-database-geo-replication-transact-sql.md)

## <a name="recover-using-geo-restore"></a>Récupération à l’aide de la géo-restauration
Si les temps d’arrêt ne mettent pas en cause la responsabilité de votre entreprise, vous pouvez utiliser la géo-restauration pour récupérer les bases de données de votre application. Cela permet de créer une copie de la base de données à partir de la dernière sauvegarde géo-redondante.

Utilisez l’un des guides suivants pour géo-restaurer une base de données dans une nouvelle région :

* [Géo-restaurer une base de données dans une nouvelle région à l’aide du portail Azure](sql-database-geo-restore-portal.md)
* [Géo-restaurer une base de données dans une nouvelle région à l’aide de PowerShell](sql-database-geo-restore-powershell.md)

## <a name="configure-your-database-after-recovery"></a>Configurer votre base de données après récupération
Si vous utilisez le basculement par géo-réplication ou la géo-restauration à des fins de restauration après une panne, vous devez vous assurer que la connectivité aux nouvelles bases de données est correctement configurée pour garantir la reprise du fonctionnement normal de l’application. Voici une liste de contrôle de tâches pour vous aider à remettre votre base de données restaurée en service.

### <a name="update-connection-strings"></a>Mettre à jour les chaînes de connexion
Comme votre base de données restaurée se trouve sur un autre serveur, vous devez mettre à jour la chaîne de connexion de votre application de sorte qu’elle pointe vers celui-ci.

Pour plus d’informations sur la modification des chaînes de connexion, consultez le langage de développement approprié pour votre [bibliothèque de connectivité](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Configurer les règles de pare-feu
Vous devez vous assurer que les règles de pare-feu configurées sur le serveur et sur la base de données correspondent à celles du serveur principal et de la base de données primaire. Pour plus d’informations, voir [Procédure : configuration des paramètres du pare-feu (Base de données SQL Azure)](sql-database-configure-firewall-settings.md).

### <a name="configure-logins-and-database-users"></a>Configurer les identifiants de connexion et les utilisateurs de la base de données
Vous devez vous assurer que tous les identifiants de connexion utilisés par votre application existent sur le serveur qui héberge votre base de données restaurée. Pour plus d’informations, voir [Configuration de la sécurité de la géo-réplication](sql-database-geo-replication-security-config.md).

> [!NOTE]
> Vous devez configurer et tester les règles et les connexions (et leurs autorisations) du pare-feu de votre serveur pendant un exercice de récupération d’urgence. Ces objets au niveau du serveur et leur configuration peuvent ne pas être disponibles pendant la panne.
> 
> 

### <a name="setup-telemetry-alerts"></a>Configurer les alertes de télémétrie
Vous devez vous assurer que vos paramètres de règles d’alerte existants sont mis à jour de manière à mapper à la base de données restaurée et à l’autre serveur.

Pour en savoir plus, voir [Réception de notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) et [Suivi de l’intégrité du service](../monitoring-and-diagnostics/insights-service-health.md).

### <a name="enable-auditing"></a>Activer la fonction d’audit
Si la fonction d’audit doit accéder à votre base de données, vous devez l’activer après la restauration de la base de données. Un bon indicateur de la nécessité d’activer l’audit est l’utilisation, par les applicatives clientes, de chaînes de connexion sécurisées dans un modèle *.database.secure.windows.net. Pour en savoir plus, voir [Prise en main de l’audit de base de données SQL](sql-database-auditing-get-started.md).

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les sauvegardes automatisées d’une base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)
* Pour en savoir plus sur la conception de la continuité des activités et les scénarios de récupération, consultez [Scénarios de continuité des activités](sql-database-business-continuity.md)
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md)




<!--HONumber=Nov16_HO3-->


