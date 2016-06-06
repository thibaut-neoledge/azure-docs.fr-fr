<properties
pageTitle="Ajouter le connecteur Salesforce à PowerApps Enterprise et à vos applications logiques | Microsoft Azure"
description="Vue d’ensemble du connecteur Salesforce avec les paramètres de l’API REST"
services=""    
documentationCenter=""     
authors="msftman"    
manager="erikre"    
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="05/19/2016"
ms.author="deonhe"/>

# Prise en main du connecteur Salesforce 
Connectez-vous à Salesforce et créez des objets, obtenez des objets et bien plus encore. Le connecteur Salesforce peut être utilisé dans :

- Logic Apps 
- PowerApps

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/connectors-create-api-salesforce.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-salesforce.md)

&nbsp;

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.

Avec Salesforce, vous pouvez effectuer les opérations suivantes :

- Créer votre flux d’activité en fonction des données que vous obtenez de Salesforce. 
- Utiliser des déclencheurs quand un objet est créé ou mis à jour.
- Utiliser des actions pour créer un objet, supprimer un objet et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, quand un objet est créé dans Salesforce, vous pouvez envoyer un message électronique à l’aide d’Office 365.
- Ajouter le connecteur Salesforce à PowerApps Enterprise. Vos utilisateurs peuvent ensuite utiliser ce connecteur dans leurs applications. 

Pour plus d’informations sur l’ajout d’un connecteur à PowerApps Enterprise, consultez [Register connector in PowerApps](../power-apps/powerapps-register-from-available-apis.md) (Inscrire un connecteur dans PowerApps).

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions
L’API Salesforce inclut les déclencheurs et les actions suivants.

| Déclencheurs | Actions|
| --- | --- |
|<ul><li>Quand un objet est créé</li><li>Quand un objet est modifié</li></ul> | <ul><li>Créer un objet</li><li>Obtenir des objets</li><li>Quand un objet est créé</li><li>Quand un objet est modifié</li><li>Supprimer un objet</li><li>Obtenir un objet</li><li>Obtenir des types d’objet (SObjects)</li><li>Mettre à jour un objet</li></ul>

Tous les connecteurs prennent en charge les données aux formats JSON et XML.

## Créer une connexion à Salesforce 

Quand vous ajoutez ce connecteur à vos applications logiques, vous devez autoriser celles-ci à se connecter à votre compte Salesforce.

>[AZURE.INCLUDE [Procédure de création d’une connexion à Salesforce](../../includes/connectors-create-api-salesforce.md)]

Après avoir créé la connexion, vous entrez les propriétés Salesforce, telles que le nom de la table. La section **Informations de référence sur l’API REST** dans cette rubrique décrit ces propriétés.

>[AZURE.TIP] Vous pouvez utiliser cette même connexion dans d’autres applications logiques.

## Informations de référence sur l’API REST Swagger
S’applique à la version 1.0.


### Créer un objet
Crée un objet Salesforce. ```POST: /datasets/default/tables/{table}/items```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Type de SObject Salesforce (exemple : « Lead »)|
|item| |yes|body|(aucun)|Objet Salesforce à créer|

### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|



### Obtenir un objet
Récupère un objet Salesforce. ```GET: /datasets/default/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Type de SObject Salesforce (exemple : « Lead »)|
|id|string|yes|path|(aucun)|Identificateur unique de l’objet Salesforce à récupérer|

### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|



### Supprimer un objet
Supprime un objet Salesforce. ```DELETE: /datasets/default/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Type de SObject Salesforce (exemple : « Lead »)|
|id|string|yes|path|(aucun)|Identificateur unique de l’objet Salesforce à supprimer|

### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|



### Mettre à jour un objet
Met à jour un objet Salesforce. ```PATCH: /datasets/default/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Type de SObject Salesforce (exemple : « Lead »)|
|id|string|yes|path|(aucun)|Identificateur unique de l’objet Salesforce à mettre à jour|
|item| |yes|body|(aucun)|Objet Salesforce avec des propriétés modifiées|

### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|



### Quand un objet est créé
Déclenche un flux quand un objet est créé dans Salesforce. ```GET: /datasets/default/tables/{table}/onnewitems```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Type de SObject Salesforce (exemple : « Lead »)|
|$skip|integer|no|query|(aucun)|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|integer|no|query|(aucun)|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|
|$filter|string|no|query|(aucun)|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|string|no|query|(aucun)|Requête orderBy ODATA pour spécifier l’ordre des entrées|

### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|



### Quand un objet est modifié 
Déclenche un flux quand un objet est modifié dans Salesforce. ```GET: /datasets/default/tables/{table}/onupdateditems```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Type de SObject Salesforce (exemple : « Lead »)|
|$skip|integer|no|query|(aucun)|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|integer|no|query|(aucun)|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|
|$filter|string|no|query|(aucun)|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|string|no|query|(aucun)|Requête orderBy ODATA pour spécifier l’ordre des entrées|

### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|



## Définitions d'objet 

#### DataSetsMetadata

| Nom | Type de données | Requis|
|---|---|---|
|tabular|non défini|no|
|objet blob|non défini|no|


#### TabularDataSetsMetadata

| Nom | Type de données | Requis|
|---|---|---|
|source|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|


#### BlobDataSetsMetadata

| Nom | Type de données | Requis|
|---|---|---|
|source|string|no|
|displayName|string|no|
|urlEncoding|string|no|


#### TableMetadata

| Nom | Type de données | Requis|
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|non défini|no|


#### DataSetsList

| Nom | Type de données | Requis|
|---|---|---|
|value|array|no|


#### DataSet

| Nom | Type de données | Requis|
|---|---|---|
|Nom|string|
|DisplayName|string|no|


#### Table

| Nom | Type de données | Requis|
|---|---|---|
|Nom|string|no|
|DisplayName|string|no|


#### Item

| Nom | Type de données | Requis|
|---|---|---|
|ItemInternalId|string|no|


#### ItemsList

| Nom | Type de données | Requis|
|---|---|---|
|value|array|no|


#### TablesList

| Nom | Type de données | Requis|
|---|---|---|
|value|array|no|


## Étapes suivantes

[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)

Revenir à la [liste des API](apis-list.md)


[5]: https://developer.salesforce.com
[6]: ./media/connectors-create-api-salesforce/salesforce-developer-homepage.png
[7]: ./media/connectors-create-api-salesforce/salesforce-create-app.png
[8]: ./media/connectors-create-api-salesforce/salesforce-new-app.png

<!---HONumber=AcomDC_0525_2016-->