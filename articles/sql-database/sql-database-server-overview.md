---
title: "Vue d’ensemble du serveur logique Azure SQL Database | Microsoft Docs"
description: "Cette page comporte des considérations et instructions relatives à l’utilisation des serveurs logiques SQL Azure."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: servers
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: 17e2830dceeaa313dd0fd7d406bf68a75b6f900e


---
# <a name="azure-sql-database-logical-servers"></a>Serveurs logiques Azure SQL Database

Cette rubrique comporte des considérations et instructions relatives à l’utilisation des serveurs logiques SQL Azure. Pour plus d’informations sur les bases de données SQL Azure, consultez [Bases de données SQL](sql-database-overview.md).

## <a name="what-is-an-azure-sql-database-logical-server"></a>Qu’est-ce qu’un serveur logique Azure SQL Database ?
Un serveur logique Azure SQL Database agit comme un point d’administration central pour plusieurs bases de données. Dans SQL Database, un serveur est une construction logique distincte d’une instance SQL Server que vous connaissez peut-être dans le monde local. Plus précisément, le service SQL Database n’offre aucune garantie en ce qui concerne l’emplacement des bases de données par rapport à leurs serveurs logiques et ne présente aucun accès ni aucune fonctionnalité au niveau de l’instance. Pour plus d’informations sur les serveurs logiques SQL Azure, consultez [Serveurs logiques](sql-database-server-overview.md). 

Un serveur logique de base de données Azure :

- Est créé dans un abonnement Azure, mais peut être déplacé avec les ressources qu’il contient vers un autre abonnement
- Est la ressource parente pour les bases de données, les pools élastiques et les entrepôts de données
- Est la ressource parente pour les bases de données, les pools élastiques et les entrepôts de données
- Est un conteneur logique avec une sémantique à durée de vie longue. La suppression d’un serveur entraîne la suppression des bases de données, des pools élastiques et des entrepôts de données qu’il contient
- Fait partie d’un contrôle d’accès en fonction du rôle Azure (RBAC) ; les bases de données, les pools élastiques d’un serveur héritent des droits d’accès du serveur
- Est un élément de poids dans l’identité des bases de données et des pools élastiques à des fins de gestion des ressources (voir le schéma d’URL pour les bases de données et les pools)
- Colocalise les ressources d’une région
- Fournit un point de terminaison de connexion pour l’accès aux bases de données (<serverName>. database.windows.net)
- Fournit l’accès aux métadonnées concernant l’accès aux ressources contenues via les DMV en vous connectant à une base de données MASTER 
- Fournit l’étendue des stratégies de gestion qui s’appliquent à ses bases de données : connexions, pare-feu, audit, détection des menaces, etc.. 
- Est limité par un quota dans l’abonnement parent (six serveurs par abonnement - [voir les limites d’abonnement ici](../azure-subscription-service-limits.md))
- Fournit l’étendue du quota de base de données et du quota DTU pour les ressources qu’il contient (par exemple, 45 000 DTU dans la V12)
- Est la portée du contrôle de version pour les fonctionnalités activées sur les ressources qu’il contient (la version la plus récente est V12)
- Les connexions principales au niveau du serveur peuvent gérer toutes les bases de données sur un serveur
- Peut contenir des connexions semblables à celles des instances de SQL Server en local qui ont accès à une ou plusieurs bases de données sur le serveur et qui peuvent se voir octroyer des droits d’administration limités. Pour plus d’informations, consultez [Connexions](sql-database-manage-logins.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-sql-database-logical-server"></a>Comment se connecter et s’authentifier auprès d’un serveur logique Azure SQL Database ?

- **Authentification et autorisation** : Azure SQL Database prend en charge l’authentification SQL et Azure Active Directory (avec certaines restrictions - voir [Connect to SQL Database with Azure Active Directory Authentication](sql-database-aad-authentication.md) (Se connecter à la base de données SQL avec l’authentification Azure Active Directory) pour l’authentification. Vous pouvez vous connecter aux bases de données SQL Azure et vous authentifier sur celles-ci par le biais de la base de données MASTER du serveur ou directement sur une base de données utilisateur. Pour en savoir plus, voir [Gestion des bases de données et des connexions dans la base de données SQL Microsoft Azure](sql-database-manage-logins.md). L’authentification Windows n’est pas prise en charge. 
- **TDS** : la base de données SQL Microsoft Azure prend en charge les versions 7.3 et ultérieures du client de protocole TDS (Tabular Data Stream).
- **TCP/IP**  : seules les connexions TCP/IP sont autorisées.
- **Pare-feu SQL Database** : pour aider à protéger vos données, le pare-feu SQL Database empêche tout accès à votre serveur de base de données jusqu’à ce que vous spécifiiez les ordinateurs qui disposent d’autorisations. Voir [Pare-feux](sql-database-firewall-configure.md)

## <a name="what-collations-are-supported"></a>Quels classements sont pris en charge ?

Le classement par défaut utilisé par la base de données SQL Microsoft Azure (y compris la base de données MASTER) est **SQL_LATIN1_GENERAL_CP1_CI_AS**, où **LATIN1_GENERAL** correspond à l’anglais (États-Unis) et **CP1** à la page de code 1252. La propriété **CI** indique que le classement n’est pas sensible à la casse, et **AS** qu’il respecte les accents. Il est déconseillé de modifier le classement des bases de données V12 après sa création. Pour plus d’informations sur les classements, consultez [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## <a name="what-are-the-naming-requirements-for-database-objects"></a>Quelles sont les exigences en matière d’affectation de noms pour les objets de base de données ?

Les noms de tous les nouveaux objets doivent être conformes aux règles des identificateurs SQL Server. Pour plus d'informations, consultez [Identificateurs](https://msdn.microsoft.com/library/ms175874.aspx).

## <a name="what-features-are-supported"></a>Quelles fonctionnalités sont prises en charge ?

Pour plus d’informations sur les fonctionnalités prises en charge, consultez la page [Fonctionnalités](sql-database-features.md). Voir aussi [Différences dans le langage Transact-SQL pour Azure SQL Database](sql-database-transact-sql-information.md) pour plus d’informations sur les raisons du manque de prise en charge de certains types de fonctionnalités.

## <a name="how-do-i-manage-a-logical-server"></a>Comment gérer un serveur logique ?

Vous pouvez gérer les serveurs logiques Azure SQL Database à l’aide de plusieurs méthodes :
- [Portail Azure](sql-database-manage-portal.md)
- [PowerShell](sql-database-manage-powershell.md)
- [REST](/rest/api/sql/)

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le service Azure SQL Database, consultez [What is SQL Database?](sql-database-technical-overview.md) (Qu’est-ce qu’une base de données ?)
- Pour plus d’informations sur les fonctionnalités prises en charge, consultez la page [Fonctionnalités](sql-database-features.md).
- Consultez la page [Vue d’ensemble des bases de données SQL](sql-database-overview.md) pour obtenir une vue d’ensemble des bases de données SQL Azure.
- Pour plus d’informations sur la prise en charge de Transact-SQL et les différences, consultez la page [Différences dans le langage Transact-SQL pour Azure SQL Database](sql-database-transact-sql-information.md).
- Pour plus d’informations sur les quotas de ressources spécifiques et les limitations basées sur votre **niveau de service**. Pour obtenir une présentation des niveaux de service, consultez [Niveaux de service de Base de données SQL](sql-database-service-tiers.md).
- Pour une vue d’ensemble de la sécurité, consultez [Azure SQL Database Security Overview (Vue d’ensemble de la sécurité de Azure SQL Database)](sql-database-security-overview.md).
- Pour plus d'informations sur la disponibilité des pilotes et sur la prise en charge de la base de données SQL, consultez [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md).




<!--HONumber=Dec16_HO4-->


