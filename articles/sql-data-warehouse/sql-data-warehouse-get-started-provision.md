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
   ms.date="05/05/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Créer un entrepôt de données SQL

> [AZURE.SELECTOR]
- [Portail Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Dans ce didacticiel, vous utiliserez le portail Azure pour créer un entrepôt de données SQL Data Warehouse qui contient un exemple de base de données AdventureWorksDW.


[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Cliquez sur **+ Nouveau** > **Données et stockage** > **SQL Data Warehouse**.

    ![Créer](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

3. Dans le panneau **SQL Data Warehouse**, fournissez les informations nécessaires, puis appuyez sur « Créer ».

    ![Création d’une base de données](./media/sql-data-warehouse-get-started-provision/create-database.png)

	- **Serveur** : nous vous recommandons de commencer par sélectionner votre serveur. Vous pouvez sélectionner un groupe de ressources existant ou [en créer un](./sql-data-warehouse-get-started-new-server.md). 

	- **Nom de la base de données** : le nom qui vous servira à référencer le SQL Data Warehouse. Le nom doit être unique sur le serveur.
	
    - **Performances** : nous vous recommandons de commencer par 400 DWU. Vous pouvez déplacer le curseur vers la gauche ou la droite pour ajuster les performances de votre entrepôt de données, ou le faire monter ou descendre en puissance suivant la création. Pour en savoir plus sur les DWU, consultez notre documentation sur la [montée en charge](./sql-data-warehouse-overview-scalability.md) ou notre [page de tarification](https://azure.microsoft.com/fr-FR/pricing/details/sql-data-warehouse/).

    - **Abonnement** : sélectionnez l’abonnement sur lequel ce SQL Data Warehouse sera facturé.

    - **Groupe de ressources** : un groupe de ressources est un conteneur, conçu pour vous aider à gérer un ensemble de ressources Microsoft Azure. En savoir plus sur les [groupes de ressources](../azure-portal/resource-group-portal.md).

    - **Sélectionner une source** : cliquez sur **Sélectionner une source** > **Exemple**. Étant donné qu’un seul exemple de base de données est disponible à ce stade, lorsque vous sélectionnez Exemple, Azure renseigne automatiquement l’option **Sélectionner un exemple** avec AdventureWorksDW.

4. Cliquez sur **Créer** pour créer votre SQL Data Warehouse.

5. Patientez quelques minutes et votre SQL Data Warehouse sera prêt. Une fois que vous avez terminé, vous êtes en principe redirigé vers le [portail Azure](https://portal.azure.com). Vous pouvez trouver votre SQL Data Warehouse sur votre tableau de bord, répertorié sous vos bases de données SQL, ou dans le groupe de ressources que vous avez utilisé pour le créer.

    ![Vue du portail](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[AZURE.INCLUDE [Créer un serveur de base de données SQL](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Étapes suivantes

Maintenant que vous avez créé un SQL Data Warehouse, vous êtes prêt à vous [connecter](./sql-data-warehouse-get-started-connect.md) et à lancer des requêtes.

Pour consulter une vue d’ensemble sur le chargement, accédez à la rubrique [Chargement de données dans SQL Data Warehouse](./sql-data-warehouse-overview-load.md).

Si vous essayez de migrer une base de données existante vers SQL Data Warehouse, consultez le [vue d’ensemble de la Migration](./sql-data-warehouse-overview-migrate.md) ou utilisez [utilitaire de Migration de](./sql-data-warehouse-migrate-migration-utility.md).

<!---HONumber=AcomDC_0511_2016-->