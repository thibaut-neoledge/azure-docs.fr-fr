---
title: Utiliser Azure Data Factory avec SQL Data Warehouse | Microsoft Docs
description: "Conseils sur l’utilisation de Microsoft Azure Data Factory avec Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 492de762-c7a2-4cdb-943f-3135230e94f1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 12743b3594a0a62b0b974fb6dd1c39972c1bd3fa
ms.contentlocale: fr-fr
ms.lasthandoff: 04/03/2017


---
# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>Utiliser Azure Data Factory avec SQL Data Warehouse
Azure Data Factory offre une méthode entièrement gérée pour orchestrer le transfert de données et l’exécution de procédures stockées dans SQL Data Warehouse.  Cela vous permettra de configurer et de planifier plus facilement des procédures ETL (« Extract, Transform, Load ») via SQL Data Warehouse. Pour une présentation complète d’Azure Data Factory, consultez la [documentation Azure Data Factory].[Azure Data Factory documentation].

## <a name="data-movement"></a>Déplacement des données
Azure Data Factory permet le déplacement des données entre des sources locales, mais également entre divers services Microsoft Azure.  De manière générale, le processus d’intégration actuel avec Microsoft Azure Data Factory prend en charge le déplacement de données depuis et vers les emplacements suivants :

* Stockage des objets blob
* Base de données SQL Azure
* SQL Server local
* SQL Server sur IaaS

Pour plus d’informations sur la configuration d’une activité de copie de données, consultez [Copie de données avec Azure Data Factory][Copy data with Azure Data Factory].

## <a name="stored-procedures"></a>Procédures stockées
 De la même manière qu’il peut permettre de planifier le transfert de données, Azure Data Factory peut être utilisé pour orchestrer l’exécution des procédures stockées.  Cela vous permet de créer des pipelines plus complexes et d’étendre la capacité de Microsoft Azure Data Factory à valoriser la puissance de calcul de SQL Data Warehouse.

## <a name="next-steps"></a>Étapes suivantes
Pour une vue d’ensemble de l’intégration, consultez [Vue d’ensemble sur l’intégration de SQL Data Warehouse][SQL Data Warehouse integration overview].
Pour obtenir des conseils supplémentaires en matière de développement, consultez l’article [Vue d’ensemble sur le développement SQL Data Warehouse][SQL Data Warehouse development overview].

<!--Image references-->

<!--Article references-->

[Copy data with Azure Data Factory]: ../data-factory/data-factory-data-movement-activities.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory documentation]:https://azure.microsoft.com/documentation/services/data-factory/


