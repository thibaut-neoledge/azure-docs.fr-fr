---
title: "Création d’un entrepôt SQL Data Warehouse à l’aide de PowerShell | Microsoft Docs"
description: "Création de SQL Data Warehouse à l’aide de PowerShell"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 97434863-7938-4129-8949-5a119f5949e3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3d13d4a0dd1d6e0b7361a57e167b06f0b717bfb4


---
# <a name="create-sql-data-warehouse-using-powershell"></a>Création de SQL Data Warehouse à l’aide de PowerShell
> [!div class="op_single_selector"]
> * [Portail Azure](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
> 
> 

Cet article vous explique comment créer un entrepôt SQL Data Warehouse à l’aide de PowerShell.

## <a name="prerequisites"></a>Composants requis
Pour commencer, vous avez besoin des éléments suivants :

* **Compte Azure** : consultez [Évaluation gratuite d’Azure][Évaluation gratuite d’Azure] ou [Crédits Azure MSDN][Crédits Azure MSDN] pour créer un compte.
* **Serveur SQL Azure** : pour plus de détails, consultez [Créer un serveur logique Azure SQL Database avec le portail Azure][Créer un serveur logique Azure SQL Database avec le portail Azure] ou [Créer un serveur logique Azure SQL Database avec PowerShell][Créer un serveur logique Azure SQL Database avec PowerShell].
* **Groupe de ressources** : utilisez le même groupe de ressources que votre serveur Azure SQL Server ou consultez [Créer un groupe de ressources][Créer un groupe de ressources].
* **PowerShell version 1.0.3 ou supérieure** : vous pouvez vérifier la version en exécutant **Get-Module -ListAvailable -Name Azure**.  La version la plus récente peut être installée à partir de [Microsoft Web Platform Installer][Microsoft Web Platform Installer].  Pour plus d’informations sur l’installation de la dernière version, consultez la page [Installation et configuration d’Azure PowerShell][Installation et configuration d’Azure PowerShell].

> [!NOTE]
> La création d’un entrepôt SQL Data Warehouse peut entraîner un nouveau service facturable.  Voir [Tarification de SQL Data Warehouse][Tarification de SQL Data Warehouse] pour plus d’informations sur la tarification.
> 
> 

## <a name="create-a-sql-data-warehouse"></a>Créer un entrepôt de données SQL
1. Ouvrez Windows PowerShell.
2. Exécutez cette applet de commande pour vous connecter à Azure Resource Manager.
   
    ```Powershell
    Login-AzureRmAccount
    ```
3. Sélectionnez l’abonnement que vous souhaitez utiliser pour votre session actuelle.
   
    ```Powershell
    Get-AzureRmSubscription    -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```
4. Créez la base de données. Cet exemple crée la base de données « mynewsqldw », avec le niveau d’objectif de service « DW400 » sur le serveur nommé « sqldwserver1 », qui se trouve dans le groupe de ressources nommé « mywesteuroperesgp1 ».
   
   ```Powershell
   New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
   ```

Les paramètres obligatoires sont :

* **RequestedServiceObjectiveName** : quantité de [DWU][DWU] que vous demandez.  Les valeurs prises en charge sont les suivantes : DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 et DW6000.
* **DatabaseName**: nom de l’entrepôt SQL Data Warehouse que vous créez.
* **ServerName**: nom du serveur que vous utilisez pour la création (doit être V12).
* **ResourceGroupName**: groupe de ressources que vous utilisez.  Pour trouver des groupes de ressources disponibles dans votre abonnement, utilisez Get-AzureResource.
* **Edition**: l’édition doit être définie sur « DataWarehouse » pour créer un entrepôt SQL Data Warehouse.

Les paramètres facultatifs sont :

* **CollationName** : le classement par défaut est COLLATE SQL_Latin1_General_CP1_CI_AS.  Le classement ne peut pas être modifié sur une base de données.
* **MaxSizeBytes**: par défaut, la taille maximale d’une base de données est de 10 Go.

Pour plus d’informations sur les options de paramètre, consultez [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase] et [Créer une base de données (Azure SQL Data Warehouse)][Créer une base de données (Azure SQL Data Warehouse)].

## <a name="next-steps"></a>Étapes suivantes
Une fois votre entrepôt SQL Data Warehouse approvisionné, vous pouvez [charger les exemples de données][charger les exemples de données] ou découvrir comment [développer][développer], [charger][charger] ou [migrer][migrer].

Si vous souhaitez en savoir plus sur la gestion de l’entrepôt SQL Data Warehouse par programme, consultez notre article sur l’utilisation des [applets de commande PowerShell et des API REST][applets de commande PowerShell et des API REST].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[migrer]: ./sql-data-warehouse-overview-migrate.md
[développer]: ./sql-data-warehouse-overview-develop.md
[charger]: ./sql-data-warehouse-load-with-bcp.md
[chargement d’exemples de données]: ./sql-data-warehouse-load-sample-databases.md
[applets de commande PowerShell et des API REST]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[règles de pare-feu]: ../sql-database-configure-firewall-settings.md

[Installation et configuration d’Azure PowerShell]: ../powershell-install-configure.md
[création d’une base de données SQL Data Warehouse à partir du portail Azure]: ./sql-data-warehouse-get-started-provision.md
[Créer un serveur logique de base de données SQL Azure avec le Portail Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Créer un serveur logique de base de données SQL Azure avec PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[Créer un groupe de ressources]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references-->
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Créer une base de données (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[Tarification de SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Évaluation gratuite d’Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Crédits Azure MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Nov16_HO2-->


