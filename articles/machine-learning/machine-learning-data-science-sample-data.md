---
title: "Échantillonner des données dans des conteneurs d’objets blob Azure, SQL Server et des tables Hive | Microsoft Docs"
description: "Comment explorer les données stockées dans différents environnements Azure."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 80a9dfae-e3a6-4cfb-aecc-5701cfc7e39d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: fashah;garye;bradsev
translationtype: Human Translation
ms.sourcegitcommit: a6bc79b2cb5b73109cddd6cf57caeba754b52e2e
ms.openlocfilehash: c046220304075242f655bced2f0f16a6fb692ad3


---
# <a name="a-nameheadingasample-data-in-azure-blob-containers-sql-server-and-hive-tables"></a><a name="heading"></a>Échantillonner des données dans des conteneurs d'objets blob Azure, SQL Server et des tables Hive
Ce document contient des liens vers des rubriques qui expliquent comment échantillonner les données stockées dans l’un des trois emplacements Azure :

* **données de conteneurs d’objets blob Azure** sont échantillonnées par le biais de leur téléchargement par programmation, puis de leur échantillonnage à l’aide d’un exemple de code Python.
* **données SQL Server** sont échantillonnées à l’aide de SQL et du langage de programmation Python. 
* **tables Hive** sont échantillonnées à l’aide de requêtes Hive.

Le **menu** ci-dessous dirige vers les rubriques qui expliquent comment échantillonner des données dans chacun de ces environnements de stockage Azure. 

[!INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Cette tâche d’échantillonnage est une étape du [processus TDSP (Team Data Science Process)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="why-sample-data"></a>Pourquoi échantillonner des données ?
Si vous prévoyez d’analyser un jeu de données volumineux, il est généralement recommandé de sous-échantillonner les données afin de réduire leur taille sous une forme plus facilement exploitable, mais toujours représentative. Cette opération facilite la compréhension et l’exploration des données, ainsi que la conception de fonctionnalités. Son rôle dans le processus Cortana Analytics consiste à permettre le prototypage rapide des fonctions de traitement des données et des modèles d’apprentissage automatique.




<!--HONumber=Dec16_HO3-->


