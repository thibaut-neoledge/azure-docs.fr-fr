---
title: "Indexation d’Azure Blob Storage avec Azure Search"
description: "Découvrez comment indexer Azure Blob Storage et extraire le texte de documents avec Azure Search"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 2a5968f4-6768-4e16-84d0-8b995592f36a
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/24/2016
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 2b62ddb83b35194b9fcd23c60773085a9551b172
ms.openlocfilehash: 041b47ed2aba11d45ed6ae02dadb73916046dd78

---

# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>Indexation de documents dans Azure Blob Storage avec Azure Search
Cet article explique comment utiliser Azure Search pour indexer des documents (tels que des fichiers PDF, des documents Microsoft Office et plusieurs autres formats courants) stockés dans le stockage d’objets blob Azure. Le nouvel indexeur d’objets blob Azure Search rend ce processus rapide et transparent.

## <a name="supported-document-formats"></a>Formats de document pris en charge
L’indexeur d’objets blob peut extraire du texte à partir des formats de document suivants :

* PDF
* Formats Microsoft Office : DOCX/DOC, XLSX/XLS, PPTX/PPT, MSG (e-mails Outlook)  
* HTML
* XML
* ZIP
* EML
* Fichiers de texte brut  
* JSON (voir la fonctionnalité de version préliminaire[Indexation d’objets blob JSON](search-howto-index-json-blobs.md))
* CSV (voir la fonctionnalité de version préliminaire[Indexation d’objets blob CSV](search-howto-index-csv-blobs.md))

> [!IMPORTANT]
> La prise en charge des fichiers CSV et JSON est actuellement en version préliminaire. Ces formats sont disponibles uniquement avec la version **2015-02-28-Preview** de l’API REST ou la version 2.x-preview du Kit de développement logiciel (SDK) .NET. N’oubliez pas que les API d’évaluation sont destinées à être utilisées à des fins de test et d’évaluation, et non dans les environnements de production.
>
>

## <a name="setting-up-blob-indexing"></a>Configuration de l’indexation d’objets blob
Vous pouvez configurer un indexeur de Stockage Blob Azure avec les outils suivants :

* [portail Azure](https://ms.portal.azure.com)
* [API REST](https://msdn.microsoft.com/library/azure/dn946891.aspx) de la Recherche Azure
* [Version 2.0-preview](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) du Kit de développement logiciel (SDK) .NET de la Recherche Azure

> [!NOTE]
> Certaines fonctionnalités (par exemple, les mappages de champs) ne sont pas encore disponibles dans le portail et doivent être utilisées par l’intermédiaire de programmes.
>
>

Dans cet article, nous allons configurer un indexeur à l’aide de l’API REST. Tout d’abord, nous allons créer une source de données, puis créer un index et enfin configurer l’indexeur.

Ensuite, nous aborderons les détails sur la manière dont l’indexeur d’objets blob analyse des objets blob, comment choisir les objets blob à indexer, comment traiter les objets blob de types de contenu non pris en charge et les paramètres de configuration disponibles. 

### <a name="step-1-create-a-data-source"></a>Étape 1 : Création d’une source de données
Une source de données spécifie les données à indexer, les informations d’identification nécessaires pour accéder aux données et les stratégies qui identifient efficacement les changements dans les données (telles que des lignes modifiées ou supprimées). Une source de données peut être utilisée par plusieurs indexeurs dans le même service de recherche.

Pour l’indexation des objets blob, la source de données doit avoir les propriétés requises suivantes :

* **name** est le nom unique de la source de données au sein de votre service de recherche.
* **type** doit être `azureblob`.
* **credentials** fournit la chaîne de connexion du compte de stockage en tant que paramètre `credentials.connectionString`. Vous pouvez obtenir la chaîne de connexion à partir du Portail Azure en accédant au panneau du compte de stockage souhaité > **Paramètres** > **Clés** et utiliser la valeur « Chaîne de connexion principale » ou « Chaîne de connexion secondaire ».
* **container** spécifie un conteneur dans votre compte de stockage. Par défaut, tous les objets blob du conteneur sont récupérables. Si vous souhaitez indexer uniquement les objets blob dans un répertoire virtuel particulier, vous pouvez spécifier ce répertoire à l’aide du paramètre facultatif **query**.

L’exemple suivant illustre une définition de source de données :

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Pour plus d’informations sur l’API Créer une source de données, consultez [Créer une source de données](https://msdn.microsoft.com/library/azure/dn946876.aspx).

### <a name="step-2-create-an-index"></a>Étape 2 : Création d’un index
L’index spécifie les champs d’un document, les attributs et d’autres constructions qui façonnent l’expérience de recherche.  

Pour l’indexation des objets blob, assurez-vous que l’index contient un champ `content` cherchable pour stocker l’objet blob.

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

Pour plus d’informations sur l’API Créer un index, consultez [Créer un index](https://msdn.microsoft.com/library/dn798941.aspx)

### <a name="step-3-create-an-indexer"></a>Étape 3 : Création d’un indexeur
Un indexeur connecte des sources de données à des index de recherche cibles et fournit des informations de planification pour vous permettre d’automatiser l’actualisation des données. Une fois la source de données et l’index créés, il est relativement simple de créer un indexeur qui référence la source de données et un index cible. Par exemple :

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Cet indexeur s’exécutera toutes les deux heures (intervalle de planification défini sur « PT2H »). Pour exécuter un indexeur toutes les 30 minutes, définissez l’intervalle sur « PT30M ». Le plus court intervalle pris en charge est de 5 minutes. La planification est facultative : en cas d’omission, un indexeur ne s’exécute qu’une seule fois lorsqu’il est créé. Toutefois, vous pouvez à tout moment exécuter un indexeur à la demande.   

Pour plus d’informations sur l’API Créer un indexeur, consultez [Créer un indexeur](https://msdn.microsoft.com/library/azure/dn946899.aspx).

## <a name="how-azure-search-indexes-blobs"></a>Comment Azure Search indexe les objets blob

En fonction de sa [configuration](#PartsOfBlobToIndex), l’indexeur d’objets blob peut indexer uniquement les métadonnées de stockage (une fonctionnalité utile lorsque vous ne vous préoccupez que des métadonnées et n’avez pas besoin d’indexer le contenu des objets blob), le stockage et le contenu des métadonnées, ou les métadonnées et le contenu textuel. Par défaut, l’indexeur extrait les métadonnées et le contenu. 

> [!NOTE]
> Par défaut, les objets blob avec contenu structuré tels que JSON, CSV ou XML sont indexés en tant que bloc de texte unique. Si vous souhaitez indexer des objets blob JSON et CSV de manière structurée, consultez les fonctionnalités en version préliminaire dans [Indexation d’objets blob JSON](search-howto-index-json-blobs.md) et [Indexation d’objets blob CSV](search-howto-index-csv-blobs.md). Nous ne prenons actuellement pas en charge l’analyse de contenu XM. Si vous pensez en avoir besoin, ajoutez une suggestion sur notre [site UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> Un document composé ou incorporé (tel qu’une archive ZIP ou un document Word avec e-mail Outlook incorporé intégrant des pièces jointes) est également indexé en tant que document unique.

* L’ensemble du contenu de texte du document est extrait dans un champ de chaîne nommé `content`.
* Les propriétés de métadonnées spécifiées par l’utilisateur qui sont éventuellement présentes dans l’objet blob sont extraites textuellement.
* Les propriétés de métadonnées d’objet blob standard sont extraites dans les champs suivants :

  * **metadata\_storage\_name** (Edm.String) : nom de fichier de l’objet blob. Par exemple, si vous disposez de l’objet blob /my-container/my-folder/subfolder/resume.pdf, ce champ présente la valeur `resume.pdf`.
  * **metadata\_storage\_path** (Edm.String) : URI complet de l’objet blob, incluant le compte de stockage. Par exemple, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **metadata\_storage\_content\_type** (Edm.String) : type de contenu tel que spécifié par le code que vous avez utilisé pour charger l’objet blob. Par exemple, `application/octet-stream`.
  * **metadata\_storage\_last\_modified** (Edm.DateTimeOffset) : horodateur de la dernière modification de l’objet blob. La Recherche Azure utilise cet horodateur pour identifier les objets blob modifiés afin d’éviter une réindexation complète après l’indexation initiale.
  * **metadata\_storage\_size** (Edm.Int64) : taille de l’objet blob en octets.
  * **metadata\_storage\_content\_md5** (Edm.String) : code de hachage MD5 du contenu de l’objet blob s’il est disponible.
* Les propriétés de métadonnées propres à chaque format de document sont extraites dans les champs répertoriés [ici](#ContentSpecificMetadata).

Vous n’avez pas besoin de définir les champs relatifs à chacune des propriétés ci-dessus dans votre index de recherche. Il vous suffit de capturer les propriétés dont vous devez disposer pour votre application.

> [!NOTE]
> Les noms de champ figurant dans votre index existant diffèrent généralement des noms de champ générés lors de l’extraction de document. Dans ce cas, vous pouvez utiliser les **mappages de champs** pour mapper les noms de propriétés fournis par la Recherche Azure sur les noms de champs de votre index de recherche. Découvrez ci-dessous une exemple d’utilisation de mappage de champ.
>
>

### <a name="picking-the-document-key-field-and-dealing-with-different-field-names"></a>Sélection du champ de clé de document et gestion des différences de nom de champ
Dans Azure Search, la clé de document identifie un document de manière unique. Chaque index de recherche doit comporter exactement un champ de clé de type Edm.String. Ce champ de clé est nécessaire pour chaque document ajouté à l’index (il constitue en fait le seul champ obligatoire).  

Vous devez déterminer avec soin le champ extrait que vous souhaitez mapper sur le champ de clé de votre index. Les candidats sont les suivants :

* **metadata\_storage\_name** : ce champ pourrait se révéler un choix commode, mais notez que (1) les noms ne sont pas forcément uniques, car vous pouvez disposer d’objets blob portant le même nom dans différents dossiers, et (2) le nom peut contenir des caractères qui ne sont pas valides dans les clés de document, comme des tirets. Vous pouvez gérer les caractères non valides en utilisant la [fonction de mappage de champs](search-indexer-field-mappings.md#base64EncodeFunction) `base64Encode`. Dans ce cas, pensez à encoder les clés de documents lorsque vous les transmettez dans des appels d’API, comme l’API Lookup. (Par exemple, dans .NET, vous pouvez utiliser la [méthode UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) à cet effet).
* **metadata\_storage\_path** : l’utilisation du chemin d’accès complet garantit l’unicité, mais le chemin d’accès contient invariablement des caractères `/` qui ne sont [pas valides dans une clé de document](https://msdn.microsoft.com/library/azure/dn857353.aspx).  Comme ci-dessus, vous avez la possibilité d’encoder les clés à l’aide de la [fonction](search-indexer-field-mappings.md#base64EncodeFunction) `base64Encode`.
* Si aucune des solutions ci-dessus n’est adaptée à votre cas, vous pouvez ajouter une propriété de métadonnées personnalisée aux objets blob. Toutefois, cette approche contraint votre processus de chargement d’objets blob à ajouter cette propriété de métadonnées à tous les objets blob. Étant donné que la clé est une propriété obligatoire, tous les objets blob dépourvus de cette propriété ne seront pas indexés.

> [!IMPORTANT]
> En l’absence de mappage explicite pour le champ de clé dans l’index, la Recherche Azure utilise automatiquement `metadata_storage_path` en guise de clé et encode les valeurs de clés en base 64 (la deuxième option ci-dessus).
>
>

Pour cet exemple, sélectionnons le champ `metadata_storage_name` en tant que clé de document. Supposons également que votre index comporte un champ de clé nommé `key` et un champ `fileSize` pour le stockage de la taille du document. Pour obtenir le résultat souhaité, spécifiez les mappages de champs ci-après lors de la création ou de la mise à jour de votre indexeur :

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Pour regrouper tous ces éléments, utilisez le code ci-après pour ajouter des mappages de champs et activer le codage base 64 des clés pour un indexeur existant :

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }

> [!NOTE]
> Pour en savoir plus sur les mappages de champs, consultez [cet article](search-indexer-field-mappings.md).
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Contrôle les objets blob indexés
Vous pouvez contrôler les objets BLOB qui sont indexés et ignorés.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Indexer uniquement les objets blob avec des extensions de fichier spécifiques
Vous pouvez indexer uniquement les objets blob avec des extensions de nom de fichier que vous spécifiez à l’aide du paramètre de configuration d’indexeur `indexedFileNameExtensions`. La valeur est une chaîne contenant une liste d'extensions de fichier séparées par des virgules (précédées d'un point). Par exemple, pour indexer uniquement les objets blob .PDF et .DOCX, procédez comme suit :

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Exclusion d’objets blob avec des extensions de fichier spécifiques
Vous pouvez exclure de l’indexation des objets blob avec des extensions de nom de fichier spécifiques à l’aide du paramètre de configuration `excludedFileNameExtensions`. La valeur est une chaîne contenant une liste d'extensions de fichier séparées par des virgules (précédées d'un point). Par exemple, pour indexer tous les objets blob, sauf ceux qui ont les extensions .PNG et .JPEG, procédez comme suit :

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Si les paramètres `indexedFileNameExtensions` et `excludedFileNameExtensions` sont tous deux présents, Azure Search regarde d’abord `indexedFileNameExtensions`, puis `excludedFileNameExtensions`. Cela signifie que, si la même extension de fichier est présente dans les deux listes, elle sera exclue de l'indexation.

### <a name="dealing-with-unsupported-content-types"></a>Gestion de types de contenu non pris en charge

Par défaut, l’indexeur d’objets blob s’arrête dès qu’il rencontre un objet blob avec un type de contenu non pris en charge (par exemple, une image). Vous pouvez évidemment utiliser le paramètre `excludedFileNameExtensions` pour ignorer certains types de contenu. Toutefois, vous devrez peut-être indexer des objets blob sans connaître à l’avance tous les types de contenu possibles. Pour poursuivre l’indexation lorsqu’un type de contenu non pris en charge est détecté, définissez le paramètre de configuration `failOnUnsupportedContentType` sur `false` : 

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    } 

### <a name="ignoring-parsing-errors"></a>Erreurs d’analyse ignorées

La logique d’extraction de documents de la Recherche Azure n’est pas parfaite et échoue parfois lors de l’analyse de documents d’un type de contenu pris en charge, notamment .DOCX ou .PDF. Si vous ne souhaitez pas interrompre l’indexation dans ce cas, définissez les paramètres de configuration `maxFailedItems` et `maxFailedItemsPerBatch` sur des valeurs raisonnables. Par exemple : 

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    } 

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Contrôle des parties de l’objet blob à indexer

Vous pouvez contrôler les parties des objets blob à indexer à l’aide du paramètre de configuration `dataToExtract`. Il peut avoir les valeurs suivantes : 

* `storageMetadata` : spécifie que seuls les propriétés standard [ et les métadonnées ](../storage/storage-properties-metadata.md) spécifiées par l’utilisateur sont indexés.
* `allMetadata` : spécifie que les métadonnées de stockage et les [métadonnées spécifiques du type de contenu](#ContentSpecificMetadata) extraites du contenu des objets blob sont indexés.
* `contentAndMetadata` : spécifie que toutes les métadonnées et tous les contenus textuels extraits de l’objet blob sont indexés. Il s’agit de la valeur par défaut.

Par exemple, pour indexer uniquement les métadonnées de stockage, utilisez : 

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Utilisation de métadonnées d’objets blob pour contrôler la manière dont les objets blob sont indexés

Les paramètres de configuration décrits ci-dessus s’appliquent à tous les objets blob. Parfois, vous pouvez souhaiter contrôler la manière dont *différents objets blob* sont indexés. Pour cela, vous pouvez ajouter les propriétés et valeurs de métadonnées d’objets blob suivantes :

| Nom de la propriété | Valeur de la propriété | Explication |
| --- | --- | --- |
| AzureSearch_Skip |"true" |Indique à l’indexeur d’objets blob d’ignorer complètement l’objet blob. Ni l’extraction des métadonnées, ni l’extraction de contenu n’est tentée. Cette propriété est utile lorsqu’un objet blob spécifique échoue à plusieurs reprises et interrompt le processus d’indexation. |
| AzureSearch_SkipContent |"true" |Équivaut au réglage `"dataToExtract" : "allMetadata"` décrit [ci-dessus](#PartsOfBlobToIndex) au niveau d’un objet blob donné. |

## <a name="incremental-indexing-and-deletion-detection"></a>Indexation incrémentielle et détection des suppressions
Lorsque vous configurez un indexeur d’objets blob à exécuter selon une planification, ce dernier réindexe uniquement les objets blob modifiés, comme déterminé par l’horodateur `LastModified` des objets blob.

> [!NOTE]
> Vous n’êtes pas contraint de spécifier une stratégie de détection des modifications ; l’indexation incrémentielle est activée automatiquement à votre intention.

Pour prendre en charge la suppression de documents, utilisez une approche de type « suppression réversible ». Si vous supprimez complètement les objets blob, les documents correspondants ne seront pas supprimés de l’index de recherche. Procédez plutôt comme suit :  

1. Ajoutez une propriété de métadonnées personnalisée à l’objet blob pour indiquer à la Recherche Azure qu’il est logiquement supprimé
2. Configurez une stratégie de détection des suppressions réversibles sur la source de données
3. Une fois que l’indexeur a traité l’objet blob (comme l’indique l’API d’état de l’indexeur), vous pouvez supprimer physiquement l’objet blob

Par exemple, la stratégie suivante considère qu’un objet blob est supprimé s’il présente une propriété de métadonnées `IsDeleted` avec la valeur `true` :

    PUT https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }   

## <a name="indexing-large-datasets"></a>Indexation de jeux de données volumineux

L’indexation d’objets blob peut être un processus long. Dans le cas où vous avez des millions d’objets blob à indexer, vous pouvez accélérer l’indexation en partitionnant les données et en utilisant plusieurs indexeurs pour traiter les données en parallèle. Par exemple, vous pouvez effectuer la configuration suivante : 

- Partitionnez les données dans plusieurs conteneurs d’objets blob ou des dossiers virtuels. 
- Configurez plusieurs sources de données Recherche Azure, une par conteneur ou dossier. Pour pointer vers un dossier d’objets blob, utilisez le paramètre `query` : 

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Créez un indexeur correspondant pour chaque source de données. Tous les indexeurs peuvent pointer vers le même index de recherche cible.  

<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Propriétés de métadonnées propres au type de contenu
Le tableau ci-après récapitule le traitement appliqué pour chaque format de document et décrit les propriétés de métadonnées extraites par Azure Search.

| Format de document/type de contenu | Propriétés de métadonnées propres au type de contenu | Détails du traitement |
| --- | --- | --- |
| HTML (`text/html`) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Suppression du balisage HTML et extraction du texte |
| PDF (`application/pdf`) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Extraction du texte, y compris les documents incorporés (à l’exclusion des images) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extraction du texte, y compris les documents incorporés |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extraction du texte, y compris les documents incorporés |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extraction du texte, y compris les documents incorporés |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extraction du texte, y compris les documents incorporés |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extraction du texte, y compris les documents incorporés |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extraction du texte, y compris les documents incorporés |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extraction du texte, y compris les pièces jointes |
| ZIP (application/zip) |`metadata_content_type` |Extraction du texte de tous les documents figurant dans l’archive |
| XML (application/xml) |`metadata_content_type`</br>`metadata_content_encoding`</br> |Suppression du balisage XML et extraction du texte |
| JSON (application/json) |`metadata_content_type`</br>`metadata_content_encoding` |Extraction du texte<br/>REMARQUE : si vous devez extraire plusieurs champs de document à partir d’un objet blob JSON, consultez [Indexation d’objets blob JSON](search-howto-index-json-blobs.md) pour plus de détails |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Extraction du texte, y compris les pièces jointes |
| Texte brut (text/plain) |`metadata_content_type`</br>`metadata_content_encoding`</br> | |

## <a name="help-us-make-azure-search-better"></a>Aidez-nous à améliorer Azure Search
Si vous souhaitez nous soumettre des demandes d’ajout de fonctionnalités ou des idées d’amélioration, contactez-nous sur notre [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).



<!--HONumber=Nov16_HO4-->


