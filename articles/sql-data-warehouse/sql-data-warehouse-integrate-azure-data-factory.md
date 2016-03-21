<properties
   pageTitle="Utiliser Azure Data Factory avec SQL Data Warehouse | Microsoft Azure"
   description="Conseils sur l’utilisation de Microsoft Azure Data Factory avec Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Utiliser Azure Data Factory avec SQL Data Warehouse

Azure Data Factory offre une méthode entièrement gérée pour orchestrer le transfert de données et l’exécution de procédures stockées dans SQL Data Warehouse. Cela vous permettra de configurer et de planifier plus facilement des procédures ETL (« Extract, Transform, Load ») via SQL Data Warehouse. Pour consulter une présentation complète de Microsoft Azure Data Factory, accédez à la [documentation relative à ce logiciel][].

## Déplacement des données

Azure Data Factory permet le déplacement des données entre des sources locales, mais également entre divers services Microsoft Azure. De manière générale, le processus d’intégration actuel avec Microsoft Azure Data Factory prend en charge le déplacement de données depuis et vers les emplacements suivants :

+ Stockage des objets blob
+ Base de données SQL Azure
+ SQL Server local
+ SQL Server sur IaaS

Pour en savoir plus sur la configuration d’une activité de copie de données, voir [Copie de données avec Azure Data Factory (activité de copie)](../data-factory/data-factory-data-movement-activities.md).

## Procédures stockées
 De la même manière qu’il peut permettre de planifier le transfert de données, Azure Data Factory peut être utilisé pour orchestrer l’exécution des procédures stockées. Cela vous permet de créer des pipelines plus complexes et d’étendre la capacité de Microsoft Azure Data Factory à valoriser la puissance de calcul de SQL Data Warehouse.

## Étapes suivantes
Pour consulter une vue d’ensemble de l’intégration, accédez à la rubrique [Vue d’ensemble sur l’intégration de SQL Data Warehouse](sql-data-warehouse-overview-integrate.md). Pour obtenir des conseils supplémentaires en matière de développement, consultez la rubrique [Vue d’ensemble sur le développement SQL Data Warehouse](sql-data-warehouse-overview-develop.md).

<!--Image references-->

<!--Article references-->

[Copy data with Azure Data Factory]: https://azure.microsoft.com/fr-FR/documentation/articles/data-factory-azure-sql-connector/
[SQL Data Warehouse development overview]: https://azure.microsoft.com/fr-FR/documentation/articles/sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]: https://azure.microsoft.com/fr-FR/documentation/articles/sql-data-warehouse-overview-integrate/

<!--MSDN references-->

<!--Other Web references-->
[documentation relative à ce logiciel]: https://azure.microsoft.com/documentation/services/data-factory/
[Copy data with Azure Data Factory]: https://azure.microsoft.com/fr-FR/documentation/articles/data-factory-data-movement-activities/

<!------HONumber=AcomDC_0309_2016-->