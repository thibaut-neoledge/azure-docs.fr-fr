---
title: "Gestion et dépannage de Stretch Database | Microsoft Docs"
description: "Découvrez comment gérer et dépanner Stretch Database"
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 8a43c0fc-64d3-4042-8921-a36542aa8933
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 681ad472e53a17600b589d8354d9fdb5c9c3c574


---
# <a name="manage-and-troubleshoot-stretch-database"></a>Gestion et dépannage de Stretch Database
Utilisez les outils et les méthodes décrites dans cette rubrique pour gérer et dépanner Stretch Database.

## <a name="manage-local-data"></a>Gérer les données locales
### <a name="a-namelocalinfoaget-info-about-local-databases-and-tables-enabled-for-stretch-database"></a><a name="LocalInfo"></a>Obtenir des informations sur les bases de données et les tables locales Stretch Database
Ouvrez les affichages catalogue **sys.databases** et **sys.tables** pour afficher des informations sur les bases de données et les tables Stretch SQL Server. Pour plus d’informations, voir [sys.databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) et [sys.tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx).

Pour afficher la quantité d’espace utilisé par une table Stretch, exécutez l’instruction suivante.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'LOCAL_ONLY';
GO
 ```
## <a name="manage-data-migration"></a>Gérer la migration des données
### <a name="check-the-filter-function-applied-to-a-table"></a>Vérifier la fonction de filtre appliquée à une table
Ouvrez l’affichage catalogue **sys.remote\_data\_archive\_tables** et vérifiez la valeur de la colonne **filter\_predicate** pour identifier la fonction que Stretch Database utilise pour sélectionner les lignes à migrer. Si la valeur est null, la table entière est éligible à la migration. Pour plus d’informations, consultez [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx) et [Sélection des lignes à migrer à l’aide d’une fonction de filtre](sql-server-stretch-database-predicate-function.md).

### <a name="a-namemigrationacheck-the-status-of-data-migration"></a><a name="Migration"></a>Vérifier l’état de la migration des données
Sélectionnez **Tâches | Stretch | Surveiller** pour une base de données dans SQL Server Management Studio pour surveiller la migration des données dans Stretch Database Monitor. Pour plus d’informations, voir [Surveillance et dépannage de la migration de données (Stretch Database)](sql-server-stretch-database-monitor.md).

Ou ouvrez la vue de gestion dynamique **sys.dm\_db\_rda\_migration\_status** pour voir combien de lots et de lignes de données ont été migrés.

### <a name="a-namefirewallatroubleshoot-data-migration"></a><a name="Firewall"></a>Dépannage de la migration des données
Pour obtenir des suggestions de dépannage, voir [Surveillance et dépannage de la migration de données (Stretch Database)](sql-server-stretch-database-monitor.md).

## <a name="manage-remote-data"></a>Gérer les données distantes
### <a name="a-nameremoteinfoaget-info-about-remote-databases-and-tables-used-by-stretch-database"></a><a name="RemoteInfo"></a>Obtenir des informations sur les bases de données et les tables distantes Stretch Database
Ouvrez les affichages catalogue **sys.remote\_data\_archive\_databases** et **sys.remote\_data\_archive\_tables** pour afficher des informations sur les bases de données et les tables distantes dans lesquelles les données migrées sont stockées. Pour plus d’informations, voir [sys.remote_data_archive_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) et [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

Pour afficher la quantité d’espace utilisé par une table Stretch dans Azure, exécutez l’instruction suivante.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'REMOTE_ONLY';
GO
 ```

### <a name="delete-migrated-data"></a>Supprimer les données migrées
Si vous souhaitez supprimer des données déjà migrées vers Azure, suivez les étapes décrites dans [sys.sp_rda_reconcile_batch](https://msdn.microsoft.com/library/mt707768.aspx).

## <a name="manage-table-schema"></a>Gérer le schéma de table
### <a name="dont-change-the-schema-of-the-remote-table"></a>Ne modifiez pas le schéma de la table distante
Ne modifiez pas le schéma d’une table Azure distante qui est associée à une table SQL Server configurée pour Stretch Database. En particulier, ne modifiez pas le nom ou le type de données d’une colonne. La fonctionnalité Stretch Database part de diverses hypothèses concernant le schéma de la table distante en relation avec le schéma de la table SQL Server. Si vous modifiez le schéma, Stretch Database cesse de fonctionner pour la table modifiée.

### <a name="reconcile-table-columns"></a>Rapprocher des colonnes de table
Si vous avez accidentellement supprimé les colonnes de la table distante, exécutez **sp_rda_reconcile_columns** pour ajouter des colonnes à la table distante qui existent dans la table Stretch SQL Server, mais pas dans la table distante. Pour plus d’informations, voir [sys.sp_rda_reconcile_columns](https://msdn.microsoft.com/library/mt707765.aspx).  

> [!IMPORTANT]
> Lorsque **sp_rda_reconcile_columns** recrée les colonnes que vous avez supprimées par inadvertance de la table distante, il ne restaure pas les données qui se trouvaient précédemment dans les colonnes supprimées.
> 
> 

**sp_rda_reconcile_columns** ne supprime pas les colonnes de la table distante qui existent dans la table distante, mais qui n’existent pas dans la table Stretch SQL Server. Si des colonnes de la table Azure distante n’existent plus dans la table Stretch SQL Server, ces colonnes supplémentaires n’empêchent pas Stretch Database de fonctionner normalement. Vous pouvez éventuellement supprimer manuellement les colonnes supplémentaires.  

## <a name="manage-performance-and-costs"></a>Gestion des coûts et des performances
### <a name="troubleshoot-query-performance"></a>Dépannage de la performance des requêtes
Les requêtes qui incluent des tables compatibles Stretch ont des performances plus lentes qu’avant leur mise en compatibilité pour Stretch. Si les performances des requêtes se dégradent considérablement, passez en revue les problèmes suivants.

* Votre serveur Azure se trouve-t-il dans une autre région géographique que votre serveur SQL ? Configurez votre serveur Azure de manière à ce qu’il se trouve dans la même région géographique que votre serveur SQL Server afin de réduire la latence du réseau.
* Les conditions de votre réseau peuvent s’être dégradées. Pour plus d’informations sur les problèmes ou les défaillances récents, contactez votre administrateur réseau.

### <a name="increase-azure-performance-level-for-resource-intensive-operations-such-as-indexing"></a>Augmenter le niveau de performances d’Azure pour les opérations gourmandes en ressources telles que l’indexation
Lorsque vous générez, regénérez ou réorganisez un index dans une table volumineuse configurée pour Stretch Database, et que vous anticipez de nombreuses interrogations sur les données migrées dans Azure dans ce laps de temps, envisagez d’augmenter le niveau de performances de la base de données Azure distante correspondante pour la durée de l’opération. Pour plus d’informations sur les niveaux de performances et la tarification, voir [Tarification SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="you-cant-pause-the-sql-server-stretch-database-service-on-azure"></a>Vous ne pouvez pas suspendre le service SQL Server Stretch Database sur Azure
 Assurez-vous de sélectionner des performances et un niveau de tarification appropriés. Si vous augmentez le niveau de performances temporairement pour une opération gourmande en ressources, n’oubliez pas de restaurer le niveau précédent une fois l’opération effectuée. Pour plus d’informations sur les niveaux de performances et la tarification, voir [Tarification SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).  

## <a name="change-the-scope-of-queries"></a>Modifier l’étendue des requêtes
 Les requêtes dans les tables Stretch renvoient des données locales et distantes par défaut. Vous pouvez modifier l’étendue des requêtes pour toutes les requêtes effectuées par tous les utilisateurs ou pour une seule requête effectuée par un administrateur.  

### <a name="change-the-scope-of-queries-for-all-queries-by-all-users"></a>Modifier l’étendue des requêtes pour toutes les requêtes effectuées par tous les utilisateurs
 Pour modifier l’étendue de toutes les requêtes effectuées par tous les utilisateurs, exécutez la procédure stockée **sys.sp_rda_set_query_mode**. Vous pouvez réduire la portée pour interroger uniquement les données locales, désactiver toutes les requêtes ou restaurer le paramètre par défaut. Pour plus d’informations, voir [sys.sp_rda_set_query_mode](https://msdn.microsoft.com/library/mt703715.aspx).  

### <a name="a-namequeryhintsachange-the-scope-of-queries-for-a-single-query-by-an-administrator"></a><a name="queryHints"></a>Modifier l’étendue des requêtes pour une seule requête effectuée par un administrateur
 Pour modifier l’étendue d’une seule requête effectuée par un membre du rôle db_owner, ajoutez l’indicateur de requête **WITH \( REMOTE_DATA_ARCHIVE_OVERRIDE = *value* \)** à l’instruction SELECT. L’indicateur de requête REMOTE_DATA_ARCHIVE_OVERRIDE peut avoir les valeurs suivantes.  

* **LOCAL_ONLY**. Interroge uniquement les données locales.  
* **REMOTE_ONLY**. Interroge uniquement les données à distance.  
* **STAGE_ONLY**. Interroge uniquement les données de la table dans laquelle Stretch Database range les lignes éligibles pour la migration et conserve les lignes migrées pour la période indiquée après la migration. Cet indicateur de requête est le seul moyen d’interroger la table de mise en lots.  

Par exemple, la requête suivante renvoie uniquement des résultats locaux.  

 ```tsql  
 USE <Stretch-enabled database name>;
 GO
 SELECT * FROM <Stretch_enabled table name> WITH (REMOTE_DATA_ARCHIVE_OVERRIDE = LOCAL_ONLY) WHERE ... ;
 GO
```  

## <a name="a-nameadminhintsamake-administrative-updates-and-deletes"></a><a name="adminHints"></a>Effectuer des mises à jour et des suppressions administratives
 Par défaut, il est impossible de METTRE À JOUR ou de SUPPRIMER des lignes ayant été migrées ou pouvant être migrées dans une table Stretch. Lorsque vous devez résoudre un problème, un membre du rôle db_owner peut exécuter une opération UPDATE ou DELETE en ajoutant l’indicateur de requête **WITH \( REMOTE_DATA_ARCHIVE_OVERRIDE = *value* \)** à l’instruction. L’indicateur de requête REMOTE_DATA_ARCHIVE_OVERRIDE peut avoir les valeurs suivantes.  

* **LOCAL_ONLY**. Met à jour ou supprime uniquement les données locales.  
* **REMOTE_ONLY**. Met à jour ou supprime uniquement les données distantes.  
* **STAGE_ONLY**. Met à jour ou supprime uniquement les données de la table dans laquelle Stretch Database range les lignes éligibles pour la migration et conserve les lignes migrées pour la période indiquée après la migration.  

## <a name="see-also"></a>Voir aussi
[Surveiller Stretch Database](sql-server-stretch-database-monitor.md)

[Sauvegarder des bases de données Stretch](sql-server-stretch-database-backup.md)

[Restaurer des bases de données Stretch](sql-server-stretch-database-restore.md)




<!--HONumber=Nov16_HO3-->


