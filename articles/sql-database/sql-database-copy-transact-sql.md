---
title: "Copier une base de données SQL Azure à l’aide de Transact-SQL | Microsoft Docs"
description: "Création d&quot;une copie d&quot;une base de données SQL Azure à l&quot;aide de Transact-SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 40ea3718-33f8-41af-90cb-3aa15059365e
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 60bcd02d24e2084b9020ce56ef6a9f8268c6b1b5
ms.openlocfilehash: 3ade1f2850b2a67f68e8a4a7f519b7dc7ba1de10


---
# <a name="copy-an-azure-sql-database-using-transact-sql"></a>Copier une base de données SQL Azure à l’aide de Transact-SQL

Les étapes suivantes vous montrent comment copier une base de données SQL avec Transact-SQL sur le même serveur ou un serveur différent. L’opération de copie de la base de données utilise l’instruction [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . 

> [!NOTE]
> Vous pouvez également copier une base de données SQL à l’aide du [portail Azure ](sql-database-copy-portal.md) ou de [PowerShell](sql-database-copy-powershell.md).
>

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous avez besoin d’un abonnement Azure, cliquez simplement sur **VERSION D’ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
* Une base de données SQL Azure. Si vous n’avez pas de base de données SQL, créez-en une en suivant les étapes figurant dans cet article : [Créer votre première base de données SQL Azure](sql-database-get-started.md).
* [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms174173.aspx). Si vous n'avez pas SSMS ou si les fonctionnalités décrites dans cet article ne sont pas disponibles, [téléchargez la dernière version](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="copy-your-sql-database"></a>Copie de votre base de données SQL
Connectez-vous à la base de données maître à l'aide de la connexion principale niveau serveur ou la connexion qui a créé la base de données que vous souhaitez copier. Les connexions autres que la connexion principale niveau serveur doivent être membres du rôle dbmanager afin de copier des bases de données. Pour plus d’informations sur les connexions et la connexion au serveur, consultez la page [Gérer les connexions](sql-database-manage-logins.md).

Démarrez la copie de la base de données source avec l’instruction [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . L'exécution de cette instruction initie le processus de copie de la base de données. Étant donné que la copie d'une base de données est un processus asynchrone, l'instruction CREATE DATABASE est retournée avant la fin de la copie de la base de données.

### <a name="copy-a-sql-database-to-the-same-server"></a>Copie d'une base de données SQL sur le même serveur
Connectez-vous à la base de données maître à l'aide de la connexion principale niveau serveur ou la connexion qui a créé la base de données que vous souhaitez copier. Les connexions autres que la connexion principale niveau serveur doivent être membres du rôle dbmanager afin de copier des bases de données.

Cette commande copie Database1 dans une nouvelle base de données nommée Database2 sur le même serveur. Selon la taille de votre base de données, l'opération de copie peut prendre plus ou moins longtemps.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Copie d'une base de données SQL vers un autre serveur
Connectez-vous à la base de données maître du serveur de destination, le serveur Azure SQL Database dans lequel la nouvelle base de données doit être créée. Utilisez une connexion qui a les mêmes nom et mot de passe que le propriétaire de la base de données (DBO) source sur le serveur Azure SQL Database source. La connexion sur le serveur de destination doit également être membre du rôle dbmanager ou être la connexion principale niveau serveur.

Cette commande copie Database1 sur server1- dans une nouvelle base de données nommée Database2 sur server2. Selon la taille de votre base de données, l'opération de copie peut prendre plus ou moins longtemps.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;


## <a name="monitor-the-progress-of-the-copy-operation"></a>Contrôle de la progression de l'opération de copie
Contrôlez le processus de copie en interrogeant les vues sys.databases et sys.dm_database_copies. Pendant que la copie est en cours, la colonne state_desc de la vue sys.databases pour la nouvelle base de données est définie sur COPYING.

* Si la copie échoue, la colonne state_desc de la vue sys.databases pour la nouvelle base de données est définie sur SUSPECT. Dans ce cas, exécutez l'instruction DROP sur la nouvelle base de données et réessayez ultérieurement.
* Si la copie réussit, la colonne state_desc de la vue sys.databases pour la nouvelle base de données est définie sur ONLINE. Dans ce cas, la copie est terminée et la nouvelle base de données est une base de données normale, qui peut être modifiée indépendamment de la base de données source.

> [!NOTE]
> * Si vous décidez d’annuler la copie pendant qu’elle est en cours, exécutez l’instruction [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) sur la nouvelle base de données. Vous pouvez également exécuter l'instruction DROP DATABASE sur la base de données source pour annuler le processus de copie.
> 
> 

## <a name="resolve-logins-after-the-copy-operation-completes"></a>Résolution des connexions à l’issue de l’opération de copie
Une fois que la nouvelle base de données est en ligne sur le serveur de destination, utilisez l'instruction [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) pour remapper les utilisateurs de la nouvelle base de données avec des connexions sur le serveur de destination. Pour résoudre les problèmes d’utilisateurs orphelins, consultez [Dépannage des utilisateurs orphelins](https://msdn.microsoft.com/library/ms175475.aspx). Vous pouvez également consulter [Gestion de la sécurité de la base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md).

Tous les utilisateurs de la nouvelle base de données conservent les autorisations qu'ils avaient dans la base de données source. L'utilisateur qui a initié la copie de la base de données devient le propriétaire de celle-ci et reçoit un nouvel identificateur de sécurité (SID). Une fois la copie réussie et avant que les autres utilisateurs ne soient remappés, seule la connexion qui a initié la copie, le propriétaire de la base de données (DBO), peuvent se connecter à la nouvelle base de données.

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la gestion des utilisateurs et des connexions lors de la copie d’une base de données sur un autre serveur logique, consultez [Configurer et gérer la sécurité Azure SQL Database pour la géo-restauration ou le basculement](sql-database-geo-replication-security-config.md).
* [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
* [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)



<!--HONumber=Feb17_HO2-->


