---
title: "Interroger votre index de Recherche Azure à l’aide du portail Azure │ Microsoft Docs"
description: "Émettez une requête de recherche dans l’Explorateur de recherche du portail Azure."
services: search
manager: jhubbard
documentationcenter: 
author: ashmaka
ms.assetid: 8e524188-73a7-44db-9e64-ae8bf66b05d3
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a23372112e17703a3399e1bdc9eaf73b85a1f80d


---
# <a name="query-your-azure-search-index-using-the-azure-portal"></a>Interroger votre index Azure Search à l’aide du portail Azure
> [!div class="op_single_selector"]
> * [Vue d'ensemble](search-query-overview.md)
> * [Portail](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Ce guide vous explique comment interroger l’index Azure Search dans le portail Azure.

Avant de commencer cette procédure, vous devez déjà avoir [créé un index de Recherche Azure](search-what-is-an-index.md) et y avoir [ajouté des données](search-what-is-data-import.md).

## <a name="i-go-to-your-azure-search-blade"></a>I. Accéder au panneau Azure Search
1. Cliquez sur « Toutes les ressources » dans le menu de gauche du [portail Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Sélectionnez votre service Azure Search.

## <a name="ii-select-the-index-you-would-like-to-search"></a>II. Sélectionnez l’index dans lequel vous souhaitez effectuer la recherche
1. Sélectionnez l’index dans lequel vous souhaitez effectuer la recherche à partir de la mosaïque « Index ».

![](./media/search-explorer/pick-index.png)

## <a name="iii-click-on-the-search-explorer-tile"></a>III. Cliquez sur la mosaïque « Explorateur de recherche »
![](./media/search-explorer/search-explorer-tile.png)

## <a name="iii-start-searching"></a>III. Commencez la recherche
1. Pour effectuer une recherche dans l’index Azure Search, commencez à taper dans le champ «*Chaîne de requête*», puis appuyez sur «**Rechercher**».
   
   * Lorsque vous utilisez l’Explorateur de recherche, vous pouvez spécifier les [paramètres de requête](https://msdn.microsoft.com/library/dn798927.aspx)
2. Dans la section «*Résultats*», les résultats de la requête apparaîtront dans le fichier JSON brut qui vous aurait été adressé dans un corps de réponse HTTP si vous aviez lancé la requête de recherche dans l’API REST d’Azure Search.
3. La chaîne de requête est automatiquement analysée dans l’URL de requête appropriée pour soumettre une requête HTTP à l’API REST d’Azure Search

![](./media/search-explorer/search-bar.png)




<!--HONumber=Nov16_HO2-->


