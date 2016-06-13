<properties
	pageTitle="Performances et prix du pool élastique de base de données SQL"
	description="Informations de tarification propres aux pools de bases de données élastiques."
	services="sql-database"
	documentationCenter=""
	authors="srinia"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="05/27/2016"
	ms.author="srinia"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Informations sur la tarification et la facturation du pool de base de données élastique

Les pools de bases de données élastiques sont facturés en fonction des caractéristiques suivantes :

- Un pool élastique est facturé lors de sa création, même s’il n’inclut aucune base de données.
- Il est facturé toutes les heures. La fréquence de mesure est identique à celle des niveaux de performances associés aux bases de données uniques.
- Si un pool élastique est redimensionné en fonction d’un nouveau nombre d’eDTU, ce pool n’est pas facturé selon ce nouveau nombre, tant que l’opération de redimensionnement n’est pas terminée. Le processus est le même que lors de la modification du niveau de performances des bases de données autonomes.


- Le prix d’un pool élastique est basé sur le nombre d’eDTU de ce pool. Le prix d’un pool élastique est indépendant du nombre et de l’utilisation des bases de données élastiques qu’il contient.
- Le prix est calculé comme suit : le nombre d’eDTUs d’un pool x le prix unitaire par eDTU.

Le prix unitaire des eDTU d’un pool élastique est supérieur au prix unitaire des DTU d’une base de données autonome au sein du même niveau de service. Pour en savoir plus, voir [Tarification de la base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).


Pour comprendre les eDTU et les niveaux de service, consultez [Options et performances de la base de données SQL](sql-database-service-tiers.md).

## Étapes suivantes

- [Créer un pool de bases de données élastiques](sql-database-elastic-pool-create-portal.md)
- [Surveiller, gérer et dimensionner un pool de bases de données élastique.](sql-database-elastic-pool-manage-portal.md)
- [Options et performances de la base de données SQL : comprendre ce qui est disponible dans chaque niveau de service](sql-database-service-tiers.md)
- [Script PowerShell pour identifier les bases de données adaptées à un pool de bases de données élastique](sql-database-elastic-pool-database-assessment-powershell.md)

<!---HONumber=AcomDC_0601_2016-->