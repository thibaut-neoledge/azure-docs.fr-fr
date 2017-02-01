---
title: "Migration vers SQL Database à l’aide de la réplication transactionnelle | Microsoft Docs"
description: "Base de données SQL Microsoft Azure, migration de base de données, importer une base de données, réplication transactionnelle"
services: sql-database
documentationcenter: 
author: jognanay
manager: jhubbard
editor: 
ms.assetid: eebdd725-833d-4151-9b2b-a0303f39e30f
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 12/09/2016
ms.author: carlrab; jognanay;
translationtype: Human Translation
ms.sourcegitcommit: 8baeadbf7ef24e492c4115745c0d384e4f526188
ms.openlocfilehash: 8380925a56d39bd53fe737bed539b862cc835fad


---
# <a name="migrate-sql-server-database-to-azure-sql-database-using-transactional-replication"></a>Migrer une base de données SQL Server vers une Base de données SQL Azure à l’aide de la réplication transactionnelle
> [!div class="op_single_selector"]
> * [Assistant Migration SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
> * [Exporter vers un fichier BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [Importer depuis un fichier BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [Réplication transactionnelle](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
> 
> 

Dans cet article, vous apprendrez à migrer une base de données SQL Server compatible vers une Base de données SQL Azure avec un temps d’arrêt minimal grâce à la réplication transactionnelle de SQL Server.

## <a name="understanding-the-transactional-replication-architecture"></a>Explication de l’architecture de la réplication transactionnelle
Quand vous ne pouvez pas vous permettre de sortir votre base de données SQL Server de la production pendant la migration, vous pouvez utiliser la réplication transactionnelle SQL Server comme solution de migration. Pour utiliser cette solution, vous configurez votre Base de données SQL Azure en tant qu’abonné à l’instance de SQL Server locale que vous souhaitez migrer. Le distributeur de réplication transactionnelle locale synchronise les données nécessaires de la base de données locale (l’éditeur) alors que de nouvelles transactions continuent d’avoir lieu. 

Vous pouvez également utiliser la réplication transactionnelle pour migrer un sous-ensemble de votre base de données locale. La publication que vous répliquez vers une base de données SQL Azure peut être limitée à un sous-ensemble des tables dans la base de données en cours de réplication. Pour chaque table répliquée, vous pouvez limiter les données à un sous-ensemble de lignes et/ou un sous-ensemble de colonnes.

Avec la réplication transactionnelle, toutes les modifications apportées à vos données ou à votre schéma apparaissent dans votre Base de données SQL Azure. Une fois la synchronisation terminée, lorsque vous êtes prêt à migrer, modifiez la chaîne de connexion de vos applications de façon à ce qu’elle pointe vers votre Base de données SQL Azure. Une fois que la réplication transactionnelle a vidé toutes les modifications restant sur votre base de données locale et que toutes vos applications pointent vers Base de données Azure, vous pouvez désinstaller la réplication transactionnelle. Votre Base de données SQL Azure est maintenant votre système de production.

 ![Diagramme SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## <a name="how-transactional-replication-works"></a>Comment fonctionne la réplication transactionnelle

La réplication transactionnelle implique trois principaux composants. Il s’agit de l’éditeur, du distributeur et de l’abonné. Ces composants effectuent ensemble la réplication. Le distributeur est chargé de contrôler les processus qui déplacent vos données d’un serveur à l’autre. Lorsque vous configurez la distribution, SQL crée une base de données de distribution. Chaque éditeur doit être lié à une base de données de distribution. La base de données de distribution conserve les métadonnées de chaque publication associée et les données sur la progression de chaque réplication. Pour la réplication de transactions, elle contient toutes les transactions qui doivent être exécutées dans l’abonné.

L’éditeur est la base de données dont proviennent toutes les données de la migration. Il peut y avoir de nombreuses publications au sein de l’éditeur. Ces publications contiennent des articles correspondant à toutes les tables et données qui doivent être répliquées. Selon la façon dont vous définissez la publication et les articles, vous pouvez répliquer tout ou partie de votre base de données. 

Dans la réplication, l’abonné est le serveur qui reçoit toutes les données et transactions de la publication. Chaque publication peut avoir de nombreuses réplications.

## <a name="transactional-replication-requirements"></a>Conditions requises de la réplication transactionnelle
[Cliquez sur ce lien pour voir la liste actualisée des exigences.](https://msdn.microsoft.com/en-US/library/mt589530.aspx)
> [!IMPORTANT]
> Utilisez la dernière version de SQL Server Management Studio pour rester synchronisé avec les mises à jour de Microsoft Azure et de Base de données SQL. Les versions antérieures de SQL Server Management Studio ne peuvent pas configurer Base de données SQL en tant qu’abonné. [Mettre à jour SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Workflow de migration vers SQL Database à l’aide de la réplication transactionnelle

1. Configurer la distribution
   -  [Avec SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   -  [Avec Transact-SQL](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)
2. Créer une publication
   -  [Avec SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   -  [Avec Transact-SQL](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. Créer un abonnement
   -  [Avec SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   -  [Avec Transact-SQL](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

## <a name="some-tips-and-differences-for-migrating-to-sql-database"></a>Quelques conseils et différences pour la migration vers SQL Database

1. Utilisez un serveur de distribution local 
   - Cela a un impact sur les performances du serveur. 
   - Si l’impact sur les performances est inacceptable, vous pouvez utiliser un autre serveur, mais cela ajoute de la complexité de gestion et d’administration.
2. Lorsque vous sélectionnez un dossier de capture instantanée, assurez-vous qu’il est suffisamment grand pour contenir un BCP de chaque table que vous souhaitez répliquer. 
3. Notez que la création d’instantanés verrouillera les tables associées jusqu’à ce qu’elle soit terminée : gardez cela à l’esprit quand vous planifiez votre capture instantanée. 
4. Seuls les abonnements de type push sont pris en charge dans Azure SQL Database.
   - Vous ne pouvez ajouter des abonnés que du côté de votre base de données locale.

## <a name="next-steps"></a>Étapes suivantes
* [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [SQL Server 2016 ](https://www.microsoft.com/sql-server/sql-server-2016)

## <a name="additional-resources"></a>Ressources supplémentaires
* Pour plus d’informations sur la réplication transactionnelle, consultez la page [Réplication transactionnelle](https://msdn.microsoft.com/library/mt589530.aspx).
* Pour en savoir plus sur les options et le processus global de migration, consultez la page [Migration d’une base de données SQL Server vers SQL Database dans le cloud](sql-database-cloud-migrate.md).



<!--HONumber=Dec16_HO2-->


