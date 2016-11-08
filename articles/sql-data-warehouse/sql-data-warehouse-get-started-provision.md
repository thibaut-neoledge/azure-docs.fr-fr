---
title: Créer un SQL Data Warehouse dans le portail Azure | Microsoft Docs
description: Découvrez comment créer un entrepôt de données Azure SQL Data Warehouse dans le portail Azure
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: ''
tags: azure-sql-data-warehouse

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 08/25/2016
ms.author: barbkess;lodipalm;sonyama

---
# Créer un Azure SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Portail Azure](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
> 
> 

Ce didacticiel fait appel au portail Azure pour créer un entrepôt de données SQL Data Warehouse qui contient un exemple de base de données AdventureWorksDW.

## Composants requis
Pour commencer, vous avez besoin des éléments suivants :

* **Compte Azure **: consultez [Évaluation gratuite d’Azure][Évaluation gratuite d’Azure] ou [Crédits Azure MSDN][Crédits Azure MSDN] pour créer un compte.
* **Serveur SQL Azure** : consultez [Création d’un serveur logique de base de données SQL Azure à l’aide du portail Azure][Création d’un serveur logique de base de données SQL Azure à l’aide du portail Azure].

> [!NOTE]
> La création d’un entrepôt de données SQL Data Warehouse peut donner lieu à un nouveau service facturable. Pour plus d’informations sur la tarification, consultez la page [Tarification de SQL Data Warehouse][Tarification de SQL Data Warehouse].
> 
> 

## Créer un entrepôt de données SQL
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **+ Nouveau** > **Données et stockage** > **SQL Data Warehouse**.
   
    ![Créer](./media/sql-data-warehouse-get-started-provision/create-sample.gif)
3. Dans le panneau **SQL Data Warehouse**, fournissez les informations nécessaires, puis appuyez sur « Créer ».
   
    ![Création d’une base de données](./media/sql-data-warehouse-get-started-provision/create-database.png)
   
   * **Serveur** : nous vous recommandons de commencer par sélectionner votre serveur.
   * **Nom de la base de données** : nom servant à référencer l’entrepôt de données SQL Data Warehouse. Le nom doit être unique sur le serveur.
   * **Performances** : nous vous recommandons de commencer par 400 [DWU][DWU]. Vous pouvez déplacer le curseur vers la gauche ou la droite pour ajuster les performances de votre entrepôt de données, ou le faire monter ou descendre en puissance suivant la création. Pour en savoir plus sur les DWU, consultez notre documentation sur la [montée en charge](sql-data-warehouse-manage-compute-overview.md) ou notre [page de tarification][SQL Data Warehouse pricing].
   * **Abonnement** : sélectionnez [l’abonnement] à utiliser pour la facturation de cet entrepôt de données SQL Data Warehouse.
   * **Groupe de ressources** : un [groupe de ressources][Resource group] est un conteneur conçu pour vous aider à gérer une collection de ressources Azure. En savoir plus sur les [groupes de ressources](../resource-group-overview.md).
   * **Sélectionner une source** : cliquez sur **Sélectionner une source** > **Exemple**. Azure renseigne automatiquement AdventureWorksDW dans l’option **Sélectionner un exemple**.

> [!NOTE]
> Le classement par défaut pour un entrepôt SQL Data Warehouse est SQL\_Latin1\_General\_CP1\_CI\_AS. Si vous avez besoin d’un classement différent, [T-SQL][T-SQL] peut être utilisé pour créer la base de données avec un autre classement.
> 
> 

1. Cliquez sur **Créer** pour créer votre entrepôt de données SQL Data Warehouse.
2. Patientez quelques minutes. Une fois que votre entrepôt de données est prêt, vous devriez être redirigé vers le [portail Azure](https://portal.azure.com). Vous pouvez trouver votre SQL Data Warehouse sur votre tableau de bord, répertorié sous vos bases de données SQL, ou dans le groupe de ressources que vous avez utilisé pour le créer.
   
    ![vue du portail](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[!INCLUDE [Créer un serveur de base de données SQL](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Étapes suivantes
Maintenant que vous avez créé un entrepôt de données SQL Data Warehouse, vous êtes prêt à vous [connecter](sql-data-warehouse-connect-overview.md) et à lancer des requêtes.

Pour consulter une vue d’ensemble sur le chargement, accédez à la rubrique [Chargement de données dans SQL Data Warehouse](sql-data-warehouse-overview-load.md).

Si vous essayez de migrer une base de données existante vers SQL Data Warehouse, consultez le [vue d’ensemble de la Migration](sql-data-warehouse-overview-migrate.md) ou utilisez [utilitaire de Migration de](sql-data-warehouse-migrate-migration-utility.md).

Les règles de pare-feu peuvent également être configurées à l’aide de Transact-SQL. Pour plus d’informations, consultez [sp\_set\_firewall\_rule][sp\_set\_firewall\_rule] et [sp\_set\_database\_firewall\_rule][sp\_set\_database\_firewall\_rule].

Nous vous recommandons également de prendre connaissance des [meilleures pratiques][meilleures pratiques].

<!--Article references-->
[Création d’un serveur logique de base de données SQL Azure à l’aide du portail Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Create an Azure SQL Database logical server with PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[resource groups]: ../resource-group-template-deploy-portal.md
[meilleures pratiques]: sql-data-warehouse-best-practices.md
[DWU]: sql-data-warehouse-overview-what-is.md#data-warehouse-units
[l’abonnement]: ../azure-glossary-cloud-terminology.md#subscription
[resource group]: ../azure-glossary-cloud-terminology.md#resource-group
[T-SQL]: ./sql-data-warehouse-get-started-create-database-tsql.md

<!--MSDN references-->
[sp\_set\_firewall\_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp\_set\_database\_firewall\_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Tarification de SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Évaluation gratuite d’Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Crédits Azure MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0831_2016-->