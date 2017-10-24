---
title: "Extensions OpenAPI pour les connecteurs personnalisés - Azure Logic Apps | Microsoft Docs"
description: "Étendre l’OpenAPI d’un connecteur personnalisé avec des fonctionnalités avancées"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: e8565019bd4631043485224c73c2fe60e633951c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="extend-your-custom-connectors-openapi-with-advanced-functionality"></a>Étendre l’OpenAPI d’un connecteur personnalisé avec des fonctionnalités avancées

Pour pouvoir créer des connecteurs personnalisés pour Azure Logic Apps, Microsoft Flow ou Microsoft PowerApps, vous devez fournir un fichier de définition OpenAPI, c’est-à-dire un document lisible par machine et indépendant du langage qui décrit les opérations et les paramètres de l’API. En parallèle des fonctionnalités prêtes à l’emploi d’OpenAPI, vous pouvez également inclure ces extensions OpenAPI lorsque vous créez des connecteurs personnalisés pour Logic Apps et Flow :

* [`summary`](#summary)
* [`x-ms-summary`](#x-ms-summary)
* [`description`](#description)
* [`x-ms-visibility`](#x-ms-visibility)
* [`x-ms-dynamic-values`](#x-ms-dynamic-values)
* [`x-ms-dynamic-schema`](#x-ms-dynamic-schema)

Voici quelques détails sur ces extensions :

<a name="summary"></a>

## <a name="summary"></a>summary

Spécifie le titre de l’action (opération). </br>
Application : opérations </br>
Recommandation : mettez la *1re lettre des phrases en majuscule* pour `summary`. </br>
Exemple : « Lorsqu’un événement est ajouté au calendrier » ou « Envoyer un e-mail »

![« summary » pour chaque opération](./media/custom-connector-openapi-extensions/summary.png)

``` json
"actions" {
  "Send_an_email": {
    /// Other action properties here...
    "summary": "Send an email",
    /// Other action properties here...
  }
},
```

<a name="x-ms-summary"></a>

## <a name="x-ms-summary"></a>x-ms-summary

Spécifie le titre d’une entité. </br>
Application : paramètres, schéma de réponse </br>
Recommandation : mettez la *1re lettre des mots en majuscule* pour `x-ms-summary`. </br>
Exemple : « ID De Calendrier », « Objet », « Description De L’Événement », etc.

![« x-ms-summary » pour chaque entité](./media/custom-connector-openapi-extensions/x-ms-summary.png)

``` json
"actions" {
  "Send_an_email": {
    /// Other action properties here...
    "parameters": [ 
      {
        /// Other parameters here...
        "x-ms-summary": "Subject",
        /// Other parameters here...
      }
    ]
  }
},
```

<a name="description"></a>

## <a name="description"></a>description

Spécifie une explication détaillée des fonctionnalités de l’opération ou le format et la fonction d’une entité. </br>
Application : opérations, paramètres, schéma de réponse </br>
Recommandation : mettez la *1re lettre des phrases en majuscule* pour `description`. </br>
Exemple : « Cette opération se déclenche lorsqu’un nouvel événement est ajouté au calendrier », « Spécifier le sujet de l’e-mail », etc.

![« description » de chaque opération ou entité](./media/custom-connector-openapi-extensions/description.png)

``` json
"actions" {
  "Send_an_email": {
     "description": "Specify the subject of the mail",
     /// Other action properties here...
  }
},
```

<a name="visibility"></a>

## <a name="x-ms-visibility"></a>x-ms-visibility

Spécifie la visibilité d’une entité vis-à-vis des utilisateurs. </br>
Les valeurs possibles sont `important`, `advanced` et `internal`. </br>
Application : opérations, paramètres, schémas

* Les opérations et paramètres `important` sont toujours affichés à l’utilisateur en premier.
* Les opérations et paramètres `advanced` sont masqués dans un menu supplémentaire.
* Les opérations et paramètres `internal` sont cachés à l’utilisateur.

> [!NOTE] 
> En ce qui concerne les paramètres `internal` et `required`, vous **devez** leur donner des valeurs par défaut.

Exemple : les menus **Afficher plus** et **Afficher les options avancées** masquent les opérations et les paramètres `advanced`.

![« x-ms-visibility » pour afficher ou masquer des opérations et des paramètres](./media/custom-connector-openapi-extensions/x-ms-visibility.png)

``` json
"actions" {
  "Send_an_email": {
     /// Other action properties here...
     "parameters": [
         {
           "name": "Subject",
           "type": "string",
           "description": "Specify the subject of the mail",
           "x-ms-summary": "Subject",
           "x-ms-visibility": "important",
           /// Other parameter properties here...
         }
     ]
     /// Other action properties here...
  }
},
```

<a name="x-ms-dynamic-values"></a>

## <a name="x-ms-dynamic-values"></a>x-ms-dynamic-values

Affiche une liste remplie, dans laquelle l’utilisateur peut sélectionner les paramètres d’entrée d’une opération. </br>
Application : paramètres </br>
Utilisation : ajoutez l’objet `x-ms-dynamic-values` à la définition du paramètre. Par exemple, consultez cet [exemple OpenAPI](https://procsi.blob.core.windows.net/blog-images/sampleDynamicSwagger.json).

![« x-ms-dynamic-values » pour afficher des listes](./media/custom-connector-openapi-extensions/x-ms-dynamic-values.png)

### <a name="properties-for-x-ms-dynamic-values"></a>Propriétés de x-ms-dynamic-values

| Nom | Obligatoire ou facultatif | Description | 
| ---- | -------------------- | ----------- | 
| **operationID** | Requis | L’opération à appeler pour remplir la liste. | 
| **value-path** | Requis | Chaîne de chemin d’accès dans l’objet, à l’intérieur de `value-collection`, qui fait référence à la valeur du paramètre. </br>Si `value-collection` n’est pas spécifié, la réponse est évaluée comme un tableau. | 
| **value-title** | Facultatif | Chaîne de chemin d’accès dans l’objet, à l’intérieur de `value-collection`, qui fait référence à la description de la valeur. </br>Si `value-collection` n’est pas spécifié, la réponse est évaluée comme un tableau. | 
| **value-collection** | Facultatif | Chaîne de chemin d’accès évaluée comme un tableau d’objets dans la charge utile de la réponse. | 
| **parameters** | Facultatif | Objet dont les propriétés spécifient les paramètres d’entrée requis pour appeler une opération dynamic-values. | 
|||| 

Voici un exemple qui montre les propriétés de `x-ms-dynamic-values` :

``` json
"x-ms-dynamic-values": {
  "operationId": "PopulateDropdown",
  "value-path": "name",
  "value-title": "properties/displayName",
  "value-collection": "value",
  "parameters": {
     "staticParameter": "<value>",
     "dynamicParameter": {
        "parameter": "<value-to-pass-to-dynamicParameter>"
     }
  }
}
```

## <a name="example-all-the-openapi-extensions-up-to-this-point"></a>Exemple : toutes les extensions OpenAPI présentées jusqu’ici

``` json
"/api/lists/{listID-dynamic}": {
    "get": {
        "description": "Get items from a single list - uses dynamic values and outputs dynamic schema",
        "summary": "Gets items from the selected list",
        "operationID": "GetListItems",
        "parameters": [
           {
             "name": "listID-dynamic",
             "type": "string",
             "in": "path",
             "description": "Select the list from where you want outputs",
             "required": true,
             "x-ms-summary": "Select List",
             "x-ms-dynamic-values": {
                "operationID": "GetLists",
                "value-path": "id",
                "value-title": "name"
             }
           }
        ]
    }
}
```

<a name="x-ms-dynamic schema"></a>

## <a name="x-ms-dynamic-schema"></a>x-ms-dynamic-schema

Indique que le schéma de la réponse ou du paramètre actuel est dynamique. Cet objet peut appeler une opération définie par la valeur de ce champ, détecter dynamiquement le schéma et afficher l’interface utilisateur correspondante (pour recueillir les entrées utilisateur) ou les champs disponibles. 

Application : paramètres, réponse

Utilisation : ajouter l’objet `x-ms-dynamic-schema` à la définition d’un paramètre de requête ou d’un corps de réponse. Vous trouverez un exemple sur la page [Exemple OpenAPI](https://procsi.blob.core.windows.net/blog-images/sampleDynamicSwagger.json).

Cet exemple montre l’évolution du formulaire d’entrée en fonction de l’élément sélectionné par l’utilisateur dans la liste déroulante :

![« x-ms-dynamic-schema » pour les paramètres dynamiques](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema.png)

Et cet exemple montre l’évolution des sorties en fonction de l’élément sélectionné par l’utilisateur dans la liste déroulante. Dans cette version, l’utilisateur sélectionne « Cars » :

![« x-ms-dynamic-schema-response » pour l’élément sélectionné « Cars »](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema-output1.png)

Dans cette version, l’utilisateur sélectionne « Food » :

![« x-ms-dynamic-schema-response » pour l’élément sélectionné « Food »](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema-output2.png)

### <a name="properties-for-x-ms-dynamic-schema"></a>Propriétés de x-ms-dynamic-schema

| Nom | Obligatoire ou facultatif | Description | 
| ---- | -------------------- | ----------- | 
| **operationID** | Requis | L’opération à appeler pour récupérer le schéma. | 
| **parameters** | Requis | Objet dont les propriétés spécifient les paramètres d’entrée requis pour appeler une opération dynamic-schema. | 
| **value-path** |Facultatif | Chaîne de chemin d’accès faisant référence à la propriété qui comporte le schéma. </br>Si elle n’est pas spécifiée, la réponse est supposée contenir le schéma dans les propriétés de l’objet racine. | 
|||| 

Voici un exemple de paramètre dynamique :

``` json
{
  "name": "dynamicListSchema",
  "in": "body",
  "description": "Dynamic schema for items in the selected list",
  "schema": {
    "type": "object",
    "x-ms-dynamic-schema": {
        "operationID": "GetListSchema",
        "parameters": {
          "listID": {
            "parameter": "listID-dynamic"
          }
        },
        "value-path": "items"
    }
  }
}
```

Voici un exemple de réponse dynamique :

``` json
"DynamicResponseGetListSchema": {
   "type": "object",
   "x-ms-dynamic-schema": {
      "operationID": "GetListSchema",
      "parameters": {
         "listID": {
            "parameter": "listID-dynamic"
         }
      },
      "value-path": "items"
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

* [Décrire les connecteurs et les API personnalisés](../logic-apps/custom-connector-api-postman-collection.md)
* [Logic Apps : Inscrire un connecteur](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow : Inscrire un connecteur](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)