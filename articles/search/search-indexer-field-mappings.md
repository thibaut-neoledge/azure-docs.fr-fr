---
title: Mappages de champs dans les indexeurs de la Recherche Azure
description: "Configurer les mappages de champs de l&quot;indexeur Azure Search pour tenir compte des différences dans les noms de champs et les représentations des données"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 0325a4de-0190-4dd5-a64d-4e56601d973b
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: fc2f30569acc49dd383ba230271989eca8a14423
ms.openlocfilehash: 57e91f070d9a42882a56e708f12b1ce238ed9191

---

# <a name="field-mappings-in-azure-search-indexers"></a>Mappages de champs dans les indexeurs de la Recherche Azure
Lorsque vous utilisez des indexeurs Azure Search, vous pouvez parfois vous retrouver dans des situations où vos données d'entrée ne correspondent pas tout à fait au schéma de votre index cible. Dans ce cas, vous pouvez utiliser les **mappages de champs** pour transformer vos données au format souhaité.

Quelques situations où les mappages de champs sont utiles :

* Votre source de données a un champ `_id`, mais Azure Search n'autorise pas les noms de champs commençant par un trait de soulignement. Un mappage de champs vous permet de « renommer » un champ.
* Vous souhaitez remplir plusieurs champs de l'index avec les mêmes données de source de données, par exemple parce que vous souhaitez appliquer différents analyseurs à ces champs. Les mappages de champs vous permettent de « répliquer » un champ de source de données.
* Vous avez besoin d’encoder ou de décoder vos données en Base64. Les mappages de champs prennent en charge plusieurs **fonctions de mappage**, y compris les fonctions d’encodage et de décodage en Base64.   

## <a name="setting-up-field-mappings"></a>Configuration des mappages de champs
Vous pouvez ajouter des mappages de champs lors de la création d'un indexeur avec l’API [Créer un indexeur](https://msdn.microsoft.com/library/azure/dn946899.aspx) . Vous pouvez gérer les mappages de champs sur un indexeur d'indexation avec l’API [Mettre à jour l’indexeur](https://msdn.microsoft.com/library/azure/dn946892.aspx) .

Un mappage de champs comprend 3 parties :

1. Un `sourceFieldName`, qui représente un champ de votre source de données. Cette propriété est requise.
2. Un `targetFieldName`facultatif, qui représente un champ de votre index de recherche. Si omis, le nom de la source de données est utilisé.
3. Une `mappingFunction`facultative, qui peut transformer vos données à l'aide d'une des fonctions prédéfinies. La liste complète des fonctions est présentée [ci-dessous](#mappingFunctions).

Les mappages de champs sont ajoutés au tableau `fieldMappings` sur la définition de l'indexeur.

Par exemple, voici comment gérer les différences dans les noms de champs :

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

Un indexeur peut avoir plusieurs mappages de champs. Par exemple, voici comment « répliquer » un champ :

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" },
]
```

> [!NOTE]
> Azure Search utilise une comparaison qui ne respecte pas la casse pour résoudre les noms de champ et de fonction dans les mappages de champs. C'est pratique (vous n'avez besoin de faire attention à la casse), mais cela signifie que votre source de données et votre index ne peuvent pas comporter des champs qui diffèrent uniquement par la casse.  
>
>

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Fonctions de mappage de champs
Ces fonctions sont actuellement prises en charge :

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode"></a>base64Encode
Exécute l’encodage Base64 *sécurisé pour les URL* de la chaîne d'entrée. Suppose que l'entrée est encodée en UTF-8.

#### <a name="sample-use-case"></a>Exemple de cas d’utilisation
Seuls les caractères sécurisés pour les URL peuvent apparaître dans une clé de document Azure Search (étant donné que les clients doivent être en mesure de traiter le document à l'aide de l'API de recherche, par exemple). Si vos données contiennent des caractères non sécurisés pour les URL et que vous souhaitez les utiliser pour remplir un champ de clé de votre index de recherche, utilisez cette fonction.   

#### <a name="example"></a>Exemple
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Path",
    "targetFieldName" : "UrlSafePath",
    "mappingFunction" : { "name" : "base64Encode" }
  }]
```

<a name="base64DecodeFunction"></a>

### <a name="base64decode"></a>base64Decode
Effectue le décodage en Base64 de la chaîne d'entrée. L'entrée est considérée comme une chaîne encodée en Base64 *sécurisée pour les URL* .

#### <a name="sample-use-case"></a>Exemple de cas d’utilisation
Les valeurs des métadonnées personnalisées des objets blob doivent être encodées en ASCII. Vous pouvez utiliser l’encodage Base64 pour représenter des chaînes Unicode arbitraires dans les métadonnées personnalisées des objets blob. Toutefois, pour rendre la recherche explicite, vous pouvez utiliser cette fonction pour transformer les données encodées en chaînes « normales » lors du remplissage de votre index de recherche.  

#### <a name="example"></a>Exemple
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode" }
  }]
```

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition"></a>extractTokenAtPosition
Divise un champ de chaîne en utilisant le séparateur spécifié et récupère le jeton à la position spécifiée dans le fractionnement résultant.

Par exemple, si l’entrée est `Jane Doe`, que le `delimiter` est `" "` (espace) et que la `position` est 0, le résultat est `Jane` ; si la `position` est 1, le résultat est `Doe`. Si la position fait référence à un jeton qui n'existe pas, une erreur est renvoyée.

#### <a name="sample-use-case"></a>Exemple de cas d’utilisation
Votre source de données contient un champ `PersonName` et vous souhaitez l’indexer en tant que deux champs `FirstName` et `LastName` distincts. Vous pouvez utiliser cette fonction pour fractionner l'entrée en utilisant l'espace comme séparateur.

#### <a name="parameters"></a>Paramètres
* `delimiter`: une chaîne à utiliser comme séparateur lors du fractionnement de la chaîne d'entrée.
* `position`: une position entière à base zéro du jeton à choisir une fois la chaîne d'entrée fractionnée.    

#### <a name="example"></a>Exemple
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection"></a>jsonArrayToStringCollection
Transforme une chaîne formatée en tant que tableau de chaînes JSON en un tableau de chaînes utilisable pour remplir un champ `Collection(Edm.String)` dans l'index.

Par exemple, si la chaîne d’entrée est `["red", "white", "blue"]`, le champ cible de type `Collection(Edm.String)` sera rempli avec les valeurs `red`, `white` et `blue`. Pour les valeurs d'entrée qui ne peuvent pas être analysées en tant que tableaux de chaînes JSON, une erreur est renvoyée.

#### <a name="sample-use-case"></a>Exemple de cas d’utilisation
Une base de données SQL Azure n'a pas de type de données intégré qui se mappe naturellement aux champs `Collection(Edm.String)` dans Azure Search. Pour remplir les champs de la collection de chaînes, mettez vos données sources sous forme de tableau de chaînes JSON et utilisez cette fonction.

#### <a name="example"></a>Exemple
```JSON

"fieldMappings" : [
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
]
```

## <a name="help-us-make-azure-search-better"></a>Aidez-nous à améliorer Azure Search
Si vous souhaitez nous soumettre des demandes d’ajout de fonctionnalités ou des idées d’amélioration, n’hésitez pas à nous contacter sur notre [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).



<!--HONumber=Nov16_HO3-->


