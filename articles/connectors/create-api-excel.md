<properties
pageTitle="Ajouter l’API Excel à PowerApps Enterprise | Microsoft Azure"
description="Vue d'ensemble de l'API Excel avec les paramètres de l'API REST"
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
ms.date="02/25/2016"
ms.author="deonhe"/>

# Prendre en main l’API Excel

Connectez-vous à Excel pour insérer une ligne, supprimer une ligne, et bien plus encore. L’API Excel peut être utilisée à partir de :

- PowerApps

Avec Excel, vous pouvez :

- Ajouter l’API Excel à PowerApps Enterprise. Ensuite, vos utilisateurs peuvent utiliser cette API dans leurs applications. 

Pour plus d’informations sur l’ajout d’une API à PowerApps Enterprise, consultez [Inscrire une API dans PowerApps](../power-apps/powerapps-register-from-available-apis.md).

## Déclencheurs et actions
Excel inclut les actions suivantes. Il n'y a aucun déclencheur.

|Déclencheur|Actions|
|--- | ---|
|Aucun | <ul><li>Obtenir des lignes</li><li>Insérer une ligne</li><li>Supprimer une ligne</li><li>Obtenir une ligne</li><li>Obtenir des tables</li><li>Mettre à jour une ligne</li></ul>

Toutes les API prennent en charge les données aux formats JSON et XML.

## Informations de référence sur l'API REST Swagger
S’applique à la version 1.0.

### Insère une nouvelle ligne dans le tableau Excel.
```POST: /datasets/{dataset}/tables/{table}/items```



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|Nom du fichier Excel|
|table|string|yes|path|(aucun)|Nom du tableau Excel|
|item| |yes|body|(aucun)|Ligne à insérer dans le tableau Excel spécifié|


### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|




### Récupère une seule ligne d'un tableau Excel
```GET: /datasets/{dataset}/tables/{table}/items/{id}```



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|Nom du fichier Excel|
|table|string|yes|path|(aucun)|Nom du tableau Excel|
|id|string|yes|path|(aucun)|Identificateur unique de la ligne à récupérer|


### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|




### Supprime une ligne d'un tableau Excel
```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|Nom du fichier Excel|
|table|string|yes|path|(aucun)|Nom du tableau Excel|
|id|string|yes|path|(aucun)|Identificateur unique de la ligne à supprimer|


### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|




### Met à jour une ligne existante dans un tableau Excel
```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|Nom du fichier Excel|
|table|string|yes|path|(aucun)|Nom du tableau Excel|
|id|string|yes|path|(aucun)|Identificateur unique de la ligne à mettre à jour|
|item| |yes|body|(aucun)|Ligne avec valeurs mises à jour|


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

| Nom | Type de données |Requis|
|---|---|---|
|source|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|

#### BlobDataSetsMetadata

| Nom | Type de données |Requis|
|---|---|---|
|source|string|no|
|displayName|string|no|
|urlEncoding|string|no|

#### TableMetadata

| Nom | Type de données |Requis|
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|non défini|no|

#### DataSetsList

| Nom | Type de données |Requis|
|---|---|---|
|value|array|no|

#### DataSet

| Nom | Type de données |Requis|
|---|---|---|
|Nom|string|no|
|DisplayName|string|no|

#### Table

| Nom | Type de données |Requis|
|---|---|---|
|Nom|string|no|
|DisplayName|string|no|

#### Item

| Nom | Type de données |Requis|
|---|---|---|
|ItemInternalId|string|no|

#### TablesList

| Nom | Type de données |Requis|
|---|---|---|
|value|array|no|

#### ItemsList

| Nom | Type de données |Requis|
|---|---|---|
|value|array|no|


## Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md) [Créer une application PowerApps](../power-apps/powerapps-get-started-azure-portal.md)

<!---HONumber=AcomDC_0302_2016-->