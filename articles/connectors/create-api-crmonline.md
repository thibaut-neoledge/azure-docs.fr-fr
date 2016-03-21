<properties
pageTitle="Ajouter l’API Dynamics CRM Online à PowerApps Enterprise ou à vos applications logiques | Microsoft Azure"
description="Vue d’ensemble de l’API CRM Online avec les paramètres de l’API REST"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor="" tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="03/02/2016"
ms.author="deonhe"/>

# Prendre en main l’API CRM
Connectez-vous à Dynamics CRM Online pour créer un enregistrement, mettre à jour un élément et bien plus encore. L’API CRM Online peut être utilisée à partir de :

- Logic Apps
- PowerApps

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/create-api-crmonline.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-crmonline.md)

Avec CRM Online, vous pouvez effectuer les opérations suivantes :

- Créer votre flux d’activité en fonction des données que vous obtenez de CRM Online. 
- Utiliser des actions pour supprimer un enregistrement, obtenir des entités et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, quand un élément est mis à jour dans CRM, vous pouvez envoyer un message électronique à l’aide d’Office 365.


Pour plus d’informations sur l’ajout d’une API à PowerApps Enterprise, consultez [Inscrire une API dans PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions
L’API CRM inclut les actions suivantes. Il n’y a aucun déclencheur.

| Déclencheurs | Actions|
| --- | --- |
|Aucun| <ul><li>Créer un enregistrement</li><li>Obtenir des enregistrements</li><li>Supprimer un enregistrement</li><li>Obtenir un enregistrement</li><li>Obtenir des entités</li><li>Mettre à jour un élément</li></ul>

Toutes les API prennent en charge les données aux formats JSON et XML.

## Créer une connexion à CRM Online

Quand vous ajoutez cette API à vos applications logiques, vous devez vous connecter à Dynamics CRM Online. Suivez ces étapes pour vous connecter à CRM Online et terminer la configuration de la **connexion** dans votre application logique :

1. Dans votre application logique, sélectionnez **Ajouter une action** : ![Configurer CRM Online][13]
4. Entrez CRM dans la zone de recherche et attendez que la recherche renvoie toutes les entrées contenant CRM dans leur nom.
5. Sélectionnez **Dynamics CRM Online : créer un enregistrement**.
6. Sélectionnez **Connexion à Dynamics CRM Online** : ![Configurer CRM Online][14]
7. Entrez vos informations d’identification CRM Online pour vous connecter et autoriser l’application : ![Configurer CRM Online][15]  
8. Après vous être connecté, revenez à votre application logique pour la terminer en ajoutant les autres déclencheurs et actions dont vous avez besoin.
9. Enregistrez votre travail en sélectionnant **Enregistrer** sur la barre de menu supérieure.

Après avoir créé la connexion, vous entrez les propriétés CRM Online, telles que la table ou le jeu de données. La section **Informations de référence sur l’API REST** dans cette rubrique décrit ces propriétés.

>[AZURE.TIP] Vous pouvez utiliser cette connexion dans d’autres applications logiques.

## Informations de référence sur l'API REST Swagger
S’applique à la version 1.0.

### Créer un enregistrement 
Créer un enregistrement dans une entité. ```POST: /datasets/{dataset}/tables/{table}/items```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|Nom unique de l’organisation CRM contoso.crm|
|table|string|yes|path|(aucun)|Nom de l’entité|
|item| |yes|body|(aucun)|Enregistrement à créer|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir des enregistrements 
 Obtenir les enregistrements d’une entité. ```GET: /datasets/{dataset}/tables/{table}/items```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|Nom unique de l’organisation CRM contoso.crm|
|table|string|yes|path|(aucun)|Nom de l’entité|
|$skip|integer|no|query|(aucun)|Nombre d’entrées à ignorer. La valeur par défaut est 0.|
|$top|integer|no|query|(aucun)|Nombre maximal d’entrées à récupérer. La valeur par défaut est 100.|
|$filter|string|no|query|(aucun)|Requête filter ODATA pour limiter le nombre d’entrées.|
|$orderby|string|no|query|(aucun)|Requête orderBy ODATA pour spécifier l’ordre des entrées.|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|




### Retourner les jeux de données 
 Retourner les jeux de données. ```GET: /datasets```

Il n’existe aucun paramètre pour cet appel.

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|



### Obtenir un élément de table 
Permet d’obtenir un enregistrement spécifique pour une entité CRM. ```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|Nom unique de l’organisation CRM contoso.crm|
|table|string|yes|path|(aucun)|Nom de l’entité|
|id|string|yes|path|(aucun)|Identificateur de l’enregistrement|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|

### Supprimer un élément d’une liste 
Supprimer un élément d’une liste. ```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|Nom unique de l’organisation CRM contoso.crm|
|table|string|yes|path|(aucun)|Nom de l’entité|
|id|string|yes|path|(aucun)|Identificateur de l’enregistrement|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|



### Corrige un élément existant de la table 
Permet de mettre partiellement à jour un enregistrement existant pour une entité CRM. ```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|Nom unique de l’organisation CRM contoso.crm|
|table|string|yes|path|(aucun)|Nom de l’entité|
|id|string|yes|path|(aucun)|Identificateur de l’enregistrement|
|item| |yes|body|(aucun)|Enregistrement à mettre à jour|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|

### Obtenir des entités 
Permet d’obtenir la liste des entités présentes dans une instance CRM. ```GET: /datasets/{dataset}/tables```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|Nom unique de l’organisation CRM contoso.crm|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## Définitions d'objet

#### DataSetsMetadata

|Nom de la propriété | Type de données | Requis|
|---|---|---|
|tabular|non défini|no|
|objet blob|non défini|no|

#### TabularDataSetsMetadata

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|source|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|

#### BlobDataSetsMetadata

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|source|string|no|
|displayName|string|no|
|urlEncoding|string|no|


#### TableMetadata

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|non défini|no|

#### DataSetsList

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|value|array|no|

#### DataSet

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|Nom|string|no|
|DisplayName|string|no|


#### Table

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|Nom|string|no|
|DisplayName|string|no|

#### Item

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|ItemInternalId|string|no|

#### ItemsList

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|value|array|no|


#### TablesList

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|value|array|no|


## Étapes suivantes

[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

Revenir à la [liste des API](apis-list.md).


[9]: ./media/create-api-crmonline/aad-tenant-applications-add-appinfo.png
[10]: ./media/create-api-crmonline/aad-tenant-applications-add-app-properties.png
[12]: ./media/create-api-crmonline/contoso-aad-app-configure.png
[13]: ./media/create-api-crmonline/crmconfig1.png
[14]: ./media/create-api-crmonline/crmconfig2.png
[15]: ./media/create-api-crmonline/crmconfig3.png

<!------HONumber=AcomDC_0309_2016-->