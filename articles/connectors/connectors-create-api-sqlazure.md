<properties
	pageTitle="Ajouter l'API SQL Azure à vos applications logiques | Microsoft Azure"
	description="Vue d'ensemble de l'API SQL Azure avec les paramètres de l'API REST"
	services=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="05/16/2016"
   ms.author="mandia"/>


# Prise en main de l'API SQL Azure
Connectez-vous à Base de données SQL Azure pour gérer vos tables et vos lignes, par exemple pour insérer des lignes, obtenir des tables, et bien plus encore.

L’API Base de données SQL Azure peut être utilisée à partir de :

- Applications logiques (abordées dans cette rubrique)
- PowerApps [consultez [PowerApps connections list](https://powerapps.microsoft.com/tutorials/connections-list/) (Liste des connexions PowerApps) pour obtenir la liste complète]

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.

Avec Base de données SQL Azure, vous pouvez :

- Créer votre flux d’activité en fonction des données que vous obtenez de Base de données SQL Azure. 
- Utiliser des actions pour obtenir une ligne, insérer une ligne, et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d'autres actions. Par exemple, vous pouvez obtenir une ligne de données à partir de Base de données SQL Azure, puis ajouter ces données à Excel. 

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Déclencheurs et actions
SQL inclut les actions suivantes. Il n'y a aucun déclencheur.

Déclencheurs | Actions
--- | ---
Aucun | <ul><li>Obtenir une ligne</li><li>Obtenir des lignes</li><li>Insérer une ligne</li><li>Supprimer une ligne</li><li>Obtenir des tables</li><li>Mettre à jour une ligne</li></ul>

Toutes les API prennent en charge les données aux formats JSON et XML.

## Créer la connexion à SQL
Quand vous ajoutez cette API à vos applications logiques, entrez les valeurs suivantes :

|Propriété| Requis|Description|
| ---|---|---|
|SQL Connection String|Oui|Entrer votre chaîne de connexion Base de données SQL Azure|

Après avoir créé la connexion, vous entrez vos propriétés SQL, telles que le nom de la table. La section **Informations de référence sur l’API REST** de cette rubrique décrit ces propriétés.

>[AZURE.TIP] Vous pouvez utiliser cette connexion dans d’autres applications logiques.

## Informations de référence sur l'API REST Swagger
S’applique à la version 1.0.

### Obtenir une ligne 
Récupère une seule ligne d’une table SQL. ```GET: /datasets/default/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Nom de la table SQL|
|id|string|yes|path|(aucun)|Identificateur unique de la ligne à récupérer|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir des lignes 
Récupère les lignes d’une table SQL. ```GET: /datasets/default/tables/{table}/items```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Nom de la table SQL|
|$skip|integer|no|query|(aucun)|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|integer|no|query|(aucun)|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|
|$filter|string|no|query|(aucun)|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|string|no|query|(aucun)|Requête orderBy ODATA pour spécifier l’ordre des entrées|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Insérer une ligne 
Insère une nouvelle ligne dans une table SQL. ```POST: /datasets/default/tables/{table}/items```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Nom de la table SQL|
|item|ItemInternalId: string|yes|body|(aucun)|Ligne à insérer dans la table SQL spécifiée|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Supprimer la ligne 
Supprime une ligne d’une table SQL. ```DELETE: /datasets/default/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Nom de la table SQL|
|id|string|yes|path|(aucun)|Identificateur unique de la ligne à supprimer|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir des tables 
Récupère les tables d’une base de données SQL. ```GET: /datasets/default/tables```

Il n'existe aucun paramètre pour cet appel.

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Mettre à jour une ligne 
Met à jour une ligne existante dans une table SQL. ```PATCH: /datasets/default/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Nom de la table SQL|
|id|string|yes|path|(aucun)|Identificateur unique de la ligne à mettre à jour|
|item|ItemInternalId: string|yes|body|(aucun)|Ligne avec valeurs mises à jour|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|

## Définitions d'objet

#### DataSetsMetadata

|Nom de la propriété | Type de données | Requis |
|---|---|---|
|tabular|non défini|no|
|objet blob|non défini|no|

#### TabularDataSetsMetadata

|Nom de la propriété | Type de données | Requis |
|---|---|---|
|source|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|

#### BlobDataSetsMetadata

|Nom de la propriété | Type de données |Requis |
|---|---|---|
|source|string|no|
|displayName|string|no|
|urlEncoding|string|no|

#### TableMetadata

|Nom de la propriété | Type de données |Requis |
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|non défini|no|

#### DataSetsList

|Nom de la propriété | Type de données |Requis |
|---|---|---|
|value|array|no|

#### DataSet

|Nom de la propriété | Type de données |Requis |
|---|---|---|
|Nom|string|no|
|DisplayName|string|no|

#### Table

|Nom de la propriété | Type de données |Requis |
|---|---|---|
|Nom|string|no|
|DisplayName|string|no|

#### Item

|Nom de la propriété | Type de données |Requis |
|---|---|---|
|ItemInternalId|string|no|

#### ItemsList

|Nom de la propriété | Type de données |Requis |
|---|---|---|
|value|array|no|

#### TablesList

|Nom de la propriété | Type de données |Requis |
|---|---|---|
|value|array|no|


## Étapes suivantes

[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0518_2016-->