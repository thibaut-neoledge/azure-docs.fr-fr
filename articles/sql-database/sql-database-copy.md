---
title: "Copie d’une base de données SQL Azure | Documents Microsoft"
description: "Création d&quot;une copie d&quot;une base de données SQL Azure"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 5aaf6bcd-3839-49b5-8c77-cbdf786e359b
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 10/24/2016
ms.author: sashan
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 0356f3bdbb691b9bde4e906ed30b3b235180f8ba
ms.openlocfilehash: 9851ec0771c4fec4719aec8c8f43f2e3dd2604f0
ms.lasthandoff: 02/27/2017


---
# <a name="copy-an-azure-sql-database"></a>Copie d'une base de données SQL Azure

Vous pouvez créer la copie de la base de données sur le même serveur ou sur un autre serveur. La copie de la base de données de copie est un instantané de la base de données source au moment de la demande de la copie. Le niveau de service et le niveau de performances (niveau tarifaire) de la copie de base de données sont, par défaut, les mêmes que ceux de la base de données source. Quand vous utilisez l’API, vous pouvez sélectionner un autre niveau de performances dans le même niveau de service (édition). Une fois la copie terminée, la copie devient une base de données indépendante et entièrement fonctionnelle. À ce stade, vous pouvez la mettre à niveau ou la rétrograder vers n’importe quelle édition. Les connexions, les utilisateurs et les autorisations peuvent être gérés indépendamment.  

Lorsque vous copiez une base de données sur le même serveur logique, les mêmes connexions peuvent être utilisées sur les deux bases de données. L'élément principal de sécurité que vous utilisez pour copier la base de données devient le propriétaire de la base de données (DBO) sur la nouvelle base de données. Tous les utilisateurs de la base de données, leurs autorisations et leurs identificateurs de sécurité (SID) sont copiés vers la copie de base de données.  

Lorsque vous copiez une base de données vers un autre serveur logique, le principal de sécurité sur le nouveau serveur devient le propriétaire de la base de données sur la nouvelle base de données. Si vous utilisez des [utilisateurs de base de données à relation contenant-contenu](sql-database-manage-logins.md) pour accéder aux données, vérifiez que les bases de données primaire et secondaire ont toujours les mêmes informations d’identification utilisateur afin de pouvoir y accéder immédiatement avec les mêmes informations d’identification, une fois la copie terminée. Si vous utilisez [Azure Active Directory](../active-directory/active-directory-whatis.md), vous n’avez plus du tout besoin de gérer les informations d’identification dans la copie. Toutefois, lorsque vous copiez la base de données sur un nouveau serveur, l’accès par connexion peut ne pas fonctionner, car ces connexions n’existent pas sur le nouveau serveur. Consultez la page [Gestion de la sécurité d’une base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md) pour en savoir plus sur la gestion des connexions durant la copie d’une base de données vers un autre serveur logique. 

Vous pouvez copier une base de données SQL à l’aide du [portail Azure](sql-database-copy-portal.md), de [PowerShell](sql-database-copy-powershell.md) ou de [T-SQL](sql-database-copy-transact-sql.md). 

## <a name="next-steps"></a>Étapes suivantes

* Consultez la page [Gestion de la sécurité d’une base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md) pour en savoir plus sur la gestion des utilisateurs et des connexions lors de la copie d’une base de données vers un autre serveur logique.
* Pour obtenir des informations sur les connexions, consultez [Gérer les connexions](sql-database-manage-logins.md).
* Pour exporter une base de données, consultez [Exporter la base de données vers un fichier BACPAC](sql-database-export.md).

