---
title: "Chargement de données dans Azure Search | Microsoft Docs"
description: "Découvrez comment charger des données dans un index dans Azure Search."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: 
ms.assetid: aa8d47c1-4ae6-4209-a8ce-48d5a9474707
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: ashmaka
ms.openlocfilehash: 5a601b75ec67824e72d8736bc3c45f8e1231ca86
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="upload-data-to-azure-search"></a>Charger des données dans Azure Search
> [!div class="op_single_selector"]
> * [Vue d'ensemble](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

Deux méthodes permettent de renseigner votre index avec vos données. La première option consiste à envoyer manuellement vos données dans l’index à l’aide de l’[API REST](search-import-data-rest-api.md) ou du [Kit de développement logiciel (SDK) .NET](search-import-data-dotnet.md) d’Azure Search. La seconde option consiste à [pointer une source de données prise en charge](search-indexer-overview.md) vers votre index et à permettre à Azure Search d’extraire automatiquement vos données.

## <a name="push-data-to-an-index"></a>Envoyer des données à un index
Cette approche désigne l’envoi par programme de vos données à Azure Search pour les rendre disponibles pour la recherche. Pour les applications ayant des exigences à très faible latence (par exemple, si vous devez synchroniser les opérations de recherche avec les bases de données d’inventaire dynamiques), le modèle d’émission est la seule option.

Vous pouvez utiliser l’[API REST](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) ou le [Kit de développement logiciel (SDK) .NET](search-import-data-dotnet.md) pour envoyer des données à un index. Il n’existe actuellement aucune prise en charge de l’outil de diffusion de données via le portail.

Cette approche est plus flexible que le modèle d’extraction, car vous pouvez charger des documents individuellement ou par lots (jusqu’à 1 000 par lot ou 16 Mo, quelle que soit la limite atteinte en premier). Le modèle d’émission vous permet également de charger des documents dans Azure Search indépendamment de l’emplacement des données.

Le format de données interprété par Azure Search est JSON, et tous les documents dans le jeu de données doivent avoir des champs correspondant aux champs définis dans votre schéma d’index. 

## <a name="pull-data-into-an-index"></a>Extraire des données dans un index
Le modèle d’extraction analyse une source de données prise en charge et charge automatiquement les données dans votre index. Dans Azure Search, cette fonctionnalité est implémentée via des *indexeurs*, actuellement disponibles pour le [stockage d’objets blob](search-howto-indexing-azure-blob-storage.md), le [stockage de tables](search-howto-indexing-azure-tables.md), [Azure Cosmos DB](http://aka.ms/documentdb-search-indexer) et la [base de données SQL Azure et SQL Server sur les machines virtuelles Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md). 

Les indexeurs connectent un index à une source de données (généralement une table, une vue ou une structure équivalente) et mappent les champs source aux champs équivalents de l’index. Pendant l’exécution, l’ensemble de lignes est automatiquement transformé en JSON et chargé dans l’index spécifié. Tous les indexeurs prennent en charge la planification de sorte que vous puissiez spécifier la fréquence à laquelle les données sont à actualiser. La plupart des indexeurs fournissent le suivi des modifications si la source de données le prend en charge. En suivant les modifications et les suppressions effectuées dans les documents existants, et en reconnaissant les nouveaux documents, les indexeurs suppriment la nécessité de gérer activement les données de votre index. 

La fonctionnalité de l’indexeur est exposée dans le [Portail Azure](search-import-data-portal.md), ainsi que dans [l’API REST](/rest/api/searchservice/Indexer-operations) et le [Kit de développement logiciel (SDK) .NET](/dotnet/api/microsoft.azure.search.indexersoperations). 

L’avantage du portail est qu’Azure Search peut générer un schéma d’index par défaut pour vous en lisant les métadonnées du jeu de données source. Vous pouvez modifier l’index généré jusqu’à ce que l’index soit traité, après quoi les seules modifications de schéma autorisées sont celles qui ne nécessitent pas la réindexation. Si les modifications que vous souhaitez apporter impactent directement le schéma, vous devez reconstruire l’index. 

Une fois l’index rempli, vous pouvez utiliser **l’Explorateur de recherche** dans la barre de commandes du portail comme une étape de vérification.

## <a name="query-an-index-using-search-explorer"></a>Interrogation d’un index à l’aide de l’Explorateur de recherche

Un moyen rapide d’effectuer une vérification préliminaire sur le téléchargement de document consiste à utiliser **l’Explorateur de recherche** dans le portail. L’Explorateur vous permet d’interroger un index sans avoir à écrire du code. L’expérience de recherche est basée sur les paramètres par défaut, tels que la [syntaxe simple](/rest/api/searchservice/simple-query-syntax-in-azure-search) et le [paramètre de requête searchMode](/rest/api/searchservice/search-documents). Les résultats sont retournés au format JSON afin que vous puissiez inspecter le document dans son intégralité.

> [!TIP]
> De nombreux [exemples de code Azure Search](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) incluent des jeux de données incorporés ou disponibles rapidement, offrant ainsi une prise en main simplifiée. Le portail fournit également un exemple d’indexeur et de source de données composée d’un petit jeu de données immobilières (nommé « realestate-us-sample »). Lorsque vous exécutez l’indexeur préconfiguré sur l’exemple de source de données, un index est créé et chargé avec des documents qui peuvent ensuite être interrogés dans l’Explorateur de recherche ou par le code que vous écrivez.