---
title: "Concepts de serveur dans une base de données Azure pour PostgreSQL | Microsoft Docs"
description: "Cette rubrique propose des considérations et des instructions relatives à l’utilisation des serveurs de base de données Azure pour PostgreSQL."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e14712b8fd68d6364f44c116448a9a8f33622a91
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---
# <a name="azure-database-for-postgresql-servers"></a>Serveurs de base de données Azure pour PostgreSQL

Cette rubrique propose des considérations et des instructions relatives à l’utilisation des serveurs de base de données Azure pour PostgreSQL.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Qu’est-ce qu’un serveur de base de données Azure pour PostgreSQL ?

Un serveur de base de données Azure pour PostgreSQL est un point d’administration central pour plusieurs bases de données. Il s’agit de la structure de serveur PostgreSQL que vous connaissez peut-être en local. Plus précisément, le service PostgreSQL est géré, fournit des garanties de performances et propose des fonctionnalités et des accès au niveau du serveur.

Un serveur de base de données Azure pour PostgreSQL :

- est créé dans un abonnement Azure ;
- représente la ressource parente des bases de données ;
- fournit un espace de noms aux bases de données ;
- constitue un conteneur avec une sémantique à durée de vie longue (la suppression d’un serveur entraîne la suppression des bases de données qu’il contient) ;
- colocalise les ressources d’une région ;
- fournit un point de terminaison de connexion pour l’accès au serveur et aux bases de données (.postgresql.database.azure.com) ;
- fournit l’étendue des stratégies de gestion qui s’appliquent à ses bases de données : connexions, pare-feu, utilisateurs, rôles, configurations, etc. ;
- est disponible dans plusieurs versions (pour plus d’informations, consultez la page [Versions prises en charge des bases de données PostgreSQL](concepts-supported-versions.md)) ;
- peut être étendu par les utilisateurs (pour plus d’informations, consultez la page [Extensions de PostgreSQL](concepts-extensions.md)).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Comment se connecter et s’authentifier auprès d’un serveur de base de données Azure pour PostgreSQL ?

Les éléments suivants permettent de garantir un accès sécurisé à votre base de données.

|||
| :-- | :-- |
| **Authentification et autorisation** | Le serveur de base de données Azure pour PostgreSQL prend en charge l’authentification PostgreSQL native. Vous pouvez vous connecter et vous authentifier auprès du serveur avec les informations de connexion d’administrateur du serveur.<br />Pour plus d’informations, consultez la page [Gestion des utilisateurs et des rôles dans la base de données Azure pour PostgreSQL](/azure/sql-database/sql-database-manage-logins). |
| **Protocole** | Le service prend en charge un protocole par messages utilisé par PostgreSQL. |
| **TCP/IP** | Le protocole est pris en charge via TCP/IP et des sockets du domaine Unix. |
| **Pare-feu** | Pour aider à protéger vos données, une règle de pare-feu empêche tout accès à votre serveur de base de données ou à ses bases de données jusqu’à ce que vous spécifiiez les ordinateurs qui disposent d’autorisations. Consultez la page [Règles de pare-feu d’un serveur de base de données Azure pour PostgreSQL](concepts-firewall-rules.md). |
|||

## <a name="how-do-i-manage-a-server"></a>Comment gérer un serveur ?

Vous pouvez gérer des serveurs de bases de données Azure pour PostgreSQL à l’aide du Portail Azure ou d’Azure CLI.

## <a name="next-steps"></a>Étapes suivantes

- Vous trouverez une vue d’ensemble du service à la page [Vue d’ensemble de la base de données Azure pour PostgreSQL](overview.md).
- Pour plus d’informations sur les quotas de ressources et les limitations associés à votre **niveau de service**, consultez la page [Niveaux de service](concepts-service-tiers.md).
- Pour plus d’informations sur la connexion au service, consultez la page [Bibliothèques de connexions de la base de données Azure pour PostgreSQL](concepts-connection-libraries.md).

