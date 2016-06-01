<properties
	pageTitle="Gestion et dépannage de Stretch Database | Microsoft Azure"
	description="Découvrez comment gérer et dépanner Stretch Database"
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="douglasl"/>

# Gestion et dépannage de Stretch Database

Utilisez les outils et les méthodes décrites dans cette rubrique pour gérer et dépanner Stretch Database.

## <a name="LocalInfo"></a>Obtenir des informations sur les bases de données et les tables locales compatibles Stretch Database
Ouvrez les affichages catalogue **sys.databases** et **sys.tables** pour afficher des informations sur les bases de données SQL Server et les tables compatibles Stretch. Pour plus d’informations, consultez [sys.databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) et [sys.tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx).

## <a name="RemoteInfo"></a>Obtenir des informations sur les bases de données et les tables distantes compatibles Stretch Database
Ouvrez les affichages catalogue **sys.remote\_data\_archive\_databases** et **sys.remote\_data\_archive\_tables** pour afficher des informations sur les bases de données et les tables distantes dans lesquelles les données migrées sont stockées. Pour plus d’informations, consultez [sys.remote\_data\_archive\_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) et [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## Vérifier le prédicat de filtre appliqué à une table
Ouvrez l’affichage catalogue **sys.remote\_data\_archive\_tables** et vérifiez la valeur de la colonne **filter\_predicate**. Si la valeur est null, la table entière est éligible à la migration. Pour plus d’informations, consultez [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="Migration"></a>Vérifier l’état de la migration des données
Sélectionnez **Tâches | Stretch | Surveiller** pour une base de données dans SQL Server Management Studio pour surveiller la migration des données dans Stretch Database Monitor. Pour plus d’informations, consultez [Surveillance et dépannage de la migration de données (Stretch Database)](sql-server-stretch-database-monitor.md).

Ou ouvrez la vue de gestion dynamique **sys.dm\_db\_rda\_migration\_status** pour voir combien de lots et de lignes de données ont été migrés.

## Augmenter le niveau de performances d’Azure pour les opérations gourmandes en ressources telles que l’indexation
Lorsque vous générez, regénérez ou réorganisez un index dans une table volumineuse configurée pour Stretch Database, envisagez d’augmenter le niveau de performance de la base de données distante correspondante pour la durée de l’opération.

## Ne modifiez pas le schéma de la table distante
Ne modifiez pas le schéma d’une table Azure distante qui est associée à une table SQL Server configurée pour Stretch Database. En particulier, ne modifiez pas le nom ou le type de données d’une colonne. La fonctionnalité Stretch Database part de diverses hypothèses concernant le schéma de la table distante en relation avec le schéma de la table SQL Server. Si vous modifiez le schéma, Stretch Database cesse de fonctionner pour la table modifiée.

## <a name="Firewall"></a>Dépannage de la migration des données
Pour obtenir des suggestions de dépannage, consultez [Surveillance et dépannage de la migration de données (Stretch Database)](sql-server-stretch-database-monitor.md).

## Dépannage de la performance des requêtes
**Les requêtes qui incluent ma table compatible Stretch sont lentes.** Les requêtes qui incluent des tables compatibles Stretch ont des performances plus lentes qu’avant leur mise en compatibilité pour Stretch. Si les performances des requêtes se dégradent considérablement, passez en revue les problèmes suivants.

-   Votre serveur Azure se trouve-t-il dans une autre région géographique que votre serveur SQL ? Configurez votre serveur Azure de manière à ce qu’il se trouve dans la même région géographique que votre serveur SQL Server afin de réduire la latence du réseau.

-   Les conditions de votre réseau peuvent s’être dégradées. Pour plus d’informations sur les problèmes ou les défaillances récents, contactez votre administrateur réseau.

## Voir aussi

[Surveiller Stretch Database](sql-server-stretch-database-monitor.md)

[Sauvegarder et restaurer des bases de données Stretch](sql-server-stretch-database-backup.md)

<!---HONumber=AcomDC_0518_2016-->