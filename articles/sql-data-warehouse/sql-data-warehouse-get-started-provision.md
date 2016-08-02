<properties
   pageTitle="Créer un SQL Data Warehouse dans le portail Azure | Microsoft Azure"
   description="Découvrez comment créer une base de données Azure SQL Data Warehouse dans le portail Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/23/2016"
   ms.author="barbkess;lodipalm;sonyama"/>

# Créer un Azure SQL Data Warehouse

> [AZURE.SELECTOR]
- [Portail Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Dans ce didacticiel, vous utiliserez le portail Azure pour créer un entrepôt de données SQL Data Warehouse qui contient un exemple de base de données AdventureWorksDW.


## Configuration requise

Pour commencer, vous avez besoin des éléments suivants :

- **Compte Azure **: consultez [Évaluation gratuite d’Azure][] ou [Crédits Azure MSDN][] pour créer un compte.
- **Serveur SQL Azure**: consultez [Créer un serveur logique de base de données SQL Azure avec le Portail Azure][].

> [AZURE.NOTE] La création d’un entrepôt SQL Data Warehouse peut entraîner un nouveau service facturable. Voir [Tarification de SQL Data Warehouse ][] pour plus d’informations sur la tarification.

## Créer un entrepôt de données SQL

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Cliquez sur **+ Nouveau** > **Données et stockage** > **SQL Data Warehouse**.

    ![Créer](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

3. Dans le panneau **SQL Data Warehouse**, fournissez les informations nécessaires, puis appuyez sur « Créer ».

    ![Création d’une base de données](./media/sql-data-warehouse-get-started-provision/create-database.png)

	- **Serveur** : nous vous recommandons de commencer par sélectionner votre serveur. Vous pouvez sélectionner un groupe de ressources existant ou [en créer un](./sql-data-warehouse-get-started-new-server.md).

	- **Nom de la base de données** : le nom qui vous servira à référencer le SQL Data Warehouse. Le nom doit être unique sur le serveur.
	
    - **Performances** : nous vous recommandons de commencer par 400 [DWU][DWU]. Vous pouvez déplacer le curseur vers la gauche ou la droite pour ajuster les performances de votre entrepôt de données, ou le faire monter ou descendre en puissance suivant la création. Pour en savoir plus sur les DWU, consultez notre documentation sur la [montée en charge](./sql-data-warehouse-manage-compute-overview.md) ou notre [page de tarification][SQL Data Warehouse pricing].

    - **Abonnement** : sélectionnez [l’abonnement] sur lequel ce SQL Data Warehouse sera facturé.

    - **Groupe de ressources** : un [groupe de ressources][Resource group] est un conteneur, conçu pour vous aider à gérer un ensemble de ressources Microsoft Azure. En savoir plus sur les [groupes de ressources](../resource-group-overview.md).

    - **Sélectionner une source** : cliquez sur **Sélectionner une source** > **Exemple**. Étant donné qu’un seul exemple de base de données est disponible à ce stade, lorsque vous sélectionnez Exemple, Azure renseigne automatiquement l’option **Sélectionner un exemple** avec AdventureWorksDW.

4. Cliquez sur **Créer** pour créer votre SQL Data Warehouse.

5. Patientez quelques minutes et votre SQL Data Warehouse sera prêt. Une fois que vous avez terminé, vous êtes en principe redirigé vers le [Portail Azure](https://portal.azure.com). Vous pouvez trouver votre SQL Data Warehouse sur votre tableau de bord, répertorié sous vos bases de données SQL, ou dans le groupe de ressources que vous avez utilisé pour le créer.

    ![Vue du portail](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[AZURE.INCLUDE [Créer un serveur de base de données SQL](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Étapes suivantes

Maintenant que vous avez créé un SQL Data Warehouse, vous êtes prêt à vous [connecter](./sql-data-warehouse-connect-overview.md) et à lancer des requêtes.

Pour consulter une vue d’ensemble sur le chargement, accédez à la rubrique [Chargement de données dans SQL Data Warehouse](./sql-data-warehouse-overview-load.md).

Si vous essayez de migrer une base de données existante vers SQL Data Warehouse, consultez le [vue d’ensemble de la Migration](./sql-data-warehouse-overview-migrate.md) ou utilisez [utilitaire de Migration de](./sql-data-warehouse-migrate-migration-utility.md).

Les règles de pare-feu peuvent également être configurées à l’aide de Transact-SQL. Pour plus d’informations, voir [sp\_set\_firewall\_rule][] et [sp\_set\_database\_firewall\_rule][].

Il convient également de jeter un œil à nos [Meilleures pratiques][].

<!--Article references-->
[Créer un serveur logique de base de données SQL Azure avec le Portail Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Create an Azure SQL Database logical server with PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[resource groups]: ../resource-group-template-deploy-portal.md
[Meilleures pratiques]: ./sql-data-warehouse-best-practices.md
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[l’abonnement]: ../azure-glossary-cloud-terminology.md#subscription
[resource group]: ../azure-glossary-cloud-terminology.md#resource-group

<!--MSDN references-->
[sp\_set\_firewall\_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp\_set\_database\_firewall\_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Tarification de SQL Data Warehouse ]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Évaluation gratuite d’Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Crédits Azure MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0727_2016-->