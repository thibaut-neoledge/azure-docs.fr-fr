
<!--
includes/sql-data-warehouse-backup-retention-policies.md

Latest Freshness check:  2016-05-05 , barbkess.

As of circa 2016-04-22, the following topics might include this include:
articles/sql-data-warehouse/sql-data-warehouse-overview-expectations.md
articles/sql-data-warehouse/sql-data-warehouse-overview-backup-and-restore.md
-->
SQL Data Warehouse crée des instantanés de toutes les données actives au moins toutes les 8 heures, à l’aide d’instantanés Azure Storage. Ces instantanés sont conservés pendant 7 jours. Ainsi, vous pouvez restaurer les données à 21 instants différents au cours des 7 derniers jours, jusqu’au moment de la prise du dernier instantané.

Avant de supprimer une base de données, SQL Data Warehouse en crée un instantané et le conserve 7 jours. Lorsque cela se produit, il ne conserve pas les instantanés de la base de données active. Cela vous permet de restaurer une base de données supprimée dans son état au moment de sa suppression.

<!---HONumber=AcomDC_0608_2016-->