---
title: T-SQL non pris en charge dans Azure SQL Database | Microsoft Docs
description: "Instructions Transact-SQL qui ne sont pas entièrement prises en charge dans Azure SQL Database"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: c05abd9e-28a7-4c97-9bdf-bc60d08fc92e
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/22/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: a08d9f2ef29002f10473b0e041737c9c607f3ca0
ms.openlocfilehash: 7d6de93c99141248ea970ea668fb0b2191267b62
ms.lasthandoff: 03/01/2017


---
# <a name="azure-sql-database-transact-sql-differences"></a>Différences dans le langage Transact-SQL Azure SQL Database.   
La plupart des fonctionnalités Transact-SQL que les applications utilisent sont prises en charge dans Microsoft SQL Server et Azure SQL Database. Par exemple, les principaux composants SQL tels que les types de données, les opérateurs ainsi que les fonctions de chaîne, arithmétiques, logiques, de curseur, etc., fonctionnent comme dans SQL Server.

# <a name="why-some-transact-sql-is-not-supported"></a>Pourquoi certaines fonctionnalités Transact-SQL ne sont-elles pas prises en charge ?
Azure SQL Database est conçu pour isoler les fonctionnalités des dépendances sur la base de données Master et le système d’exploitation. Par conséquent, de nombreuses activités au niveau du serveur sont inappropriées pour SQL Database. Des instructions Transact-SQL sont généralement indisponibles si elles configurent des options au niveau du serveur et des composants du système d’exploitation, ou spécifient la configuration du système de fichiers. Lorsque des fonctionnalités extérieures à la base de données utilisateur sont nécessaires, une alternative appropriée est souvent disponible en procédant différemment à partir de SQL Database ou d’un autre service ou fonctionnalité Azure. 

Par exemple, la fonctionnalité Toujours actif ne s’applique pas à Azure SQL Database, puisque la haute disponibilité est conçue dans le service : chaque base de données est hautement disponible. Pour cette raison, aucune des instructions Transact-SQL relatives aux groupes de disponibilité n’est prise en charge par SQL Database, et les vues de gestion dynamique liées à Always On ne sont pas prises en charge.  

Pour obtenir la liste des fonctionnalités prises en charge et non prises en charge par SQL Database, voir [Considérations, directives et fonctionnalités relatives à Azure SQL Database](sql-database-features.md).


## <a name="transact-sql-syntax-partially-supported-in-sql-database"></a>Syntaxe Transact-SQL partiellement prises en charge dans SQL Database
SQL Database prend en charge certains arguments qui existent dans les instructions Transact-SQL correspondantes pour SQL Server 2016 afin de gérer les bases de données et les connexions. Par exemple, l’instruction `CREATE DATABASE` est disponible dans Azure SQL Database, mais toutes les options prises en charge dans SQL Server ne le sont pas dans Azure SQL Database et vice versa. Reportez-vous aux rubriques sur la syntaxe liées pour plus d’informations sur la prise en charge des fonctionnalités de chaque instruction.

- Bases de données : [CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER DATABASE](https://msdn.microsoft.com/library/mt574871.aspx)   
- Connexions : [CREATE](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)   

## <a name="transact-sql-syntax-not-supported-in-sql-database"></a>Syntaxe Transact-SQL non prise en charge dans SQL Database   
En plus des instructions Transact-SQL liées aux fonctionnalités non prises en charge décrites dans [Considérations, directives et fonctionnalités relatives à Azure SQL Database](sql-database-features.md), les instructions et groupes d’instructions suivants ne sont pas pris en charge.
- Classement des objets système
- Connexions associées : instructions de point de terminaison, `ORIGINAL_DB_NAME`. La Base de données SQL ne prend pas en charge l’authentification Windows, mais elle prend en charge l’authentification Azure Active Directory similaire. Certains types d’authentification nécessitent la version la plus récente de SSMS. Pour plus d’informations, voir [Connexion au service Base de données SQL ou SQL Data Warehouse avec l’authentification Azure Active Directory](sql-database-aad-authentication.md).
- Requêtes dans plusieurs bases de données à l’aide de noms à trois ou quatre parties. (Les requêtes sur plusieurs bases de données en lecture seule sont prises en charge à l’aide d’une [requête de base de données élastique](sql-database-elastic-query-overview.md).)
- Chaînage d’appartenance entre plusieurs bases de données, paramètre `TRUSTWORTHY`
- `DATABASEPROPERTY` Utilisez `DATABASEPROPERTYEX` à la place.
- `EXECUTE AS LOGIN` Utilisez « EXECUTE AS USER » à la place.
- Le chiffrement est pris en charge, à l’exception de la gestion de clés extensible.
- Gestion des événements : événements, notifications d’événements, notifications de requête.
- Syntaxe liée à l’emplacement du fichier de la base de données, à la taille et aux fichiers de la base de données qui sont gérés automatiquement par Microsoft Azure.
- Syntaxe liée à haute disponibilité, qui est gérée via votre compte Microsoft Azure. Cela inclut la syntaxe pour la sauvegarde, la restauration, la fonctionnalité AlwaysOn, la mise en miroir de bases de données, la copie des journaux de transaction et les modes de récupération.
- Syntaxe reposant sur le lecteur de journal, qui n’est pas disponible dans SQL Database : réplication par émission, modification de la capture de données. SQL Database peut être un abonné d’un article de réplication par émission.
- Syntaxe reposant sur SQL Server Agent ou la base de données MSDB : alertes, opérateurs, serveurs d’administration centrale. Utilisez des scripts tels qu’Azure PowerShell à la place.
- Fonctions : `fn_get_sql`, `fn_virtualfilestats`,`fn_virtualservernodes`
- Tables globales temporaires
- Syntaxe pour les paramètres du serveur relatifs au matériel : mémoire, threads de travail, affinité du processeur, indicateurs de trace, etc. Utilisez plutôt les niveaux de service.
- `HAS_DBACCESS`
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE` et noms en quatre parties
- [Intégration du CLR avec SQL Server](http://msdn.microsoft.com/library/ms254963.aspx)
- Recherche sémantique
- Informations d’identification du serveur. Utilisez à la place les informations d’identification de niveau base de données.
- Éléments au niveau du serveur : les rôles de serveur, `IS_SRVROLEMEMBER`, `sys.login_token`, `GRANT`, `REVOKE` et `DENY` des autorisations de niveau serveur ne sont pas disponibles, bien que certains soient remplacés par des autorisations au niveau base de données. Certaines vues de gestion dynamique utiles au niveau du serveur ont des vues de gestion dynamique équivalentes au niveau de la base de données.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- Options `sp_configure` et `RECONFIGURE`. Certaines options sont disponibles à l’aide [d’ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server Audit. Utilisez l’audit de base de données SQL Azure à la place.
- SQL Server trace
- Indicateurs de trace. Certains éléments d’indicateur de trace ont été déplacés vers les modes de compatibilité.
- Débogage Transact-SQL
- Déclencheurs : déclencheurs au niveau du serveur ou de connexion
- Instruction `USE` : pour basculer le contexte de base de données vers une autre base de données, vous devez établir une nouvelle connexion à la nouvelle base de données.

## <a name="full-transact-sql-reference"></a>Référence complète Transact-SQL
Pour plus d'informations sur la grammaire, l'utilisation et les exemples Transact-SQL, consultez [Référence Transact-SQL (moteur de la base de données)](https://msdn.microsoft.com/library/bb510741.aspx) dans la documentation en ligne de SQL Server. 

### <a name="about-the-applies-to-tags"></a>À propos des balises « S’applique à »
La référence sur Transact-SQL comprend des rubriques relatives aux versions de SQL Server de 2008 jusqu'à présent. Sous le titre de la rubrique se trouve une barre d’icônes qui répertorie les quatre plateformes SQL Server et indique l’applicabilité. Par exemple, la fonction des groupes de disponibilité ont été introduits dans SQL Server 2012. La rubrique [CREATE AVAILABILTY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) indique que l’instruction s’applique à **SQL Server (à partir de 2012)**. L’instruction ne s’applique pas à SQL Server 2008, SQL Server 2008 R2, à la base de données SQL Azure, à Azure SQL Data Warehouse ou à Parallel Data Warehouse.

Dans certains cas, le sujet général d'une rubrique peut être utilisé dans un produit, mais il existe des différences mineures entre les produits. Les différences sont indiquées dans la rubrique comme il convient.

