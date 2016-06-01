<properties
pageTitle="Sql Connector | Microsoft Azure"
description="Créer des applications logiques avec Azure App Service. Le connecteur Sql Connector fournit une API pour travailler avec des bases de données SQL."
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="05/17/2016"
ms.author="deonhe"/>

# Prise en main du connecteur Sql Connector



Le connecteur Sql Connector peut être utilisé à partir de :

- [Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [Flux](http://flows.microsoft.com)  

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.

Vous pouvez commencer en créant une application logique. Consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions

Le connecteur Sql Connector peut être utilisé en tant qu’action ; il possède un ou plusieurs déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML.

 Le connecteur Sql Connector met à votre disposition les actions et/ou les déclencheurs ci-après.

### Actions du connecteur Sql Connector
Vous pouvez effectuer les actions suivantes :

|Action|Description|
|--- | ---|
|[GetTables](connectors-create-api-sqlconnector.md#gettables)|Extrait des tables à partir d’une base de données SQL|
|[PostItem](connectors-create-api-sqlconnector.md#postitem)|Insère une nouvelle ligne dans une table SQL|
|[GetItem](connectors-create-api-sqlconnector.md#getitem)|Récupère une seule ligne d’une table SQL|
|[DeleteItem](connectors-create-api-sqlconnector.md#deleteitem)|Supprime une ligne d’une table SQL|
|[PatchItem](connectors-create-api-sqlconnector.md#patchitem)|Met à jour une ligne existante dans une table SQL|
### Déclencheurs du connecteur Sql Connector
Vous pouvez écouter les événements suivants :

|Déclencheur | Description|
|--- | ---|


## Créer une connexion à Sql Connector
Pour créer des applications logiques avec Sql Connector, vous devez d’abord créer une **connexion**, puis fournir les détails pour les propriétés suivantes :

|Propriété| Requis|Description|
| ---|---|---|
|SqlConnectionString|Oui|Fournir votre chaîne de connexion SQL|
Après avoir créé la connexion, vous pouvez l’utiliser pour exécuter les actions et écouter les déclencheurs décrits dans cet article.

>[AZURE.INCLUDE [Procédure de création d’une connexion à Mashup SQL](../../includes/connectors-create-api-mashupsql.md)]

>[AZURE.TIP] Vous pouvez utiliser cette connexion dans d'autres applications logiques.

## Référence pour Sql Connector
S’applique à la version : 1.0

## GetTables
Obtenir des tables : extrait des tables à partir d’une base de données SQL

```GET: /datasets/default/tables```

Il n’existe aucun paramètre pour cet appel
#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## PostItem
Insérer une ligne : insère une nouvelle ligne dans une table SQL

```POST: /datasets/default/tables/{table}/items```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Nom de la table SQL|
|item| |yes|body|(aucun)|Ligne à insérer dans la table SQL spécifiée|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## GetItem
Obtenir une ligne : récupère une seule ligne d’une table SQL

```GET: /datasets/default/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Nom de la table SQL|
|id|string|yes|path|(aucun)|Identificateur unique de la ligne à récupérer|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## DeleteItem
Supprimer une ligne : supprime une ligne d’une table SQL

```DELETE: /datasets/default/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Nom de la table SQL|
|id|string|yes|path|(aucun)|Identificateur unique de la ligne à supprimer|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## PatchItem
Mettre à jour une ligne : met à jour une ligne existante dans une table SQL

```PATCH: /datasets/default/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Nom de la table SQL|
|id|string|yes|path|(aucun)|Identificateur unique de la ligne à mettre à jour|
|item| |yes|body|(aucun)|Ligne avec valeurs mises à jour|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## Définitions d'objet 

### DataSetsMetadata


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|tabular|non défini|Non |
|objet blob|non défini|Non |



### TabularDataSetsMetadata


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|source|string|Non |
|displayName|string|Non |
|urlEncoding|string|Non |
|tableDisplayName|string|Non |
|tablePluralName|string|Non |



### BlobDataSetsMetadata


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|source|string|Non |
|displayName|string|Non |
|urlEncoding|string|Non |



### TableMetadata


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|name|string|Non |
|title|string|Non |
|x-ms-permission|string|Non |
|x-ms-capabilities|non défini|Non |
|schema|non défini|Non |



### TableCapabilitiesMetadata


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|sortRestrictions|non défini|Non |
|filterRestrictions|non défini|Non |
|filterFunctions|array|Non |



### Object


| Nom de la propriété | Type de données | Requis |
|---|---|---|



### TableSortRestrictionsMetadata


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|sortable|booléenne|Non |
|unsortableProperties|array|Non |
|ascendingOnlyProperties|array|Non |



### TableFilterRestrictionsMetadata


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|filterable|booléenne|Non |
|nonFilterableProperties|array|Non |
|requiredProperties|array|Non |



### DataSetsList


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|value|array|Non |



### DataSet


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|Nom|string|Non |
|DisplayName|string|Non |



### Table


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|Nom|string|Non |
|DisplayName|string|Non |



### Élément


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|ItemInternalId|string|Non |



### TablesList


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|value|array|Non |



### ItemsList


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|value|array|Non |


## Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0518_2016-->