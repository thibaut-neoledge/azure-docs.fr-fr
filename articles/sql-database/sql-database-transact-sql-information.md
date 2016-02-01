<properties
   pageTitle="Non pris en charge dans Azure SQL Database T-SQL | Microsoft Azure"
   description="Instructions Transact-SQL qui ne sont pas entièrement prises en charge dans Azure SQL Database"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="01/15/2016"
   ms.author="rick.byham@microsoft.com"/>

# Différences dans le langage Transact-SQL Azure SQL Database.


La plupart des fonctionnalités Transact-SQL que les applications utilisent sont prises en charge dans Microsoft SQL Server et Azure SQL Database. Vous trouverez ci-dessous une liste partielle des fonctionnalités prises en charge pour les applications :

- Types de données.
- Opérateurs.
- Fonctions de chaîne, arithmétiques, logiques et de curseur.

Toutefois, Azure SQL Database est conçu pour isoler les fonctionnalités de toute dépendance sur la base de données **maître**. Par conséquent, de nombreuses activités au niveau du serveur ne correspondent pas à la base de données SQL et ne sont pas prises en charge. Cette rubrique décrit en détail les fonctionnalités qui ne sont pas entièrement prises en charge dans la base de données SQL.

De plus, les fonctionnalités déconseillées dans SQL Server ne sont généralement pas prises en charge dans la base de données SQL.

## Mise à niveau vers la version 12 de la base de données SQL

Cette rubrique traite des fonctionnalités qui sont disponibles avec la base de données SQL lors de la mise à niveau gratuite vers la V12 de la base de données SQL. Pour plus d'informations sur la V12, consultez [Nouveautés de la base de données SQL V12](sql-database-v12-whats-new.md). La V12 de la base de données SQL ajoute des améliorations de performances et de gestion, ainsi que de prise en charge des fonctionnalités supplémentaires. Les fonctionnalités ajoutées sont répertoriées ci-dessous, avec d’une part les fonctionnalités qui sont entièrement prises en charge et d’autre part les fonctionnalités partiellement prises en charge.

## Fonctionnalités partiellement prises en charge par la V12 de la base de données SQL

La V12 de la base de données SQL prend en charge certains arguments qui existent dans les instructions relatives au langage Transact-SQL pour SQL Server 2016. Par exemple, l’instruction CREATE PROCEDURE est disponible, cependant l’option WITH ENCRYPTION de CREATE PROCEDURE n’est pas disponible. Reportez-vous aux rubriques de syntaxe liées pour plus d’informations sur la prise en charge des fonctionnalités de chaque instruction.

- Bases de données : [CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER](https://msdn.microsoft.com/library/ms174269.aspx)
- Les DMV sont généralement disponibles pour les fonctionnalités disponibles
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
- Connexions liées  : instructions de point de terminaison, ORIGINAL\_DB\_NAME. L’authentification Windows n’est pas disponible pour les connexions ou les utilisateurs de la base de données contenant-contenu.
- Requêtes dans plusieurs bases de données, la propriété de plusieurs bases de données, paramètre TRUSTWORTHY
- Classement des données
- Schémas de base de données
- Messagerie de base de données
- DATABASEPROPERTY (utilisez plutôt DATABASEPROPERTYEX)
- Connexions EXECUTE AS
- Chiffrement : gestion extensible de clés
- Gestion des événements : événements, notifications d’événements, notifications de requête
- Fonctionnalités liées à l’emplacement du fichier de la base de données, taille et fichiers de la base de données qui sont gérés automatiquement par Microsoft Azure.
- Fonctionnalités à haute disponibilité qui est gérée via votre compte Microsoft Azure : sauvegarde, restauration, AlwaysOn, mise en miroir de la base de données, envoi de journaux, modes de récupération. Pour en savoir plus, consultez Sauvegarde et restauration de la base de données SQL Azure.
- Fonctionnalités qui reposent sur le lecteur de journal : réplication, capture de données modifiées.
- Fonctionnalités qui reposent sur l’Agent SQL Server ou la base de données MSDB : travaux, alertes et les opérateurs, gestion basée sur la stratégie, messagerie de la base de données, serveurs d’administration centrale.
- FILESTREAM
- Fonctions : fn\_get\_sql fn\_virtualfilestats, fn\_virtualservernodes
- Tables globales temporaires
- Matériel relatif aux paramètres du serveur : mémoire, threads de travail, affinité du processeur, indicateurs de trace, etc. Utilisez plutôt les niveaux de service.
- HAS\_DBACCESS
- KILL STATS JOB
- Serveurs liés, OPENQUERY, OPENROWSET, OPENDATASOURCE, BULK INSERT, nom à 3 ou 4 parties
- Serveurs maître/cible
- [Intégration du CLR avec SQL Server](http://msdn.microsoft.com/library/ms254963.aspx) .NET Framework
- Gouverneur de ressources
- Recherche sémantique
- Informations d’identification du serveur
- Éléments de niveau serveur : rôles du serveur, sys.login\_token IS\_SRVROLEMEMBER. Les autorisations de niveau serveur ne sont pas disponibles, bien que certaines soient remplacées par des autorisations de niveau base de données. Certaines DMV de niveau serveur ne sont pas disponibles, bien que certaines soient remplacées par des DMV de niveau base de données.
- Express sans serveur : localdb, instances utilisateur
- Service broker
- SET REMOTE\_PROC\_TRANSACTIONS
- SHUTDOWN
- sp\_addmessage
- sp\_configureoptions et RECONFIGURE
- sp\_helpuser
- sp\_migrate\_user\_to\_contained
- Audit SQL Server (utilisez plutôt l’audit de la base de données SQL)
- Générateur de profils SQL Server
- SQL Server trace
- Indicateurs de trace
- Débogage Transact-SQL
- Déclencheurs : déclencheurs au niveau du serveur ou de connexion
- Instruction USE

## Référence complète Transact-SQL

Pour plus d'informations sur la grammaire, l'utilisation et les exemples Transact-SQL, consultez [Référence Transact-SQL (moteur de la base de données)](https://msdn.microsoft.com/library/bb510741.aspx) dans la documentation en ligne de SQL Server.

### À propos des balises « S’applique à »

La référence sur Transact-SQL comprend des rubriques relatives aux versions de SQL Server allant de 2008 jusqu'à présent. Sous le titre de la rubrique, il existe généralement une ligne « S'applique à » qui répertorie les versions de SQL Server et éventuellement d'autres noms de produits. Souvent les mêmes balises « S'applique à » répertorient également Azure SQL Database. Si une balise « S'applique à » ne répertorie pas Azure SQL Database, le contenu de la rubrique ne s'applique pas à Azure SQL Database. Parfois, une ligne « S'applique à » qui répertorie plusieurs produits s'affiche, chacune avec une petite icône pour indiquer si la rubrique s'applique à chaque produit.

 Par exemple, la fonction des groupes de disponibilité ont été introduits dans SQL Server 2012. La rubrique **CREATE AVAILABILTY GROUP** indique qu'ils sont appliqués à **SQL Server (de SQL Server 2012 à la version actuelle)**, car ils ne sont pas appliqués à SQL Server 2008, SQL Server 2008 R2 ou à Azure SQL Database.

Dans certains cas, le sujet général d'une rubrique peut être utilisé dans un produit, mais il existe des différences mineures entre les produits. Les différences sont indiquées dans la rubrique comme il convient.

<!---HONumber=AcomDC_0121_2016-->