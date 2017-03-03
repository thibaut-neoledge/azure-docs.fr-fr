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
ms.date: 02/01/2017
ms.author: sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 8d988aa55d053d28adcf29aeca749a7b18d56ed4
ms.openlocfilehash: a5eaf43aa01e5d30171ea038db7ba985c9684fb7
ms.lasthandoff: 02/16/2017


---
# <a name="connect-to-sql-database-with-sql-server-management-studio-and-execute-a-sample-t-sql-query"></a>Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL

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
> Pour accéder à un didacticiel, consultez [Didacticiel : Approvisionner une base de données SQL Azure et y accéder à l’aide du portail Azure et de SQL Server Management Studio](sql-database-get-started.md).    
>

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez utiliser des instructions T-SQL pour créer et gérer des bases de données dans Azure de la même façon que vous pouvez le faire avec SQL Server. Si vous êtes familiarisé avec l’utilisation de T-SQL avec SQL Server, consultez [Informations sur Transact-SQL avec Azure SQL Database](sql-database-transact-sql-information.md) pour obtenir un récapitulatif des différences.
- Si vous débutez avec T-SQL, consultez [Didacticiel : Écriture d’instructions Transact-SQL](https://msdn.microsoft.com/library/ms365303.aspx) et [Informations de référence sur Transact-SQL (moteur de base de données)](https://msdn.microsoft.com/library/bb510741.aspx).
- Pour bien démarrer avec l’authentification SQL Server, consultez le didacticiel [Authentification et autorisation SQL](sql-database-control-access-sql-authentication-get-started.md).
- Pour bien démarrer avec l’authentification Azure Active Directory, consultez le didacticiel [Authentification et autorisation Azure AD](sql-database-control-access-aad-authentication-get-started.md).
- Pour plus d’informations sur SSMS, consultez [Utiliser SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).


