<properties
pageTitle="Découvrez comment utiliser le connecteur Salesforce dans vos applications logiques | Microsoft Azure"
description="Créez des applications logiques avec Azure App Service. Le connecteur Salesforce offre une API permettant de travailler avec des objets Salesforce."
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/22/2016"
ms.author="deonhe"/>

# Prise en main du connecteur Salesforce

Le connecteur Salesforce offre une API permettant de travailler avec des objets Salesforce.

Pour utiliser [n’importe quel connecteur](./apis-list.md), vous devez commencer par créer une application logique. Vous pouvez démarrer maintenant en [créant une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Se connecter au connecteur Salesforce

Pour que votre application logique puisse accéder à un service, vous devez commencer par créer une *connexion* à celui-ci. Une [connexion](./connectors-overview.md) permet d’assurer la connectivité entre une application logique et un autre service.

### Créer une connexion au connecteur Salesforce

>[AZURE.INCLUDE [Procédure de création d’une connexion au connecteur Salesforce](../../includes/connectors-create-api-salesforce.md)]

## Utiliser un déclencheur du connecteur Salesforce

Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. [Apprenez-en davantage sur les déclencheurs](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

>[AZURE.INCLUDE [Procédure de création d’un déclencheur Salesforce](../../includes/connectors-create-api-salesforce-trigger.md)]

## Ajouter une condition 
>[AZURE.INCLUDE [Procédure de création d’une condition Salesforce](../../includes/connectors-create-api-salesforce-condition.md)]

## Utiliser une action du connecteur Salesforce

Une action est une opération effectuée par le flux de travail défini dans une application logique. [Apprenez-en davantage sur les actions](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

>[AZURE.INCLUDE [Procédure de création d’une action Salesforce](../../includes/connectors-create-api-salesforce-action.md)]

## Détails techniques

Voici les détails des déclencheurs, actions et réponses pris en charge par cette connexion :

## Déclencheurs du connecteur Salesforce

Le connecteur Salesforce comporte les déclencheurs suivants :

|Déclencheur | Description|
|--- | ---|
|[Quand un objet est créé](connectors-create-api-salesforceconnector.md#when-an-object-is-created)|Cette opération déclenche un flux lorsqu’un objet est créé.|
|[Quand un objet est modifié](connectors-create-api-salesforceconnector.md#when-an-object-is-modified)|Cette opération déclenche un flux lorsqu’un objet est modifié.|


## Actions du connecteur Salesforce

Le connecteur Salesforce comporte les actions suivantes :


|Action|Description|
|--- | ---|
|[Get objects](connectors-create-api-salesforceconnector.md#get-objects) (Obtenir les objets)|Cette opération récupère les objets d’un certain type d’objet, tel que « Prospect ».|
|[Créer un objet](connectors-create-api-salesforceconnector.md#create-object)|Cette opération crée un objet.|
|[Obtenir un objet](connectors-create-api-salesforceconnector.md#get-object)|Cette opération récupère un objet.|
|[Supprimer un objet](connectors-create-api-salesforceconnector.md#delete-object)|Cette opération supprime un objet.|
|[Mettre à jour un objet](connectors-create-api-salesforceconnector.md#update-object)|Cette opération met à jour un objet.|
|[Get object types](connectors-create-api-salesforceconnector.md#get-object-types) (Obtenir les types d’objets)|Cette opération répertorie les types d’objets disponibles.|
### Détails de l’action

Voici les détails des actions et des déclencheurs de ce connecteur, ainsi que leurs réponses :



### Get objects (Obtenir les objets)
Cette opération récupère les objets d’un certain type d’objet, tel que « Prospect ».


|Nom de la propriété| Display Name|Description|
| ---|---|---|
|table*|Type d’objet|Type d’objet Salesforce, tel que « Prospect »|
|$filter|Requête de filtre|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|Trier par|Requête orderBy ODATA pour spécifier l’ordre des entrées|
|$skip|Nombre à ignorer|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|Nombre maximal à récupérer|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|

Le caractère * indique qu’une propriété est obligatoire.

#### Détails des résultats

ItemsList


| Nom de la propriété | Type de données |
|---|---|
|value|array|




### Créer un objet
Cette opération crée un objet.


|Nom de la propriété| Display Name|Description|
| ---|---|---|
|table*|Type d’objet|Type d’objet, tel que « Prospect »|
|item*|Object|Objet à créer|

Le caractère * indique qu’une propriété est obligatoire.

#### Détails des résultats

Item


| Nom de la propriété | Type de données |
|---|---|
|ItemInternalId|string|




### Obtenir un objet
Cette opération récupère un objet.


|Nom de la propriété| Display Name|Description|
| ---|---|---|
|table*|Type d’objet|Type d’objet Salesforce, tel que « Prospect »|
|id*|ID objet|Identificateur de l’objet à obtenir|

Le caractère * indique qu’une propriété est obligatoire.

#### Détails des résultats

Item


| Nom de la propriété | Type de données |
|---|---|
|ItemInternalId|string|




### Supprimer un objet
Cette opération supprime un objet.


|Nom de la propriété| Display Name|Description|
| ---|---|---|
|table*|Type d’objet|Type d’objet, tel que « Prospect »|
|id*|ID objet|Identificateur de l’objet à supprimer|

Le caractère * indique qu’une propriété est obligatoire.




### Mettre à jour un objet
Cette opération met à jour un objet.


|Nom de la propriété| Display Name|Description|
| ---|---|---|
|table*|Type d’objet|Type d’objet, tel que « Prospect »|
|id*|ID objet|Identificateur de l’objet à mettre à jour|
|item*|Object|Objet avec des propriétés modifiées|

Le caractère * indique qu’une propriété est obligatoire.

#### Détails des résultats

Item


| Nom de la propriété | Type de données |
|---|---|
|ItemInternalId|string|




### Quand un objet est créé
Cette opération déclenche un flux lorsqu’un objet est créé.


|Nom de la propriété| Display Name|Description|
| ---|---|---|
|table*|Type d’objet|Type d’objet, tel que « Prospect »|
|$filter|Requête de filtre|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|Trier par|Requête orderBy ODATA pour spécifier l’ordre des entrées|
|$skip|Nombre à ignorer|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|Nombre maximal à récupérer|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|

Le caractère * indique qu’une propriété est obligatoire.

#### Détails des résultats

ItemsList


| Nom de la propriété | Type de données |
|---|---|
|value|array|




### Quand un objet est modifié
Cette opération déclenche un flux lorsqu’un objet est modifié.


|Nom de la propriété| Display Name|Description|
| ---|---|---|
|table*|Type d’objet|Type d’objet, tel que « Prospect »|
|$filter|Requête de filtre|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|Trier par|Requête orderBy ODATA pour spécifier l’ordre des entrées|
|$skip|Nombre à ignorer|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|Nombre maximal à récupérer|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|

Le caractère * indique qu’une propriété est obligatoire.

#### Détails des résultats

ItemsList


| Nom de la propriété | Type de données |
|---|---|
|value|array|




### Get object types (Obtenir les types d’objets)
Cette opération répertorie les types d’objets disponibles.


Il n’existe aucun paramètre pour cet appel

#### Détails des résultats

TablesList


| Nom de la propriété | Type de données | 
|---|---|
|value|array|



## Réponses HTTP

Les actions et déclencheurs ci-dessus peuvent renvoyer un ou plusieurs des codes d’état HTTP suivants :

|Nom|Description|
|---|---|
|200|OK|
|202|Acceptée|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|






## Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!-----HONumber=AcomDC_0803_2016-->
