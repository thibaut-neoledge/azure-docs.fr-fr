---
title: "Développer des solutions intégrées avec SQL Data Warehouse | Microsoft Docs"
description: "Outils et partenaires proposant des solutions s’intégrant avec SQL Data Warehouse. "
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: e2dc8f3f-10e3-4589-a4e2-50c67dfcf67f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a4de014f80bb7acfc91fb0c2f4c36fa659463db4


---
# <a name="leverage-other-services-with-sql-data-warehouse"></a>Tirer parti d’autres services avec SQL Data Warehouse
Outre ses fonctionnalités principales, SQL Data Warehouse permet aux utilisateurs de tirer parti de nombreux autres services dans Azure.  Plus précisément, nous avons pris des mesures pour l’intégrer étroitement aux éléments suivants :

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Nous travaillons à une intégration à un certain nombre d’autres services dans l’écosystème Azure.

## <a name="power-bi"></a>Power BI
L’intégration de Power BI vous permet de tirer parti de la puissance de calcul de SQL Data Warehouse avec la création de rapports dynamiques et la visualisation de Power BI. L’intégration de Power BI inclut actuellement les éléments suivants :

* **Connexion directe**: une connexion plus avancée avec un menu déroulant logique dans SQL Data Warehouse.  Ainsi, les analyses sont plus rapides à une plus grande échelle.
* **Ouvrir dans Power BI**: ce bouton transmet les informations d’instance à Power BI, permettant une connexion plus fluide.

Pour plus d’informations, consultez [Intégrer à Power BI](sql-data-warehouse-integrate-power-bi.md) ou la [documentation Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx).

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory offre aux utilisateurs une plateforme gérée pour créer des pipelines d’extraction-chargement complexes.  L’intégration de SQL Data Warehouse à Azure Data Factory inclut les éléments suivants :

* **Procédures stockées**: orchestrez l’exécution de procédures stockées dans SQL Data Warehouse.
* **Copy**: utilisez ADF pour déplacer les données dans SQL Data Warehouse.  Cette opération peut utiliser le mécanisme de déplacement de données standard d’ADF ou PolyBase en arrière-plan. 

Pour plus d’informations, consultez [Intégrer à Azure Data Factory](sql-data-warehouse-integrate-azure-data-factory.md) ou la [documentation Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning est un service d’analyse entièrement géré qui permet aux utilisateurs de créer des modèles complexes exploitant un large ensemble d’outils prédictifs.  SQL Data Warehouse est pris en charge à la fois comme source et destination de ces modèles avec les fonctionnalités suivantes :

* **Lire les données :** pilotez des modèles à l’échelle à l’aide de T-SQL dans SQL Data Warehouse.
* **Écrire des données :** validez les modifications d’un modèle dans SQL Data Warehouse.

Pour plus d’informations, consultez [Intégrer à Azure Machine Learning](sql-data-warehouse-integrate-azure-machine-learning.md) ou la [documentation Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics est une infrastructure complexe, entièrement gérée pour le traitement et l’utilisation des données d’événement générées à partir d’Azure Event Hubs.  L’intégration à SQL Data Warehouse permet de traiter efficacement et de stocker les données de diffusion en continu avec des données relationnelles, ce qui permet une analyse plus approfondie et plus avancée.  

* **Sortie de la tâche :** envoyez une sortie des tâches Stream Analytics directement à SQL Data Warehouse.

Pour plus d’informations, consultez [Intégrer à Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md) ou la [documentation Azure Stream Analytics](https://azure.microsoft.com/documentation/services/stream-analytics/).

<!--Image references-->

<!--Article references-->
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partenaires]: sql-data-warehouse-partner-business-intelligence.md

<!--MSDN references-->

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


