---
title: "moreLikeThis dans la Recherche Azure (préversion) | Microsoft Docs"
description: "Documentation préliminaire pour la fonctionnalité moreLikeThis (préversion) exposée dans l’API REST Recherche Azure."
services: search
documentationCenter: na
authors: mhko
manager: jlembicz
editor: na
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: nateko
ms.openlocfilehash: 20f6a1166d47799f15275cb5b6a9ea4934ae57e2
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2017
---
# <a name="morelikethis-in-azure-search-preview"></a>moreLikeThis dans la Recherche Azure (préversion)

`moreLikeThis=[key]` est un paramètre de requête dans l’[API Recherche](https://docs.microsoft.com/rest/api/searchservice/search-documents). En spécifiant le paramètre `moreLikeThis` dans une requête de recherche, vous pouvez trouver des documents qui sont similaires au document spécifié par la clé de document. Lorsqu’une requête de recherche est formulée avec `moreLikeThis`, une demande est générée en utilisant les termes de recherche extraits du document donné et qui décrivent le mieux ce document. La demande générée est ensuite utilisée pour effectuer la requête de recherche. Par défaut, le contenu de tous les champs `searchable` est pris en compte, sauf si le paramètre `searchFields` est utilisé pour restreindre ces champs. Le paramètre `moreLikeThis` ne peut pas être utilisé avec le paramètre de recherche, `search=[string]`.

## <a name="examples"></a>Exemples 

Voici l’exemple d’une requête moreLikeThis. La requête recherche les documents dont les champs de description sont les plus proches du champ du document source, tel que spécifié par le paramètre `moreLikeThis`.

```  
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2016-09-01-Preview
```  

```  
POST /indexes/hotels/docs/search?api-version=2016-09-01-Preview
    {  
      "moreLikeThis": "1002",  
      "searchFields": "description"  
    }  
```  

## <a name="feature-availability"></a>Disponibilité des fonctionnalités

La fonctionnalité moreLikeThis est actuellement en préversion et uniquement prise en charge dans les versions d’API en préversion, `2015-02-28-Preview` et `2016-09-01-Preview`. Comme la version de l’API est spécifiée dans la requête, il est possible de combiner les API mises à la disposition générale (GA) et les API en version préliminaire disponibles dans la même application. Cependant, les API de versions préliminaires ne sont pas soumises à un contrat SLA, et les fonctionnalités peuvent changer ; par conséquent, nous ne recommandons pas de les utiliser dans des applications de production.