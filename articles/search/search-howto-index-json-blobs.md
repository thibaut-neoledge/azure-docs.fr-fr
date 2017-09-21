---
title: "Indexation d’objets blob JSON avec l’indexeur d’objets blob Recherche Azure"
description: "Indexation d’objets blob JSON avec l’indexeur d’objets blob Recherche Azure"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 57e32e51-9286-46da-9d59-31884650ba99
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 09/07/2017
ms.author: eugenesh
ms.translationtype: HT
ms.sourcegitcommit: 9b7316a5bffbd689bdb26e9524129ceed06606d5
ms.openlocfilehash: bf4d3a517e1308a142d21cffff64f3c6e104eb62
ms.contentlocale: fr-fr
ms.lasthandoff: 09/08/2017

---

# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indexation d’objets blob JSON avec l’indexeur d’objets blob Recherche Azure
Cet article explique comment configurer l’indexeur d’objets blob Recherche Azure pour extraire le contenu structuré à partir d’objets blob contenant JSON.

## <a name="scenarios"></a>Scénarios
Par défaut, [l’indexeur d’objets blob Recherche Azure](search-howto-indexing-azure-blob-storage.md) analyse les objets blob JSON comme un bloc de texte unique. Vous souhaitez généralement conserver la structure de vos documents JSON. Par exemple, dans le document JSON

    {
        "article" : {
             "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

vous pouvez analyser les champs « text », « datePublished » et « tags » dans un document Recherche Azure.

Vous pouvez également, lorsque vos objets blob contiennent un **tableau d’objets JSON**, séparer chaque élément du tableau en un document Recherche Azure. Par exemple, un objet blob avec ce JSON :  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Vous pouvez remplir l’index Recherche Azure avec trois documents distincts, chacun avec des champs « id » et « text ».

> [!IMPORTANT]
> Pour l’instant, la fonctionnalité d’analyse de tableau JSON n’existe qu’en préversion. Elle est uniquement disponible dans l’API REST en utilisant la version **2016-09-01-Preview**. N’oubliez pas que les API d’évaluation sont destinées à être utilisées à des fins de test et d’évaluation, et non dans les environnements de production.
>
>

## <a name="setting-up-json-indexing"></a>Configuration de l’indexation JSON
L’indexation d’objets blob JSON est similaire à l’extraction de documents standard. Commencez par créer la source de données comme d’habitude : 

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

Ensuite, créez l’index de recherche cible si vous n’en avez pas. 

Enfin, créez un indexeur et définissez le paramètre `parsingMode` sur `json` (pour indexer chaque objet blob en tant que document unique) ou `jsonArray` (si vos objets blob contiennent des tableaux JSON et que vous voulez traiter chaque élément de tableau comme un document distinct) :

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Si nécessaire, utilisez les **mappages de champ** pour sélectionner les propriétés du document JSON source utilisé pour renseigner votre index de recherche cible, comme décrit dans la section suivante.

> [!IMPORTANT]
> Lorsque vous utilisez le mode d’analyse `json` ou `jsonArray`, Recherche Azure suppose que tous les objets blob dans votre source de données contiennent JSON. Si vous devez prendre en charge une combinaison d’objets blob JSON et autres dans la même source de données, faites-le nous savoir sur [notre site UserVoice](https://feedback.azure.com/forums/263029-azure-search).
>
>

## <a name="using-field-mappings-to-build-search-documents"></a>Utilisation des mappages de champ pour créer des documents de recherche
Actuellement, Recherche Azure ne peut pas indexer des documents JSON arbitraires directement, car il ne prend en charge que les types de données primitifs, les tableaux de chaînes et les points GeoJSON. Toutefois, vous pouvez utiliser les **mappages de champ** pour sélectionner des parties de votre document JSON et les intégrer dans les champs de niveau supérieur du document de recherche. Pour découvrir les bases des mappages de champs, consultez [Les mappages de champs de l’indexeur Recherche Azure comblent les différences entre les sources de données et les index de recherche](search-indexer-field-mappings.md).

Revenons à notre exemple de document JSON :

    {
        "article" : {
             "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Imaginons que vous disposez d’un index de recherche avec les champs suivants : `text` de type `Edm.String`, `date` de type `Edm.DateTimeOffset` et `tags` de type `Collection(Edm.String)`. Pour mapper votre document JSON à la forme souhaitée, utilisez les mappages de champ suivants :

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Les noms de champ source dans les mappages sont spécifiés selon la notation de [pointeur JSON](http://tools.ietf.org/html/rfc6901) . Vous débutez par une barre oblique pour faire référence à la racine de votre document JSON, puis sélectionnez la propriété souhaitée (au niveau arbitraire de l’imbrication) en utilisant un chemin d’accès séparé par des barres obliques avant.

Vous pouvez également faire référence à des éléments de tableau en utilisant un index de base zéro. Par exemple, pour sélectionner le premier élément du tableau « tags » dans l’exemple ci-dessus, utilisez un mappage de champ similaire au suivant :

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Si un nom de champ source dans un chemin de mappage de champ fait référence à une propriété qui n’existe pas dans JSON, ce mappage est ignoré sans erreur. Cela nous permet de prendre en charge les documents avec un schéma différent (cas fréquent). Comme il n’y a aucune validation, vous devez veiller à éviter les fautes de frappe dans la spécification du mappage de champ.
>
>

Si vos documents JSON ne contiennent que des propriétés de niveau supérieur, il se peut que vous n’ayez pas besoin d’aucun mappage de champ. Par exemple, si votre document JSON ressemble à ceci, les propriétés de niveau supérieur « text », « datePublished » et « tags » sont mappées directement aux champs correspondants dans l’index de recherche :

    {
       "text" : "A hopefully useful article explaining how to parse JSON blobs",
       "datePublished" : "2016-04-13"
       "tags" : [ "search", "storage", "howto" ]    
     }

Voici une charge utile d’indexeur complète avec les mappages de champs :

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }

## <a name="indexing-nested-json-arrays"></a>Indexation de tableaux JSON imbriqués
Que se passe-t-il si vous souhaitez indexer un tableau d’objets JSON, mais que ce tableau est imbriqué quelque part dans le document ? Vous pouvez choisir la propriété qui contient le tableau à l’aide de la propriété de configuration `documentRoot` . Par exemple, si vos objets blob ressemblent à ceci :

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Utilisez cette configuration pour indexer le tableau contenu dans la propriété `level2` :

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="help-us-make-azure-search-better"></a>Aidez-nous à améliorer Recherche Azure
Si vous souhaitez nous soumettre des demandes d’ajout de fonctionnalités ou des idées d’amélioration, n’hésitez pas à nous contacter sur notre [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

