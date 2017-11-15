---
title: Prise en charge de Stockage Table Azure dans Azure Cosmos DB | Microsoft Docs
description: "Découvrez comment collaborent l’API Table Azure Cosmos DB et Stockage Table Azure."
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: 378f00f2-cfd9-4f6b-a9b1-d1e4c70799fd
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: 1513fc53501f1cfec93134841fbef9a8552dd43c
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2017
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Développement avec l’API Table Azure Cosmos DB et Stockage Table Azure

L’API Table Azure Cosmos DB et Stockage Table Azure partagent le même modèle de données de table et exposent les mêmes opérations de création, suppression, mise à jour et interrogation par le biais de leurs SDK. 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Développement avec l’API Table Azure Cosmos DB

À ce stade, [l’API Table Azure Cosmos DB](table-introduction.md) a un SDK .NET disponible, à savoir [Stockage Table Premium Microsoft Azure (préversion)](https://aka.ms/premiumtablenuget). Cette bibliothèque présente les mêmes classes et signatures de méthode que le [SDK Stockage Microsoft Azure](https://www.nuget.org/packages/WindowsAzure.Storage) public, mais elle peut également se connecter à des comptes Azure Cosmos DB à l’aide de l’API Table (préversion). Pour un démarrage rapide et un didacticiel utilisant l’API Table Azure Cosmos DB, consultez les articles suivants :
- Démarrage rapide : [Azure Cosmos DB : Créer une application .NET à l’aide de l’API Table](create-table-dotnet.md)
- Didacticiel : [Azure Cosmos DB : développer avec l’API Table dans .NET](tutorial-develop-table-dotnet.md)

Des informations supplémentaires sur l’utilisation de l’API Table sont disponibles dans l’article [FAQ : Développer avec l’API Table](faq.md#develop-with-the-table-api-preview).

## <a name="developing-with-the-azure-table-storage"></a>Développement avec Stockage Table Azure

[Stockage Table Azure](table-storage-overview.md) met à votre disposition de nombreux SDK et didacticiels, qui se trouvent désormais dans la section [Stockage Table Azure](table-storage-overview.md). Ces articles sont mis à jour à mesure que l’interopérabilité entre les SDK Stockage Table Azure et les API Table Azure Cosmos DB devient disponible.  





