---
title: "Didacticiel de version préliminaire des synonymes dans la Recherche Azure | Microsoft Docs"
description: "Ajoutez la fonctionnalité de version préliminaire des synonymes à un index dans la Recherche Azure."
services: search
manager: jhubbard
documentationcenter: 
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 03/31/2017
ms.author: heidist
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 014959ed471f796d2184f0f8ff10d15cdc8a2ec6
ms.contentlocale: fr-fr
ms.lasthandoff: 04/17/2017

---
# <a name="synonym-preview-c-tutorial-for-azure-search"></a>Didacticiel C# des synonymes (version préliminaire) pour la Recherche Azure

Les synonymes développent une requête en faisant correspondre les termes considérés comme sémantiquement équivalents à l’expression entrée. Par exemple, vous souhaiterez peut-être que le terme « voiture » vous permette de trouver des documents contenant les mots « automobile » ou « véhicule ».

Dans la Recherche Azure, les synonymes sont définis dans une *carte de synonymes*, via des *règles de mappage* qui associent des termes équivalents. Vous pouvez créer plusieurs cartes de synonymes, les valider en tant que ressources du service disponible pour tout index, et ensuite référencer ceux que vous souhaitez utiliser au niveau du champ. Au moment de la requête, outre la recherche dans un index, la Recherche Azure effectue une recherche dans une carte de synonymes, si une carte est spécifiée dans les champs utilisés dans la requête.

> [!NOTE]
> La fonctionnalité de synonymes est actuellement en version préliminaire et prise en charge uniquement dans les dernières versions d’API et de Kit de développement logiciel (SDK) préliminaires (api-version=2016-09-01-Preview, SDK version 4.x-preview). Il n’existe aucune prise en charge sur le portail Azure pour l’instant. Les API de versions préliminaires ne sont pas soumises à un contrat SLA, et les fonctionnalités peuvent changer ; par conséquent, nous ne recommandons pas de les utiliser dans des applications de production.

## <a name="prerequisites"></a>Composants requis

La configuration requise du didacticiel est la suivante :

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Service Recherche Azure](search-create-service-portal.md)
* [Version préliminaire de la bibliothèque Microsoft.Azure.Search .NET](https://aka.ms/search-sdk-preview)
* [Comment utiliser la Recherche Azure à partir d’une application .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>Vue d'ensemble

Les requêtes avant et après présentent la valeur des synonymes. Dans ce didacticiel, nous utilisons un exemple d’application qui exécute des requêtes et retourne des résultats sur un index d’exemples. L’exemple d’application crée un petit index nommé « hotels » comprenant deux documents. L’application exécute des requêtes de recherche à l’aide de termes et d’expressions qui n’apparaissent pas dans l’index, active la fonctionnalité de synonymes, puis lance les mêmes recherches une nouvelle fois. Le code ci-dessous montre le flux global.

```csharp
  static void Main(string[] args)
  {
      SearchServiceClient serviceClient = CreateSearchServiceClient();

      Console.WriteLine("{0}", "Cleaning up resources...\n");
      CleanupResources(serviceClient);

      Console.WriteLine("{0}", "Creating index...\n");
      CreateHotelsIndex(serviceClient);

      ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

      Console.WriteLine("{0}", "Uploading documents...\n");
      UploadDocuments(indexClient);

      ISearchIndexClient indexClientForQueries = CreateSearchIndexClient();

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Adding synonyms...\n");
      UploadSynonyms(serviceClient);
      EnableSynonymsInHotelsIndex(serviceClient);
      Thread.Sleep(10000); // Wait for the changes to propagate

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");

      Console.ReadKey();
  }
```
Les étapes pour créer et remplir l’index des exemples sont expliquées dans [Comment utiliser la Recherche Azure à partir d’une application .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

## <a name="before-queries"></a>Requêtes « avant »

Dans `RunQueriesWithNonExistentTermsInIndex`, nous lançons des requêtes de recherche avec « five star », « internet » et « economy AND hotel ».
```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = indexClient.Documents.Search<Hotel>("\"five star\"", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = indexClient.Documents.Search<Hotel>("internet", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = indexClient.Documents.Search<Hotel>("economy AND hotel", parameters);
WriteDocuments(results);
```
Aucun des deux documents indexés ne contient les termes, nous avons donc la sortie suivante à partir du premier `RunQueriesWithNonExistentTermsInIndex`.
~~~
Search the entire index for the phrase "five star":

no document matched

Search the entire index for the term 'internet':

no document matched

Search the entire index for the terms 'economy' AND 'hotel':

no document matched
~~~

## <a name="enable-synonyms"></a>Activation des synonymes

L’activation des synonymes est un processus en deux étapes. Tout d’abord nous définissons et chargeons les règles de synonymes, puis nous configurons les champs pour les utiliser. Le processus est décrit dans `UploadSynonyms` et `EnableSynonymsInHotelsIndex`.

1. Ajoutez une carte de synonymes à votre service de recherche. Dans `UploadSynonyms`, nous définissons quatre règles de notre carte de synonymes « desc-synonymmap » et effectuons le téléchargement vers le service.
```csharp
    var synonymMap = new SynonymMap()
    {
        Name = "desc-synonymmap",
        Format = "solr",
        Synonyms = "hotel, motel\n
                    internet,wifi\n
                    five star=>luxury\n
                    economy,inexpensive=>budget"
    };

    serviceClient.SynonymMaps.CreateOrUpdate(synonymMap);
```
Une carte de synonymes doit être conforme au format `solr` standard Open Source. Le format est expliqué dans [Synonyms in Azure Search (Synonymes dans la Recherche Azure)](search-synonyms.md) sous la section `Apache Solr synonym format`.

2. Configurez les champs pouvant faire l’objet d’une recherche pour utiliser la carte de synonymes dans la définition d’index. Dans `EnableSynonymsInHotelsIndex`, nous activons les synonymes sur deux champs `category` et `tags` en affectant à la propriété `synonymMaps` le nom de la carte de synonymes qui vient d’être téléchargée.
```csharp
  Index index = serviceClient.Indexes.Get("hotels");
  index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
  index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };

  serviceClient.Indexes.CreateOrUpdate(index);
```
Lorsque vous ajoutez une carte de synonymes, les reconstructions d’index ne sont pas requises. Vous pouvez ajouter une carte de synonymes à votre service, puis modifier les définitions de champ existantes dans n’importe quel index pour utiliser la nouvelle carte de synonymes. L’ajout de nouveaux attributs n’a aucun impact sur la disponibilité de l’index. Il en va de même pour la désactivation de synonymes pour un champ. Vous pouvez simplement affecter à la propriété `synonymMaps` une liste vide.
```csharp
  index.Fields.First(f => f.Name == "category").SynonymMaps = new List<string>();
```

## <a name="after-queries"></a>Requêtes « après »

Une fois que la carte de synonymes est téléchargée et l’index mis à jour pour utiliser la carte de synonymes, le deuxième appel `RunQueriesWithNonExistentTermsInIndex` affiche les éléments suivants :

~~~
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
~~~
La première requête trouve le document à partir de la règle `five star=>luxury`. La deuxième requête étend la recherche à l’aide de `internet,wifi` et la troisième avec à la fois `hotel, motel` et `economy,inexpensive=>budget` pour trouver les documents en correspondance.

L’ajout de synonymes modifie complètement l’expérience de recherche. Dans ce didacticiel, les requêtes d’origine n’ont pas pu retourner de résultats significatifs même si les documents dans notre index étaient pertinents. En activant les synonymes, nous pouvons développer un index pour inclure les termes communément utilisés, sans modification de données sous-jacentes dans l’index.

## <a name="sample-application-source-code"></a>Code source de l'exemple d'application
Vous trouverez le code source complet de l’exemple d’application utilisé dans cette procédure sur [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="next-steps"></a>Étapes suivantes

* Consultez [How to use synonyms in Azure Search (Comment utiliser les synonymes dans la Recherche Azure)](search-synonyms.md).
* Consultez la [documentation sur l’API REST de synonymes](https://aka.ms/rgm6rq).
* Parcourez les références relatives au [Kit de développement logiciel (SDK) .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) et à [l’API REST](https://docs.microsoft.com/rest/api/searchservice/).

