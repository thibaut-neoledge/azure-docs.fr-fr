<properties
   pageTitle="Non pris en charge dans Azure SQL Database T-SQL | Microsoft Azure"
   description="Instructions Transact-SQL qui ne sont pas entièrement prises en charge dans Azure SQL Database"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/30/2016"
   ms.author="rick.byham@microsoft.com"/>

# Différences dans le langage Transact-SQL Azure SQL Database.


La plupart des fonctionnalités Transact-SQL que les applications utilisent sont prises en charge dans Microsoft SQL Server et Azure SQL Database. Vous trouverez ci-dessous une liste partielle des fonctionnalités prises en charge pour les applications :

- Types de données.
- Opérateurs.
- Fonctions de chaîne, arithmétiques, logiques et de curseur.

Toutefois, Azure SQL Database est conçu pour isoler les fonctionnalités de toute dépendance sur la base de données **maître**. Par conséquent, de nombreuses activités au niveau du serveur ne correspondent pas à la base de données SQL et ne sont pas prises en charge. Les fonctionnalités déconseillées dans SQL Server ne sont généralement pas prises en charge dans la base de données SQL.

> [AZURE.NOTE]
Cette rubrique traite des fonctionnalités disponibles avec la base de données SQL lors de la mise à niveau à la version V12 de la base de données SQL. Pour plus d'informations sur la V12, consultez [Nouveautés de la base de données SQL V12](sql-database-v12-whats-new.md).

Les sections suivantes répertorient les fonctionnalités partiellement prises en charge et les fonctionnalités qui ne sont pas du tout prises en charge.


## Fonctionnalités partiellement prises en charge par la V12 de la base de données SQL

La version V12 de la base de données SQL prend en charge certains arguments qui existent dans les instructions Transact-SQL correspondantes pour SQL Server 2016. Par exemple, l’instruction CREATE PROCEDURE est disponible, mais toutes les options de CREATE PROCEDURE ne sont pas disponibles. Reportez-vous aux rubriques sur la syntaxe liées pour plus d’informations sur la prise en charge des fonctionnalités de chaque instruction.

- Bases de données : [CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER](https://msdn.microsoft.com/library/ms174269.aspx)
- Les DMV sont généralement disponibles pour les fonctionnalités disponibles.
- Fonctions : [CREATE](https://msdn.microsoft.com/library/ms186755.aspx)/[ALTER FUNCTION](https://msdn.microsoft.com/library/ms186967.aspx)
- [KILL](https://msdn.microsoft.com/library/ms173730.aspx)
- Connexions : [CREATE](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
- Procédures stockées : [CREATE](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
- Tables : [CREATE](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTER](https://msdn.microsoft.com/library/ms190273.aspx)
- Types (personnalisés) : [CREATE TYPE](https://msdn.microsoft.com/library/ms175007.aspx)
- Utilisateurs : [CREATE](https://msdn.microsoft.com/library/ms173463.aspx)/[ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)
- Onglets : [CREATE](https://msdn.microsoft.com/library/ms187956.aspx)/[ALTER VIEW](https://msdn.microsoft.com/library/ms173846.aspx)

## Fonctionnalités non prises en charge par la base de données SQL

- Classement des objets système
- Connexions liées  : instructions de point de terminaison, ORIGINAL\_DB\_NAME. La Base de données SQL ne prend pas en charge l’authentification Windows, mais elle prend en charge l’authentification Azure Active Directory similaire. Certains types d’authentification nécessitent la version la plus récente de SSMS. Pour plus d’informations, voir [Connexion au service Base de données SQL ou SQL Data Warehouse avec l’authentification Azure Active Directory](sql-database-aad-authentication.md).
- Requêtes dans plusieurs bases de données à l’aide de noms à trois ou quatre parties. (Les requêtes sur plusieurs bases de données en lecture seule sont prises en charge à l’aide d’une [requête de base de données élastique](sql-database-elastic-query-overview.md).)
- Chaînage d’appartenance entre plusieurs bases de données, paramètre TRUSTWORTHY
- Classement des données
- Schémas de base de données
- Messagerie de base de données
- DATABASEPROPERTY (utilisez plutôt DATABASEPROPERTYEX)
- Connexions EXECUTE AS
- Chiffrement : gestion extensible de clés
- Gestion des événements : événements, notifications d’événements, notifications de requête
- Fonctionnalités liées à l’emplacement du fichier de la base de données, à la taille et aux fichiers de la base de données qui sont gérés automatiquement par Microsoft Azure.
- Fonctionnalités à haute disponibilité gérée par le biais de votre compte Microsoft Azure : sauvegarde, restauration, AlwaysOn, mise en miroir de la base de données, envoi de journaux, modes de récupération. Pour en savoir plus, consultez Sauvegarde et restauration de la base de données SQL Azure.
- Fonctionnalités qui reposent sur le lecteur de journal exécuté dans une base de données SQL : réplication par émission, capture de données modifiées.
- Fonctionnalités qui reposent sur l’Agent SQL Server ou la base de données MSDB : travaux, alertes et les opérateurs, gestion basée sur la stratégie, messagerie de la base de données, serveurs d’administration centrale.
- FILESTREAM
- Fonctions : fn\_get\_sql fn\_virtualfilestats, fn\_virtualservernodes
- Tables globales temporaires
- Paramètres du serveur relatifs au matériel : mémoire, threads de travail, affinité du processeur, indicateurs de trace, etc. Utilisez plutôt les niveaux de service.
- HAS\_DBACCESS
- KILL STATS JOB
- Serveurs liés, OPENQUERY, OPENROWSET, OPENDATASOURCE, BULK INSERT, nom à quatre parties
- Serveurs maître/cible
- [Intégration du CLR avec SQL Server](http://msdn.microsoft.com/library/ms254963.aspx) .NET Framework
- Gouverneur de ressources
- Recherche sémantique
- Informations d’identification du serveur. Utilisez à la place les informations d’identification de niveau base de données.
- Éléments de niveau serveur : rôles du serveur, sys.login\_token IS\_SRVROLEMEMBER. Les autorisations de niveau serveur ne sont pas disponibles, bien que certaines soient remplacées par des autorisations de niveau base de données. Certaines DMV de niveau serveur ne sont pas disponibles, bien que certaines soient remplacées par des DMV de niveau base de données.
- Express sans serveur : localdb, instances utilisateur
- Service broker
- SET REMOTE\_PROC\_TRANSACTIONS
- SHUTDOWN
- sp\_addmessage
- Options sp\_configure et RECONFIGURE. Certaines options sont disponibles à l’aide [d’ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx).
- sp\_helpuser
- sp\_migrate\_user\_to\_contained
- SQL Server Audit. Utilisez l’audit de base de données SQL Azure à la place.
- Générateur de profils SQL Server
- SQL Server trace
- Indicateurs de trace. Certains éléments d’indicateur de trace ont été déplacés vers les modes de compatibilité.
- Débogage Transact-SQL
- Déclencheurs : déclencheurs au niveau du serveur ou de connexion
- Instruction USE : pour basculer le contexte de base de données vers une autre base de données, vous devez établir une nouvelle connexion à la nouvelle base de données.


## Référence complète Transact-SQL

Pour plus d'informations sur la grammaire, l'utilisation et les exemples Transact-SQL, consultez [Référence Transact-SQL (moteur de la base de données)](https://msdn.microsoft.com/library/bb510741.aspx) dans la documentation en ligne de SQL Server.

### À propos des balises « S’applique à »

La référence sur Transact-SQL comprend des rubriques relatives aux versions de SQL Server de 2008 jusqu'à présent. Sous le titre de la rubrique se trouve une barre d’icônes qui répertorie les quatre plateformes SQL Server et indique l’applicabilité. Par exemple, la fonction des groupes de disponibilité ont été introduits dans SQL Server 2012. La rubrique [CREATE AVAILABILTY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) indique que les instructions s’appliquent à** SQL Server (à partir de 2012). L’instruction ne s’applique pas à SQL Server 2008, SQL Server 2008 R2, à la base de données SQL Azure, à Azure SQL Data Warehouse ou à Parallel Data Warehouse.

Dans certains cas, le sujet général d'une rubrique peut être utilisé dans un produit, mais il existe des différences mineures entre les produits. Les différences sont indiquées dans la rubrique comme il convient.

<!---HONumber=AcomDC_0831_2016-->