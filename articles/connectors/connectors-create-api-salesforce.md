---
title: "Découvrez comment utiliser le connecteur Salesforce dans vos applications logiques | Microsoft Docs"
description: "Créez des applications logiques avec Azure App Service. Le connecteur Salesforce offre une API permettant de travailler avec des objets Salesforce."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/05/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: b6758aa36120c9c187e91ee5d9e7ceb5041eae6a


---
# <a name="get-started-with-the-salesforce-connector"></a>Prise en main du connecteur Salesforce
Le connecteur Salesforce offre une API permettant de travailler avec des objets Salesforce.

Pour utiliser [n’importe quel connecteur](apis-list.md), vous devez commencer par créer une application logique. Vous pouvez démarrer maintenant en [créant une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-salesforce-connector"></a>Se connecter au connecteur Salesforce
Pour que votre application logique puisse accéder à un service, vous devez commencer par créer une *connexion* à celui-ci. Une [connexion](connectors-overview.md) permet d’assurer la connectivité entre une application logique et un autre service.  

### <a name="create-a-connection-to-salesforce-connector"></a>Créer une connexion au connecteur Salesforce
> [!INCLUDE [Steps to create a connection to Salesforce Connector](../../includes/connectors-create-api-salesforce.md)]
> 
> 

## <a name="use-a-salesforce-connector-trigger"></a>Utiliser un déclencheur du connecteur Salesforce
Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. [En savoir plus sur les déclencheurs](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

> [!INCLUDE [Steps to create a Salesforce trigger](../../includes/connectors-create-api-salesforce-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Ajouter une condition
> [!INCLUDE [Steps to create a Salesforce condition](../../includes/connectors-create-api-salesforce-condition.md)]
> 
> 

## <a name="use-a-salesforce-connector-action"></a>Utiliser une action du connecteur Salesforce
Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

> [!INCLUDE [Steps to create a Salesforce action](../../includes/connectors-create-api-salesforce-action.md)]
> 
> 

## <a name="technical-details"></a>Détails techniques
Voici les détails des déclencheurs, actions et réponses pris en charge par cette connexion :

## <a name="salesforce-connector-triggers"></a>Déclencheurs du connecteur Salesforce
Le connecteur Salesforce comporte les déclencheurs suivants :  

| Déclencheur | Description |
| --- | --- |
| [Quand un objet est créé](connectors-create-api-salesforce.md#when-an-object-is-created) |Cette opération déclenche un flux lorsqu’un objet est créé. |
| [Quand un objet est modifié](connectors-create-api-salesforce.md#when-an-object-is-modified) |Cette opération déclenche un flux lorsqu’un objet est modifié. |

## <a name="salesforce-connector-actions"></a>Actions du connecteur Salesforce
Le connecteur Salesforce comporte les actions suivantes :

| Action | Description |
| --- | --- |
| [Get objects](connectors-create-api-salesforce.md#get-objects) (Obtenir les objets) |Cette opération récupère les objets d’un certain type d’objet, tel que « Prospect ». |
| [Create object](connectors-create-api-salesforce.md#create-object) (Créer un objet) |Cette opération crée un objet. |
| [Get object](connectors-create-api-salesforce.md#get-object) (Obtenir un objet) |Cette opération récupère un objet. |
| [Delete object](connectors-create-api-salesforce.md#delete-object) (Supprimer un objet) |Cette opération supprime un objet. |
| [Update object](connectors-create-api-salesforce.md#update-object) (Mettre à jour un objet) |Cette opération met à jour un objet. |
| [Get object types](connectors-create-api-salesforce.md#get-object-types) (Obtenir les types d’objets) |Cette opération répertorie les types d’objets disponibles. |

### <a name="action-details"></a>Détails de l’action
Voici les détails des actions et des déclencheurs de ce connecteur, ainsi que leurs réponses :

### <a name="get-objects"></a>Get objects (Obtenir les objets)
Cette opération récupère les objets d’un certain type d’objet, tel que « Prospect ». 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |Type d'objet |Type d’objet Salesforce, tel que « Prospect » |
| $filter |Requête de filtre |Requête filter ODATA pour limiter le nombre d’entrées |
| $orderby |Trier par |Requête orderBy ODATA pour spécifier l’ordre des entrées |
| $skip |Nombre à ignorer |Nombre d’entrées à ignorer (valeur par défaut : 0) |
| $top |Nombre maximal à récupérer |Nombre maximal d’entrées à récupérer (valeur par défaut : 256) |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
ItemsList

| Nom de la propriété | Type de données |
| --- | --- |
| value |array |

### <a name="create-object"></a>Créer un objet
Cette opération crée un objet. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |Type d’objet |Type d’objet, tel que « Prospect » |
| item* |Object |Objet à créer |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
Élément

| Nom de la propriété | Type de données |
| --- | --- |
| ItemInternalId |string |

### <a name="get-object"></a>Obtenir un objet
Cette opération récupère un objet. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |Type d'objet |Type d’objet Salesforce, tel que « Prospect » |
| id* |ID objet |Identificateur de l’objet à obtenir |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
Élément

| Nom de la propriété | Type de données |
| --- | --- |
| ItemInternalId |string |

### <a name="delete-object"></a>Supprimer un objet
Cette opération supprime un objet. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |Type d’objet |Type d’objet, tel que « Prospect » |
| id* |ID objet |Identificateur de l’objet à supprimer |

Le caractère * indique qu’une propriété est obligatoire.

### <a name="update-object"></a>Mettre à jour un objet
Cette opération met à jour un objet. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |Type d’objet |Type d’objet, tel que « Prospect » |
| id* |ID objet |Identificateur de l’objet à mettre à jour |
| item* |Object |Objet avec des propriétés modifiées |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
Élément

| Nom de la propriété | Type de données |
| --- | --- |
| ItemInternalId |string |

### <a name="when-an-object-is-created"></a>Quand un objet est créé
Cette opération déclenche un flux lorsqu’un objet est créé. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |Type d’objet |Type d’objet, tel que « Prospect » |
| $filter |Requête de filtre |Requête filter ODATA pour limiter le nombre d’entrées |
| $orderby |Trier par |Requête orderBy ODATA pour spécifier l’ordre des entrées |
| $skip |Nombre à ignorer |Nombre d’entrées à ignorer (valeur par défaut : 0) |
| $top |Nombre maximal à récupérer |Nombre maximal d’entrées à récupérer (valeur par défaut : 256) |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
ItemsList

| Nom de la propriété | Type de données |
| --- | --- |
| value |array |

### <a name="when-an-object-is-modified"></a>Quand un objet est modifié
Cette opération déclenche un flux lorsqu’un objet est modifié. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |Type d’objet |Type d’objet, tel que « Prospect » |
| $filter |Requête de filtre |Requête filter ODATA pour limiter le nombre d’entrées |
| $orderby |Trier par |Requête orderBy ODATA pour spécifier l’ordre des entrées |
| $skip |Nombre à ignorer |Nombre d’entrées à ignorer (valeur par défaut : 0) |
| $top |Nombre maximal à récupérer |Nombre maximal d’entrées à récupérer (valeur par défaut : 256) |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
ItemsList

| Nom de la propriété | Type de données |
| --- | --- |
| value |array |

### <a name="get-object-types"></a>Get object types (Obtenir les types d’objets)
Cette opération répertorie les types d’objets disponibles. 

Il n’existe aucun paramètre pour cet appel

#### <a name="output-details"></a>Détails des résultats
TablesList

| Nom de la propriété | Type de données |
| --- | --- |
| value |array |

## <a name="http-responses"></a>Réponses HTTP
Les actions et déclencheurs ci-dessus peuvent renvoyer un ou plusieurs des codes d’état HTTP suivants : 

| Nom | Description |
| --- | --- |
| 200 |OK |
| 202 |Acceptée |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


