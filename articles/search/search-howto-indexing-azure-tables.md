---
title: Indexation du stockage de tables Azure avec Azure Search
description: "Découvrez comment indexer les données stockées dans des tables Azure avec Azure Search"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 1cc27411-d0cc-40ed-8aed-c7cb9ab402b9
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/18/2017
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 66e6fec16aab7764b05b616efc0fccbfb2d0595e
ms.lasthandoff: 03/30/2017

---

# <a name="indexing-azure-table-storage-with-azure-search"></a>Indexation du stockage de tables Azure avec Azure Search
Cet article montre comment utiliser Azure Search pour indexer les données stockées dans le stockage de tables Azure. Le nouvel indexeur de table Azure Search rend ce processus rapide et transparent.

## <a name="setting-up-azure-table-indexing"></a>Configuration de l’indexation de tables Azure
Pour installer et configurer un indexeur de table Azure, vous pouvez appeler l’API REST Azure Search afin de créer et de gérer des **indexeurs** et des **sources de données** en suivant les procédures décrites dans la rubrique [Opérations d'indexeur](https://msdn.microsoft.com/library/azure/dn946891.aspx). Vous pouvez également utiliser la [version 2.0-preview](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) du SDK .NET. À l’avenir, la prise en charge de l’indexation de tables sera ajoutée au Portail Azure.

Une source de données spécifie les données à indexer, les informations d’identification nécessaires pour accéder aux données et les stratégies qui permettent à Azure Search d’identifier efficacement les changements dans les données (tels que des lignes ajoutées, modifiées ou supprimées).

Un indexeur lit les données d'une source de données et les charge dans un index de recherche cible.

Pour configurer l’indexation de tables :

1. Création d'une source de données
   * Définissez le paramètre `type` sur `azuretable`.
   * Transmettez la chaîne de connexion du compte de stockage en tant que paramètre `credentials.connectionString`. Pour plus d’informations, consultez [Comment spécifier des informations d’identification](#Credentials) ci-dessous.
   * Spécifiez le nom de la table à l’aide du paramètre `container.name` .
   * Si vous le souhaitez, spécifiez une requête en utilisant le paramètre `container.query` . Si possible, utilisez un filtre sur PartitionKey pour optimiser les performances ; toute autre requête entraîne une analyse complète des tables, ce qui peut entraîner une baisse des performances pour les tables volumineuses.
2. Créez un index de recherche avec le schéma correspondant aux colonnes de la table que vous souhaitez indexer.
3. Créez l’indexeur en connectant votre source de données à l’index de recherche.

### <a name="create-data-source"></a>Créer une source de données
    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Pour plus d’informations sur l’API Créer une source de données, consultez [Créer une source de données](https://msdn.microsoft.com/library/azure/dn946876.aspx).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Comment spécifier des informations d’identification ####

Vous pouvez fournir les informations d’identification de la table de l’une des manières suivantes : 

- **Chaîne de connexion au compte de stockage avec accès complet** : `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`. Vous pouvez obtenir la chaîne de connexion sur le portail Azure en sélectionnant le panneau du compte de stockage > Paramètres > Clés (pour les comptes de stockage Classic) ou en sélectionnant Paramètres > Clés d’accès (pour les comptes de stockage ARM).
- Chaîne de connexion de la**signature d’accès partagé (SAP) au compte de stockage** : `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl`. La SAP doit avoir les autorisations de liste et de lecture sur les conteneurs (des tables en l’occurrence) et les objets (des lignes de table).
-  **Signature d’accès partagé à une table** : `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r`. La SAP doit avoir les autorisations de requête (lecture) sur la table.

Pour plus d’informations sur les signatures d’accès partagé au stockage, consultez [Utilisation des signatures d’accès partagé](../storage/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Si vous utilisez des informations d’identification d’une SAP, vous devez mettre à jour les informations d’identification de la source de données régulièrement avec des signatures renouvelées afin d’éviter leur expiration. Si les informations d’identification de la SAP expirent, l’indexeur se bloque et affiche un message d’erreur similaire à `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="create-index"></a>Création d’index
    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

Pour plus d’informations sur l’API Créer un index, consultez [Créer un index](https://msdn.microsoft.com/library/dn798941.aspx)

### <a name="create-indexer"></a>Créer un indexeur
Enfin, créez l’indexeur qui référence la source de données et l’index cible. Par exemple :

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Pour plus d’informations sur l’API Créer un indexeur, consultez [Créer un indexeur](https://msdn.microsoft.com/library/azure/dn946899.aspx).

C’est tout. Une indexation vraiment très simple !

## <a name="dealing-with-different-field-names"></a>Gestion de différents noms de champs
Les noms de champ figurant dans votre index existant diffèrent généralement des noms de propriétés dans votre table. Dans ce cas, vous pouvez utiliser les **mappages de champs** pour mapper les noms de propriété de la table aux noms de champ de votre index de recherche. Pour en savoir plus sur les mappages de champs, consultez [Les mappages de champs de l’indexeur Azure Search comblent les différences entre les sources de données et les index de recherche](search-indexer-field-mappings.md).

## <a name="handling-document-keys"></a>Gestion des clés de document
Dans Azure Search, la clé de document identifie un document de manière unique. Chaque index de recherche doit comporter exactement un champ de clé de type `Edm.String`. Ce champ de clé est nécessaire pour chaque document ajouté à l’index (il constitue en fait le seul champ obligatoire).

Puisque les lignes d’une table ont une clé composée, Azure Search génère un champ synthétique appelé `Key` qui est une concaténation des valeurs de la clé de partition et de la clé de ligne. Par exemple, si la valeur PartitionKey d’une ligne est `PK1` et que RowKey est `RK1`, alors la valeur du champ `Key` sera `PK1RK1`.

> [!NOTE]
> La valeur `Key` peut contenir des caractères non valides dans les clés de document, par exemple des tirets. Vous pouvez traiter les caractères non valides à l’aide de la `base64Encode` [fonction de mappage de champ](search-indexer-field-mappings.md#base64EncodeFunction). Si vous procédez ainsi, n’oubliez pas d’utiliser également l’encodage Base64 sécurisé pour les URL lorsque vous transmettez des clés de document dans des appels d’API tels que Recherche.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Indexation incrémentielle et détection des suppressions
Lorsque vous configurez un indexeur de table pour l’exécuter de manière planifiée, cet indexeur répertorie uniquement les lignes nouvelles ou mises à jour, comme le détermine la valeur `Timestamp` de la ligne. Vous n’êtes pas contraint de spécifier une stratégie de détection des modifications ; l’indexation incrémentielle est activée automatiquement à votre intention.

Pour indiquer que certains documents doivent être supprimés de l’index, vous pouvez utiliser une stratégie de suppression réversible : plutôt que de supprimer une ligne, ajoutez une propriété pour signaler sa suppression, puis configurez une stratégie de détection des suppressions réversibles sur la source de données. Par exemple, la stratégie ci-après considère qu’une ligne est supprimée si elle présente une propriété `IsDeleted` avec la valeur `"true"` :

    PUT https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "query" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   


## <a name="help-us-make-azure-search-better"></a>Aidez-nous à améliorer Azure Search
Si vous souhaitez nous soumettre des demandes d’ajout de fonctionnalités ou des idées d’amélioration, n’hésitez pas à nous contacter sur notre [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

