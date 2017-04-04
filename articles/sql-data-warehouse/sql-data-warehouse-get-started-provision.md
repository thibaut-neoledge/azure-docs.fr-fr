---
title: "Création d’un entrepôt SQL Data Warehouse dans le portail Azure | Microsoft Docs"
description: "Découvrez comment créer un entrepôt de données Azure SQL Data Warehouse dans le portail Azure"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: 552e496e-d560-419c-9996-6bbc80c521cb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 75f4792ff2aa2b73ebc3ff976887a74ce09988f3
ms.lasthandoff: 03/10/2017


---
# <a name="create-an-azure-sql-data-warehouse"></a>Créer un Azure SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Portail Azure](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

Ce didacticiel fait appel au portail Azure pour créer un entrepôt de données SQL Data Warehouse qui contient un exemple de base de données AdventureWorksDW.

## <a name="prerequisites"></a>Composants requis
Pour commencer, vous avez besoin des éléments suivants :

* **Compte Azure** : consultez [Évaluation gratuite d’Azure][Azure Free Trial] ou [Crédits Azure MSDN][MSDN Azure Credits] pour créer un compte.
* **Serveur SQL Azure** : pour en savoir plus, consultez [Create and query a single Azure SQL database in the Azure portal][Create an Azure SQL database in the Azure portal] (Créer et interroger une base de données SQL Azure unique dans le portail Azure).

> [!NOTE]
> La création d’un entrepôt de données SQL Data Warehouse peut donner lieu à un nouveau service facturable.  Pour plus d’informations sur la tarification, consultez la page [SQL Data Warehouse Tarification][SQL Data Warehouse pricing].
>
>

## <a name="create-a-sql-data-warehouse"></a>Créer un entrepôt de données SQL
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **+Nouveau** > **Bases de données** > **SQL Data Warehouse**.

    ![Créer](./media/sql-data-warehouse-get-started-provision/create-sample.gif)
3. Dans le panneau **SQL Data Warehouse** , fournissez les informations nécessaires, puis appuyez sur « Créer ».

    ![Création d’une base de données](./media/sql-data-warehouse-get-started-provision/create-database.png)

   * **Serveur**: nous vous recommandons de commencer par sélectionner votre serveur.  
   * **Nom de la base de données**: nom servant à référencer l’entrepôt de données SQL Data Warehouse.  Le nom doit être unique sur le serveur.
   * **Performances** : nous vous recommandons de commencer par 400 [DWU][DWU]. Vous pouvez déplacer le curseur vers la gauche ou la droite pour ajuster les performances de votre entrepôt de données, ou le faire monter ou descendre en puissance suivant la création.  Pour en savoir plus sur les DWU, consultez notre documentation sur la [montée en charge](sql-data-warehouse-manage-compute-overview.md) ou notre [page de tarification][SQL Data Warehouse pricing].
   * **Abonnement**: sélectionnez [l’abonnement] à utiliser pour la facturation de cet entrepôt de données SQL Data Warehouse.
   * **Groupe de ressources** : un [groupe de ressources][Resource group] est un conteneur conçu pour vous aider à gérer une collection de ressources Azure. En savoir plus sur les [groupes de ressources](../azure-resource-manager/resource-group-overview.md).
   * **Sélectionner une source** : cliquez sur **Sélectionner une source** > **Exemple**. Azure renseigne automatiquement AdventureWorksDW dans l’option **Sélectionner un exemple** .

   > [!NOTE]
   > Le classement par défaut pour un entrepôt SQL Data Warehouse est SQL_Latin1_General_CP1_CI_AS. Si vous avez besoin d’un classement différent, [T-SQL][T-SQL] permet de créer la base de données avec un autre classement.
   >
   >

1. Cliquez sur **Créer** pour créer votre entrepôt de données SQL Data Warehouse.
2. Patientez quelques minutes. Une fois que votre entrepôt de données est prêt, vous devriez être redirigé vers le [Portail Azure](https://portal.azure.com). Vous pouvez trouver votre SQL Data Warehouse sur votre tableau de bord, répertorié sous vos bases de données SQL, ou dans le groupe de ressources que vous avez utilisé pour le créer.

    ![vue du portail](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez créé un entrepôt de données SQL Data Warehouse, vous êtes prêt à vous [connecter](sql-data-warehouse-connect-overview.md) et à lancer des requêtes.

Pour consulter une vue d’ensemble sur le chargement, accédez à la rubrique [Chargement de données dans SQL Data Warehouse](sql-data-warehouse-overview-load.md).

Si vous tentez de migrer une base de données existante vers SQL Data Warehouse, consultez la [vue d’ensemble de la migration](sql-data-warehouse-overview-migrate.md) ou utilisez [l’utilitaire de migration](sql-data-warehouse-migrate-migration-utility.md).

Les règles de pare-feu peuvent également être configurées à l’aide de Transact-SQL. Pour plus d’informations, consultez [sp_set_firewall_rule][sp_set_firewall_rule] et [sp_set_database_firewall_rule][sp_set_database_firewall_rule].

Nous vous recommandons également de prendre connaissance des [bonnes pratiques][Best practices].

<!--Article references-->
[Create an Azure SQL database in the Azure portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-create-and-configure-database-powershell
[resource groups]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[Best practices]: sql-data-warehouse-best-practices.md
[DWU]: sql-data-warehouse-overview-what-is.md
[l’abonnement]: ../azure-glossary-cloud-terminology.md#subscription
[resource group]: ../azure-glossary-cloud-terminology.md#resource-group
[T-SQL]: ./sql-data-warehouse-get-started-create-database-tsql.md

<!--MSDN references-->
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

