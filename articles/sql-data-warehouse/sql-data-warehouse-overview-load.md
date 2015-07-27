<properties
   pageTitle="Charger des données dans SQL Data Warehouse | Microsoft Azure"
   description="Découvrez les scénarios courants de chargement des données dans SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/21/2015"
   ms.author="mausher;barbkess"/>

# Charger des données dans SQL Data Warehouse
SQL Data Warehouse comporte de nombreuses options permettant de charger des données selon divers scénarios. Par exemple, vous pouvez charger :

- de grands lots, une fois par jour ;
- de plus petits lots, tout au long de la journée ;
- de simples mises à jour dans des tables (de dimension) plus réduites.

Les exigences des scénarios sont aussi variées que les types de données que vous souhaitez charger dans SQL Data Warehouse. Dans cet article, nous répertorions les options de chargement de données dont vous disposez.

## Technologies
SQL Data Warehouse prend en charge ces outils standard de chargement des données :

- Azure Data Factory
- Utilitaire de ligne de commande bcp
- PolyBase
- SQL Server Integration Services (SSIS)
- Outils de chargement de données tiers

### Azure Data Factory (ADF)
Azure Data Factory est un service entièrement géré pour composer des services de stockage, de traitement et de déplacement des données dans des pipelines de production de données rationalisés, évolutifs et fiables. SQL Data Warehouse est [une source/un récepteur pris(e) en charge][] pour l’[activité de copie][] ADF.

### Utilitaire de ligne de commande bcp
L’exécutable de ligne de commande **bcp** est un utilitaire Microsoft qui permet de charger et d’extraire des données de SQL Server, de la base de données SQL et de SQL Data Warehouse. Pour commencer, suivez le didacticiel [Charger des données avec bcp][].

### PolyBase
PolyBase est une technologie Microsoft qui simplifie l’analyse des données en proposant un moyen d’interroger le stockage d’objets Blob Microsoft Azure et Hadoop, avec le langage standard Transact-SQL, sans recourir à MapReduce. PolyBase peut également charger des données à partir des objets Blob Microsoft Azure vers SQL Data Warehouse. Pour commencer, suivez le didacticiel [Charger des données avec PolyBase][].

### SQL Server Integration Services (SSIS)
[SSIS][] est une plateforme prenant en charge le développement de solutions d’intégration et de transformation des données de niveau entreprise. Pour créer des packages qui se connectent à SQL Data Warehouse, utilisez la norme [adaptateur de destination OLE DB][] à l’aide d’un gestionnaire de connexions .Net ADO.

### Outils tiers
SQL Data Warehouse prend en charge les solutions leaders du marché de chargement des données. Pour en savoir plus, consultez notre liste de [partenaires de solutions][].

## Étapes suivantes
Pour obtenir des conseils supplémentaires sur le développement, consultez la [vue d’ensemble sur le développement][].

<!--Image references-->

<!--Article references-->
[Charger des données avec bcp]: sql-data-warehouse-load-with-bcp.md
[Charger des données avec PolyBase]: sql-data-warehouse-load-with-polybase.md
[partenaires de solutions]: sql-data-warehouse-solution-partners.md
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[une source/un récepteur pris(e) en charge]: https://msdn.microsoft.com/library/dn894007.aspx
[activité de copie]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141237.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!--Other Web references-->

<!---HONumber=July15_HO3-->