---
title: "Guides de la science des données SQL Server avec R, Python et T-SQL | Microsoft Docs"
description: "Exemples qui montrent comment utiliser R, Python et T-SQL dans SQL Server pour effectuer des analyses prédictives."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 333fd4ee6dcdcbfd347d6b5e8cb900474f6ec8cc
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---

# <a name="sql-server-data-science-walkthroughs-using-r-python-and-t-sql"></a>Guides de la science des données SQL Server avec R, Python et T-SQL

Ces guides utilisent SQL Server, SQL Server R Services et SQL Server Python Services pour effectuer des analyses prédictives. Le code R et Python est déployé dans des procédures stockées. Ils suivent les étapes décrites dans le processus TDSP (Team Data Science Process). Vous trouverez une vue d’ensemble du processus TDSP sur la page [Processus de science des données](data-science-process-overview.md) 

D’autres guides de la science des données qui appliquent le processus TDSP sont regroupés en fonction de la **plateforme** qu’ils utilisent : 

[!INCLUDE [tdsp-walkthroughs-by-platform](../../includes/tdsp-walkthroughs-by-platform.md)]


## <a name="predict-taxi-tips-using-python-and-sql-queries-with-sql-server"></a>Prédire les pourboires laissés aux taxis avec Python, des requêtes SQL et SQL Server 

Le guide [Utiliser SQL Server](machine-learning-data-science-process-sql-walkthrough.md) explique comment générer et déployer des modèles Machine Learning de classification et de régression avec SQL Server et un jeu de données public de courses et de tarifs des taxi new-yorkais.


## <a name="predict-taxi-tips-using-microsoft-r-with-sql-server"></a>Prédire les pourboires laissés aux taxis avec Microsoft R et SQL Server 

La procédure pas à pas [Utiliser SQL Server R Services](https://msdn.microsoft.com/library/mt612857.aspx) fournit aux chercheurs de données une combinaison de codes R, de données SQL Server et de fonctions SQL personnalisées pour générer et déployer un modèle R sur SQL Server. Le guide est conçu pour présenter R Services aux développeurs R (dans la base de données).


## <a name="predict-taxi-tips-using-r-from-t-sql-or-stored-procedures-with-sql-server"></a>Prédire les pourboires laissés aux taxis avec R à partir de T-SQL ou de procédures stockées avec SQL Server

Le [Guide de la science des données pour R et SQL Server](https://docs.microsoft.com/en-us/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough) offre aux programmeurs SQL une expérience de génération d’une solution d’analytique avancée avec Transact-SQL et SQL Server R Services pour opérationnaliser une solution R. 


## <a name="predict-taxi-tips-using-python-in-sql-server-stored-procedures"></a>Prédire les pourboires laissés aux taxis avec Python dans les procédures stockées SQL Server

La procédure pas à pas [Utiliser T-SQL with SQL Server Python Services ](https://docs.microsoft.com/en-us/sql/advanced-analytics/tutorials/sqldev-in-database-python-for-sql-developers) fournit aux programmeurs SQL une expérience leur permettant de générer une solution machine learning dans SQL Server. Elle montre comment intégrer Python dans une application en ajoutant un code Python dans des procédures stockées.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les composants clés qui constituent le processus TDSP (Team Data Science Process), consultez la page [Vue d’ensemble du processus TDSP (Team Data Science Process)](data-science-process-overview.md).

Pour en savoir plus sur le cycle de vie du processus TDSP (Team Data Science Process), qui permet de structurer les projets de science des données, consultez la page [Cycle de vie du processus TDSP (Team Data Science Process)](data-science-process-lifecycle.md). Le cycle de vie présente les étapes, du début à la fin, que les projets suivent généralement quand ils sont exécutés. 
