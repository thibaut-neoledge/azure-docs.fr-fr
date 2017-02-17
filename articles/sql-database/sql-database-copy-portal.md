---
title: "Copie d’une base de données SQL Azure à l’aide du portail Azure | Microsoft Docs"
description: "Création d&quot;une copie d&quot;une base de données SQL Azure"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: daa6f079-13ed-462f-b346-e201aa61681b
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 09/19/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 867f06c1fae3715ab03ae4a3ff4ec381603e32f7
ms.openlocfilehash: 57bc3c00a1ea79ac69b62965a77debea91c6002e


---
# <a name="copy-an-azure-sql-database-using-the-azure-portal"></a>Copie d’une base de données SQL Azure à l’aide du portail Azure
> [!div class="op_single_selector"]
> * [Vue d'ensemble](sql-database-copy.md)
> * [portail Azure](sql-database-copy-portal.md)
> * [PowerShell](sql-database-copy-powershell.md)
> * [T-SQL](sql-database-copy-transact-sql.md)
> 
> 

Les étapes suivantes vous montrent comment copier une base de données SQL avec le [Portail Azure](https://portal.azure.com) sur le même serveur ou un serveur différent.

Pour copier une base de données SQL, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous avez besoin d’un abonnement Azure, cliquez simplement sur **VERSION D’ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
* Une base de données SQL à copier. Si vous n’avez pas de base de données SQL, créez-en une en suivant les étapes figurant dans cet article : [Créer votre première base de données SQL Azure](sql-database-get-started.md).

## <a name="copy-your-sql-database"></a>Copie de votre base de données SQL
Ouvrez la page de la base de données SQL que vous voulez copier :

1. Accédez au [portail Azure](https://portal.azure.com).
2. Cliquez sur **More services (Plus de services)** > **Bases de données SQL**, puis sur la base de données souhaitée.
3. Sur la page de la base de données SQL, cliquez sur **Copie**:
   
   ![Base de données SQL](./media/sql-database-copy-portal/sql-database-copy.png)
4. Sur la page **Copie** , un nom est fourni par défaut pour la base de données. Tapez un autre nom si vous le souhaitez (toutes les bases de données d’un serveur doivent avoir un nom unique).
5. Sélectionner un **serveur cible**. Le serveur cible est celui où est créée la copie de la base de données. Vous pouvez copier la base de données sur le même serveur ou sur un autre serveur. Vous pouvez créer un serveur ou sélectionner un serveur existant dans la liste. 
6. Une fois le **Serveur cible** sélectionné, les options **Pool de bases de données élastiques** et **Niveau tarifaire** sont activées. Si le serveur dispose d’un pool, vous pouvez y copier la base de données.
7. Cliquez sur **OK** pour démarrer le processus de copie.
   
   ![Base de données SQL](./media/sql-database-copy-portal/copy-page.png)

## <a name="monitor-the-progress-of-the-copy-operation"></a>Contrôle de la progression de l'opération de copie
* Après le démarrage de la copie, cliquez sur la notification du portail pour plus d’informations.
  
    ![notification][3]
  
    ![monitor][4]

## <a name="verify-the-database-is-live-on-the-server"></a>Vérifiez que la base de données est en ligne sur le serveur
* Cliquez sur **More services (Plus de services)** > **Bases de données SQL** et vérifiez que la nouvelle base de données est **En ligne**.

## <a name="resolve-logins"></a>Résolution des connexions
Pour résoudre les connexions à l’issue de l’opération de copie, consultez [Résoudre les connexions](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)

## <a name="next-steps"></a>Étapes suivantes
* Consultez la page [Copie d’une base de données SQL Azure](sql-database-copy.md) pour une vue d’ensemble de la copie d’une base de données SQL Azure.
* Consultez la page [Copier une base de données SQL Azure à l’aide de PowerShell](sql-database-copy-powershell.md) pour copier une base de données à l’aide de PowerShell.
* Consultez la page [Copier une base de données SQL Azure à l’aide de T-SQL](sql-database-copy-transact-sql.md) pour copier une base de données à l’aide de Transact-SQL.
* Consultez la page [Gestion de la sécurité d’une base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md) pour en savoir plus sur la gestion des utilisateurs et des connexions lors de la copie d’une base de données vers un autre serveur logique.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Gérer les connexions](sql-database-manage-logins.md)
* [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)
* [Exporter la base de données vers un fichier BACPAC](sql-database-export.md)
* [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
* [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!--Image references-->
[1]: ./media/sql-database-copy-portal/copy.png
[2]: ./media/sql-database-copy-portal/copy-ok.png
[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png




<!--HONumber=Dec16_HO2-->


