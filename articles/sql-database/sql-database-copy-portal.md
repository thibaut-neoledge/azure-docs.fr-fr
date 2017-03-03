---
title: "Copie d’une base de données SQL Azure à l’aide du portail Azure | Microsoft Docs"
description: "Création d&quot;une copie d&quot;une base de données SQL Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: daa6f079-13ed-462f-b346-e201aa61681b
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
ms.openlocfilehash: 5da07683a4ca9fe1751debc163a0a5a595b6f82b
ms.lasthandoff: 02/11/2017


---
# <a name="copy-an-azure-sql-database-using-the-azure-portal"></a>Copie d’une base de données SQL Azure à l’aide du portail Azure

Les étapes suivantes vous montrent comment copier une base de données SQL avec le [Portail Azure](https://portal.azure.com) sur le même serveur ou un serveur différent. 

> [!NOTE]
> Vous pouvez également copier une base de données SQL à l’aide de [PowerShell](sql-database-copy-powershell.md) ou [Transact-SQL](sql-database-copy-transact-sql.md).
>

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
6. Une fois le **Serveur cible** sélectionné, les options **Pool élastique** et **Niveau tarifaire** sont activées. Si le serveur dispose d’un pool, vous pouvez y copier la base de données.
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
* Pour en savoir plus sur la gestion des utilisateurs et des connexions lors de la copie d’une base de données sur un autre serveur logique, consultez [Configurer et gérer la sécurité Azure SQL Database pour la géo-restauration ou le basculement](sql-database-geo-replication-security-config.md).
* Pour exporter une base de données dans un fichier BACPAC à l’aide du portail Azure, consultez [Export the database to a BACPAC using the Azure portal](sql-database-export-portal.md) (Exporter la base de données dans un fichier BACPAC à l’aide du portail Azure).
* [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
* [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!--Image references-->
[1]: ./media/sql-database-copy-portal/copy.png
[2]: ./media/sql-database-copy-portal/copy-ok.png
[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png


