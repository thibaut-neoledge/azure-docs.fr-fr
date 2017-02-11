---
title: Utiliser Azure Data Factory avec SQL Data Warehouse | Microsoft Docs
description: "Conseils sur l’utilisation de Microsoft Azure Data Factory avec Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 492de762-c7a2-4cdb-943f-3135230e94f1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 427a1e8c6bfb11ace38fcf84b7cda89d2d01d091


---
# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>Utiliser Azure Data Factory avec SQL Data Warehouse
Azure Data Factory offre une méthode entièrement gérée pour orchestrer le transfert de données et l’exécution de procédures stockées dans SQL Data Warehouse.  Cela vous permettra de configurer et de planifier plus facilement des procédures ETL (« Extract, Transform, Load ») via SQL Data Warehouse. Pour consulter une présentation complète de Microsoft Azure Data Factory, accédez à la [documentation relative à ce logiciel]documentation relative à [Azure Data Factory].

## <a name="data-movement"></a>Déplacement des données
Azure Data Factory permet le déplacement des données entre des sources locales, mais également entre divers services Microsoft Azure.  De manière générale, le processus d’intégration actuel avec Microsoft Azure Data Factory prend en charge le déplacement de données depuis et vers les emplacements suivants :

* Stockage des objets blob
* Base de données SQL Azure
* SQL Server local
* SQL Server sur IaaS

Pour en savoir plus sur la configuration d’une activité de copie de données, consultez [Copie de données avec Azure Data Factory][Copie de données avec Azure Data Factory]

## <a name="stored-procedures"></a>Procédures stockées
 De la même manière qu’il peut permettre de planifier le transfert de données, Azure Data Factory peut être utilisé pour orchestrer l’exécution des procédures stockées.  Cela vous permet de créer des pipelines plus complexes et d’étendre la capacité de Microsoft Azure Data Factory à valoriser la puissance de calcul de SQL Data Warehouse.

## <a name="next-steps"></a>Étapes suivantes
Pour une vue d’ensemble de l’intégration, consultez [Vue d’ensemble sur l’intégration de SQL Data Warehouse][Vue d’ensemble sur l’intégration de SQL Data Warehouse].
Pour obtenir des conseils supplémentaires en matière de développement, consultez [Vue d’ensemble sur le développement SQL Data Warehouse][Vue d’ensemble sur le développement SQL Data Warehouse].

<!--Image references-->

<!--Article references-->

[Copie de données avec Azure Data Factory]: ../data-factory/data-factory-data-movement-activities.md
[Vue d’ensemble sur le développement SQL Data Warehouse]: ./sql-data-warehouse-overview-develop.md
[Vue d’ensemble sur l’intégration de SQL Data Warehouse]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Documentation Azure Data Factory]:https://azure.microsoft.com/documentation/services/data-factory/




<!--HONumber=Nov16_HO3-->


