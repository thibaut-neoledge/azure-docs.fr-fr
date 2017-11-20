---
title: "Importer des données pour les utiliser avec l’API Table Azure Cosmos DB | Microsoft Docs"
description: "Découvrez comment importer des données à utiliser avec l’API Table Azure Cosmos DB."
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: b60743e2-0227-43ab-965a-0ae3ebacd917
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 984510b2dae99849507953163f94ad2f925651cd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="import-data-for-use-with-the-azure-cosmos-db-table-api"></a>Importer des données à utiliser avec l’API Table Azure Cosmos DB

Ce didacticiel fournit des instructions sur l’importation de données à utiliser avec [l’API Table](table-introduction.md) Azure Cosmos DB. Si vous avez des données stockées dans le stockage Table Azure, vous pouvez utiliser l’outil de migration de données ou azCopy pour importer vos données dans Azure Cosmos DB. Une fois vos données importées, vous pouvez tirer parti des fonctionnalités Premium d’Azure Cosmos DB, comme la distribution mondiale clé en main, le débit dédié, les latences de quelques millisecondes au 99e centile, la garantie de haute disponibilité et l’indexation automatique secondaire.

Ce didacticiel décrit les tâches suivantes :

> [!div class="checklist"]
> * Importation de données avec l’outil de migration de données
> * Importation de données avec AzCopy

## <a name="data-migration-tool"></a>Outil de migration de données

L’outil de migration de données d’Azure Cosmos DB est une des options qui vous permet d’importer vos données de stockage Table Azure existantes. Pour importer des données à l’aide de l’outil, sélectionnez le stockage Table Azure comme source et l’API de table Azure Cosmos DB comme cible, puis fournissez le reste des informations demandées dans l’outil. Les données peuvent être exportées en bloc ou à l’aide de l’importation d’enregistrement séquentiel. 

Vous trouverez des informations sur la définition du stockage Table Azure comme source et de l’API de table comme cible dans les sections suivantes :
- [Utilisation du Stockage Table Azure comme source de migration de données](import-data.md#AzureTableSource). 
- [Exporter vers l’API Table avec l’importation en bloc](import-data.md#tableapibulkexport)
- [Exporter vers l’API Table avec l’importation d’enregistrement séquentiel](import-data.md#tableapiseqtarget).

## <a name="azcopy-command"></a>Commande AzCopy

L’utilitaire de ligne de commande AzCopy est l’autre option qui permet de migrer les données à partir du stockage Table Azure dans l’API Table Azure Cosmos DB. Pour utiliser AzCopy, vous devez d’abord exporter vos données dans un fichier manifeste comme décrit dans [Exporter des données à partir du stockage Table](../storage/common/storage-use-azcopy.md#export-data-from-table-storage), puis importer les données à partir du fichier manifeste dans [l’API Table Azure Cosmos DB](../storage/common/storage-use-azcopy.md#import-data-into-table-storage).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Importer des données avec l’outil de migration de données
> * Importer des données avec AzCopy

Vous pouvez maintenant passer au didacticiel suivant et découvrir comment interroger les données à l’aide de l’API Table Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Comment interroger les données ?](../cosmos-db/tutorial-query-table.md)



