---
title: "Vue d’ensemble de la base de données SQL Azure | Microsoft Docs"
description: "Cette page fournit une vue d’ensemble des bases de données SQL Azure."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/28/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: 315fb49ba25c46afaa6571d9249ecd1c8da13e91


---
# <a name="azure-sql-database-overview"></a>Vue d’ensemble de la base de données SQL Azure
Cette rubrique fournit une vue d’ensemble des bases de données SQL Azure. Pour plus d’informations sur les serveurs logiques SQL Azure, consultez [Serveurs logiques](sql-database-server-overview.md).

## <a name="what-is-azure-sql-database"></a>Qu’est-ce que la base de données SQL Azure ?
Chaque base de données d’Azure SQL Database est associée à un serveur logique. La base de données peut être :

- Une base de données unique avec son [propre ensemble de ressources](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus) (DTU)
- Une partie d’un [pool élastique](sql-database-elastic-pool.md) qui [partage un ensemble de ressources](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus) (eDTU)
- Une partie d’un [ensemble de bases de données partitionnées dont la taille des instances a été augmentée](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling). Il peut s’agir de bases de données uniques ou mises en pool
- Une partie d’un ensemble de bases de données participant à un [modèle de conception SaaS partagé au sein d’une architecture mutualisée ](sql-database-design-patterns-multi-tenancy-saas-applications.md), et dont les bases de données peuvent être uniques ou mises en pool (ou les deux) 

## <a name="how-do-i-connect-and-authenticate-to-an-azure-sql-database"></a>Comment se connecter et s’authentifier auprès d’une base de données SQL Azure ?

- **Authentification et autorisation** : Azure SQL Database prend en charge l’authentification SQL et Azure Active Directory (avec certaines restrictions - voir [Connect to SQL Database with Azure Active Directory Authentication](sql-database-aad-authentication.md) (Se connecter à la base de données SQL avec l’authentification Azure Active Directory) pour l’authentification. Vous pouvez vous connecter aux bases de données SQL Azure et vous authentifier sur celles-ci par le biais de la base de données MASTER du serveur ou directement sur une base de données utilisateur. Pour en savoir plus, voir [Gestion des bases de données et des connexions dans la base de données SQL Microsoft Azure](sql-database-manage-logins.md). L’authentification Windows n’est pas prise en charge. 
- **TDS** : la base de données SQL Microsoft Azure prend en charge les versions 7.3 et ultérieures du client de protocole TDS (Tabular Data Stream).
- **TCP/IP**  : seules les connexions TCP/IP sont autorisées.
- **Pare-feu SQL Database** : pour aider à protéger vos données, le pare-feu SQL Database empêche tout accès à votre serveur de base de données jusqu’à ce que vous spécifiiez les ordinateurs qui disposent d’autorisations. Voir [Pare-feux](sql-database-firewall-configure.md)

## <a name="what-collations-are-supported"></a>Quels classements sont pris en charge ?
Le classement par défaut utilisé par la base de données SQL Microsoft Azure est **SQL_LATIN1_GENERAL_CP1_CI_AS**, où **LATIN1_GENERAL** correspond à l’anglais (États-Unis) et **CP1** à la page de code 1252. La propriété **CI** indique que le classement n’est pas sensible à la casse, et **AS** qu’il respecte les accents. Il est impossible de modifier le classement des bases de données V12. Pour plus d’informations sur la définition du classement, consultez [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## <a name="what-are-the-naming-requirements-for-database-objects"></a>Quelles sont les exigences en matière d’affectation de noms pour les objets de base de données ?

Les noms de tous les nouveaux objets doivent être conformes aux règles des identificateurs SQL Server. Pour plus d'informations, consultez [Identificateurs](https://msdn.microsoft.com/library/ms175874.aspx).

## <a name="what-features-are-supported-by-azure-sql-databases"></a>Quelles fonctionnalités sont prises en charge par les bases de données SQL Azure ?

Pour plus d’informations sur les fonctionnalités prises en charge, consultez la page [Fonctionnalités](sql-database-features.md). Voir aussi [Différences dans le langage Transact-SQL pour Azure SQL Database](sql-database-transact-sql-information.md) pour plus d’informations sur les raisons du manque de prise en charge de certains types de fonctionnalités.

## <a name="how-do-i-manage-an-azure-sql-database"></a>Comment gérer une base de données SQL Azure ?

Vous pouvez gérer les serveurs logiques Azure SQL Database à l’aide de plusieurs méthodes :
- [Portail Azure](sql-database-manage-portal.md)
- [PowerShell](sql-database-manage-powershell.md)
- [Transact-SQL](sql-database-manage-azure-ssms.md)
- [REST](/rest/api/sql/)

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le service Azure SQL Database, consultez [What is SQL Database?](sql-database-technical-overview.md) (Qu’est-ce qu’une base de données ?)
- Pour plus d’informations sur les fonctionnalités prises en charge, consultez la page [Fonctionnalités](sql-database-features.md).
- Pour une vue d’ensemble des serveurs logiques SQL Azure, consultez [SQL Database logical server overview](sql-database-server-overview.md) (Vue d’ensemble du serveur logique SQL Database)
- Pour plus d’informations sur la prise en charge de Transact-SQL et les différences, consultez la page [Différences dans le langage Transact-SQL pour Azure SQL Database](sql-database-transact-sql-information.md).
- Pour plus d’informations sur les quotas de ressources spécifiques et les limitations basées sur votre **niveau de service**. Pour obtenir une présentation des niveaux de service, consultez [Niveaux de service de Base de données SQL](sql-database-service-tiers.md).
- Pour une vue d’ensemble de la sécurité, consultez [Azure SQL Database Security Overview (Vue d’ensemble de la sécurité de Azure SQL Database)](sql-database-security-overview.md).
- Pour plus d'informations sur la disponibilité des pilotes et sur la prise en charge de la base de données SQL, consultez [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md).




<!--HONumber=Dec16_HO4-->


