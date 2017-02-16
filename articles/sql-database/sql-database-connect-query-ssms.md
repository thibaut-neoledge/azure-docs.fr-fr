---
title: "Se connecter à Base de données SQL - SQL Server Management Studio | Microsoft Docs"
description: "Découvrez comment vous connecter à Base de données SQL sur Azure avec SQL Server Management Studio (SSMS). Ensuite, exécutez un exemple de requête à l’aide de Transact-SQL (T-SQL)."
metacanonical: 
keywords: "connexion à une base de données sql, sql server management studio"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7cd2a114-c13c-4ace-9088-97bd9d68de12
ms.service: sql-database
ms.custom: development
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2a85b3dc1078bad9e5e2fc0ce0bec7e994b29150
ms.openlocfilehash: a6b147521525fad343376db0454f786a77b55c42


---
# <a name="connect-to-sql-database-with-sql-server-management-studio-and-execute-a-sample-t-sql-query"></a>Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL
> [!div class="op_single_selector"]
> * [Visual Studio](sql-database-connect-query.md)
> * [SSMS](sql-database-connect-query-ssms.md)
> * [Excel](sql-database-connect-excel.md)
> 

Cet article explique comment se connecter à une base de données SQL Azure avec SQL Server Management Studio (SSMS). Une fois la connexion établie, nous exécutons une requête Transact-SQL (T-SQL) simple pour vérifier la communication avec la base de données.

[!INCLUDE [SSMS Install](../../includes/sql-server-management-studio-install.md)]

1. Si ce n’est déjà fait, téléchargez et installez la dernière version de SSMS via [Téléchargement de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). Pour vous permettre de rester à jour, la dernière version de SSMS vous envoie une invite lorsqu’une nouvelle version est disponible au téléchargement.

2. Une fois l’installation terminée, tapez **Microsoft SQL Server Management Studio** dans la zone de recherche de Windows, puis cliquez sur **Entrée** pour ouvrir SSMS :

    ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)
3. Dans la boîte de dialogue Se connecter au serveur, entrez les informations nécessaires pour vous connecter à votre serveur SQL Server à l’aide de l’authentification SQL Server.

    ![connect to server](./media/sql-database-get-started/connect-to-server.png)
4. Cliquez sur **Connecter**.

    ![connected to server](./media/sql-database-get-started/connected-to-server.png)
5. Dans Explorateur d’objet, développez **Bases de données** et développez une base de données pour afficher les objets de cette base.

    ![new sample db objects with ssms](./media/sql-database-get-started/new-sample-db-objects-ssms.png)
6. Cliquez avec le bouton droit sur cette base de données, puis cliquez sur **Nouvelle requête**.

    ![new sample db query with ssms](./media/sql-database-get-started/new-sample-db-query-ssms.png)
7. Dans la fenêtre de requête, saisissez la requête suivante :

   ```select * from sys.objects```
   
8.  Dans la barre d’outils, cliquez sur **Exécuter** pour retourner une liste de tous les objets système dans l’exemple de base de données.

    ![new sample db query system objects with ssms](./media/sql-database-get-started/new-sample-db-query-objects-ssms.png)

> [!Tip]
> Pour trouver un didacticiel, consultez [Didacticiel sur la base de données SQL : création d’un serveur, d’une règle de pare-feu de niveau serveur, d’un exemple de base de données, d’une règle de pare-feu de niveau base de données et connexion à SQL Server](sql-database-get-started.md).    
>

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez utiliser des instructions T-SQL pour créer et gérer des bases de données dans Azure de la même façon que vous pouvez le faire avec SQL Server. Si vous êtes familiarisé avec l’utilisation de T-SQL avec SQL Server, consultez [Informations sur Transact-SQL avec Azure SQL Database](sql-database-transact-sql-information.md) pour obtenir un récapitulatif des différences.
- Si vous débutez avec T-SQL, consultez [Didacticiel : Écriture d’instructions Transact-SQL](https://msdn.microsoft.com/library/ms365303.aspx) et [Informations de référence sur Transact-SQL (moteur de base de données)](https://msdn.microsoft.com/library/bb510741.aspx).
- Pour commencer à créer des utilisateurs de base de données et des administrateurs d’utilisateurs de base de données, consultez la rubrique [Didacticiel sur la base de données SQL : Créer des comptes d’utilisateurs de base de données SQL pour accéder à et gérer une base de données à l’aide du portail Azure](sql-database-control-access-sql-authentication-get-started.md)
- Pour plus d’informations sur SSMS, consultez [Utiliser SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).




<!--HONumber=Jan17_HO3-->


