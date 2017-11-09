---
title: "Didacticiel de distribution mondiale Azure Cosmos DB pour l’API Table | Microsoft Docs"
description: "Découvrez comment configurer la distribution mondiale Azure Cosmos DB à l’aide de l’API Table."
services: cosmos-db
keywords: distribution mondiale, Table
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: mimig
ms.openlocfilehash: e9cb4b5b886cec46c0483287460c720855867f38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-table-api"></a>Comment configurer la distribution mondiale Azure Cosmos DB à l’aide de l’API Table

Dans cet article, nous allons vous montrer comment utiliser le portail Azure pour configurer la distribution mondiale Azure Cosmos DB avant d’établir une connexion à l’aide de l’API Table (version préliminaire).

Cet article décrit les tâches suivantes : 

> [!div class="checklist"]
> * Configurer la distribution mondiale à l’aide du portail Azure
> * Configurer la distribution mondiale à l’aide de [l’API Table](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Se connecter à une région de prédilection avec l’API Table

Pour tirer parti de la [distribution mondiale](distribute-data-globally.md), les applications clientes peuvent spécifier la liste ordonnée de préférences de régions à utiliser pour effectuer des opérations sur les documents. Pour ce faire, définissez la valeur de configuration `TablePreferredLocations` dans la configuration de l’application de la version préliminaire du kit de développement logiciel Azure Storage. Selon la configuration du compte Azure Cosmos DB, la disponibilité régionale actuelle et la liste de préférences spécifiée, le Kit de développement logiciel (SDK) Azure Storage choisit le point de terminaison optimal pour les opérations de lecture et d’écriture.

Le paramètre `TablePreferredLocations` doit contenir une liste séparée par des virgules des emplacements (de multihébergement) préférés pour les lectures. Chaque instance de client peut spécifier un sous-ensemble de ces régions dans l’ordre de préférence pour des lectures à faible latence. Les régions doivent être nommées à l’aide de leurs [noms d’affichage](https://msdn.microsoft.com/library/azure/gg441293.aspx), par exemple, `West US`.

Toutes les lectures sont envoyées vers la première région disponible dans la liste `TablePreferredLocations`. Si la demande échoue, le client passe à la région suivante dans la liste et ainsi de suite.

Le SDK tente des opérations de lecture uniquement à partir des régions spécifiées dans `TablePreferredLocations`. Ainsi, par exemple, si le compte de base de données est disponible dans trois régions, mais que le client spécifie uniquement deux des régions sans écriture de `TablePreferredLocations`, aucune lecture n’est traitée hors de la région d’écriture, même en cas de basculement.

Le SDK envoie automatiquement toutes les écritures vers la région d’écriture en cours.

Si la propriété `TablePreferredLocations` n’est pas définie, toutes les demandes seront traitées par la zone d’écriture en cours.

```xml
    <appSettings>
      <add key="TablePreferredLocations" value="East US, West US, North Europe"/>           
    </appSettings>
```

C’est ici que s’achève ce didacticiel. Découvrez comment gérer la cohérence de votre compte répliqué à l’échelle mondiale en lisant l’article [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md). Pour plus d’informations sur le fonctionnement de la réplication de base de données à l’échelle mondiale dans Azure Cosmos DB, voir [Diffuser des données à l’échelle mondiale avec Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez effectué les tâches suivantes :

> [!div class="checklist"]
> * Configurer la distribution mondiale à l’aide du portail Azure
> * Configurer la distribution mondiale à l’aide des API DocumentDB

Vous pouvez maintenant passer au didacticiel suivant pour apprendre à développer en local à l’aide de l’émulateur local Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Développer en local avec l’émulateur](local-emulator.md)