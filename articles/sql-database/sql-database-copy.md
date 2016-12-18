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
ms.devlang: NA
ms.date: 10/24/2016
ms.author: sstein; sashan
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3e6152f6539962653a582ba4921af82e4447fd76


---
# <a name="copy-an-azure-sql-database"></a>Copie d'une base de données SQL Azure
> [!div class="op_single_selector"]
> * [Vue d'ensemble](sql-database-copy.md)
> * [portail Azure](sql-database-copy-portal.md)
> * [PowerShell](sql-database-copy-powershell.md)
> * [T-SQL](sql-database-copy-transact-sql.md)
> 
> 

Vous pouvez utiliser les [sauvegardes automatisées de base de données SQL](sql-database-automated-backups.md) Azure pour créer une copie de votre base de données SQL. La copie de la base de données utilise la même technologie que la fonctionnalité de géoréplication. Mais contrairement à la géoréplication, elle met fin au lien de réplication dès la fin de la phase d’amorçage. Ainsi, la base de données de copie est un instantané de la base de données source au moment de la demande de la copie.  
Vous pouvez créer la copie de la base de données sur le même serveur ou sur un autre serveur. Le niveau de service et le niveau de performances (niveau tarifaire) de la copie de base de données sont, par défaut, les mêmes que ceux de la base de données source. Quand vous utilisez l’API, vous pouvez sélectionner un autre niveau de performances dans le même niveau de service (édition). Une fois la copie terminée, la copie devient une base de données indépendante et entièrement fonctionnelle. À ce stade, vous pouvez la mettre à niveau ou la rétrograder vers n’importe quelle édition. Les connexions, les utilisateurs et les autorisations peuvent être gérés indépendamment.  

Lorsque vous copiez une base de données sur le même serveur logique, les mêmes connexions peuvent être utilisées sur les deux bases de données. L'élément principal de sécurité que vous utilisez pour copier la base de données devient le propriétaire de la base de données (DBO) sur la nouvelle base de données. Tous les utilisateurs de la base de données, leurs autorisations et leurs identificateurs de sécurité (SID) sont copiés vers la copie de base de données.  

Lorsque vous copiez une base de données vers un autre serveur logique, le principal de sécurité sur le nouveau serveur devient le propriétaire de la base de données sur la nouvelle base de données. Si vous utilisez des [utilisateurs de base de données à relation contenant-contenu](sql-database-manage-logins.md) pour accéder aux données, vérifiez que les bases de données primaire et secondaire ont toujours les mêmes informations d’identification utilisateur afin de pouvoir y accéder immédiatement avec les mêmes informations d’identification, une fois la copie terminée. Si vous utilisez [Azure Active Directory](../active-directory/active-directory-whatis.md), vous n’avez plus du tout besoin de gérer les informations d’identification dans la copie. Toutefois, lorsque vous copiez la base de données sur un nouveau serveur, l’accès par connexion échoue généralement, car ces identifiants n’existent pas sur le nouveau serveur. Consultez la page [Gestion de la sécurité d’une base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md) pour en savoir plus sur la gestion des connexions durant la copie d’une base de données vers un autre serveur logique. 

Pour copier une base de données SQL, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous avez besoin d’un abonnement Azure, cliquez simplement sur **VERSION D’ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
* Une base de données SQL à copier. Si vous n’avez pas de base de données SQL, créez-en une en procédant de la manière décrite dans [Créer votre première base de données SQL Azure](sql-database-get-started.md).

## <a name="next-steps"></a>Étapes suivantes
* Consultez [Copier une base de données SQL Azure à l’aide du Portail Azure](sql-database-copy-portal.md) pour copier une base de données à l’aide du Portail Azure.
* Consultez la page [Copier une base de données SQL Azure à l’aide de PowerShell](sql-database-copy-powershell.md) pour copier une base de données à l’aide de PowerShell.
* Consultez la page [Copier une base de données SQL Azure à l’aide de T-SQL](sql-database-copy-transact-sql.md) pour copier une base de données à l’aide de Transact-SQL.
* Consultez la page [Gestion de la sécurité d’une base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md) pour en savoir plus sur la gestion des utilisateurs et des connexions lors de la copie d’une base de données vers un autre serveur logique.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Gérer les connexions](sql-database-manage-logins.md)
* [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)
* [Exporter la base de données vers un fichier BACPAC](sql-database-export.md)
* [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
* [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)




<!--HONumber=Nov16_HO3-->


