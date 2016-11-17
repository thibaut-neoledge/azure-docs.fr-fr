---
title: "Se connecter à Base de données SQL - SQL Server Management Studio | Microsoft Docs"
description: "Découvrez comment vous connecter à Base de données SQL sur Azure avec SQL Server Management Studio (SSMS). Ensuite, exécutez un exemple de requête à l’aide de Transact-SQL (T-SQL)."
metacanonical: 
keywords: "connexion à une base de données sql, sql server management studio"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 7cd2a114-c13c-4ace-9088-97bd9d68de12
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0eb25eb76c6c6c2446ac0b2b07c65975c3719db0


---
# <a name="connect-to-sql-database-with-sql-server-management-studio-and-execute-a-sample-tsql-query"></a>Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL
> [!div class="op_single_selector"]
> * [Visual Studio](sql-database-connect-query.md)
> * [SSMS](sql-database-connect-query-ssms.md)
> * [Excel](sql-database-connect-excel.md)
> 
> 

Cet article explique comment se connecter à une base de données SQL Azure avec SQL Server Management Studio (SSMS). Une fois la connexion établie, nous exécutons une requête Transact-SQL (T-SQL) simple pour vérifier la communication avec la base de données.

[!INCLUDE [SSMS Install](../../includes/sql-server-management-studio-install.md)]

[!INCLUDE [SSMS Connect](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

## <a name="run-sample-queries"></a>Exécuter les exemples de requêtes
Une fois la connexion à votre serveur établie, vous pouvez vous connecter à une base de données et exécuter une requête type. Si vous ne savez pas comment rédiger des requêtes, consultez la page [Écriture d’instructions Transact-SQL](https://msdn.microsoft.com/library/ms365303.aspx).

1. Dans l’**Explorateur d’objets**, sélectionnez une base de données sur le serveur, comme **AdventureWorks**.
2. Cliquez avec le bouton droit sur la base de données et sélectionnez **Nouvelle requête**:
   
    ![Nouvelle requête. Se connecter à un serveur de base de données SQL : SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)
3. Dans la fenêtre de requête, copiez et collez le code suivant :
   
        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;
4. Cliquez sur le bouton **Exécuter** :
   
    ![Vous avez réussi ! Se connecter à un serveur de base de données SQL : SQL Server Management Studio](./media/sql-database-connect-query-ssms/5-success.png)

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez utiliser des instructions T-SQL pour créer et gérer des bases de données dans Azure de la même façon que vous pouvez le faire avec SQL Server. Si vous êtes familiarisé avec l’utilisation de T-SQL avec SQL Server, consultez [Informations sur Transact-SQL avec Azure SQL Database](sql-database-transact-sql-information.md) pour obtenir un récapitulatif des différences.

Si vous débutez avec T-SQL, consultez [Didacticiel : Écriture d’instructions Transact-SQL](https://msdn.microsoft.com/library/ms365303.aspx) et [Informations de référence sur Transact-SQL (moteur de base de données)](https://msdn.microsoft.com/library/bb510741.aspx).

Pour commencer à créer des utilisateurs de base de données et des administrateurs d’utilisateurs de base de données, consultez la rubrique [Didacticiel sur la base de données SQL : Créer des comptes d’utilisateurs de base de données SQL pour accéder à et gérer une base de données à l’aide du portail Azure](sql-database-get-started-security.md)

Pour plus d’informations sur SSMS, consultez [Utiliser SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).




<!---HONumber=Nov16_HO2-->


