<properties
   pageTitle="Migrer vers Base de données SQL à l’aide de la réplication transactionnelle"
   description="Base de données SQL Microsoft Azure, migration de base de données, importer une base de données, réplication transactionnelle"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="03/14/2016"
   ms.author="carlrab"/>

# Migrer une base de données SQL Server vers Base de données SQL à l’aide de la réplication transactionnelle

Quand vous ne pouvez pas vous permettre de sortir votre base de données SQL Server de la production pendant la migration, vous pouvez utiliser la réplication transactionnelle SQL Server comme solution de migration. Avec la réplication transactionnelle, toutes les modifications apportées à vos données ou à votre schéma qui se produisent entre le début et à la fin de la migration s'afficheront dans votre base de données SQL Azure. Une fois la migration terminée, vous devez modifier la chaîne de connexion de vos applications afin de les pointer vers votre base de données SQL Azure au lieu de votre base de données locale. Une fois que la réplication transactionnelle a appliqué toutes les modifications restantes sur votre base de données locale et que toutes vos applications pointent vers la base de données Azure, vous pouvez désinstaller en toute sécurité la réplication en conservant votre base de données SQL Azure comme système de production.

 ![Diagramme SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)


La réplication transactionnelle est une technologie intégrée à SQL Server depuis SQL Server 6.5. Il s'agit d'une technologie très mature et éprouvée que la plupart des administrateurs de bases de données maîtrisent parfaitement. Avec la [version préliminaire de SQL Server 2016](http://www.microsoft.com/server-cloud/products/sql-server-2016/), il est désormais possible de configurer votre Base de données SQL Azure comme un [abonné de réplication transactionnelle](https://msdn.microsoft.com/library/mt589530.aspx) à votre publication locale. La configuration dans Management Studio est identique à la configuration d'un abonné de réplication transactionnelle sur un serveur local. Prise en charge de ce scénario lorsque le serveur de publication et le serveur de distribution sont au moins une des versions suivantes de SQL Server :

 - SQL Server 2016 CTP3 (version préliminaire) et versions ultérieures 
 - SQL Server 2014 SP1 CU3 et versions ultérieures
 - SQL Server 2014 RTM CU10 et versions ultérieures
 - SQL Server 2012 SP2 CU8 et versions ultérieures
 - SQL Server 2013 SP3 dès sa sortie


> [AZURE.IMPORTANT] Vous devez utiliser la dernière version de SQL Server Management Studio afin de rester en cohérence avec les mises à jour de Microsoft Azure et Base de données SQL. Les versions antérieures de SQL Server Management Studio ne pourront pas configurer la base de données SQL en tant qu’abonné. [Mettre à jour SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


Vous pouvez également utiliser la réplication transactionnelle pour migrer un sous-ensemble de votre base de données locale. La publication que vous répliquez vers une base de données SQL Azure peut être limitée à un sous-ensemble des tables dans la base de données en cours de réplication. Par ailleurs, pour chaque table répliquée, vous pouvez limiter les données à un sous-ensemble de lignes et/ou un sous-ensemble de colonnes.

<!---HONumber=AcomDC_0518_2016-->