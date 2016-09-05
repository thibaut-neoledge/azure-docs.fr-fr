<properties
pageTitle="Utiliser le connecteur SharePoint Online dans vos applications logiques | Microsoft Azure"
description="Commencez à utiliser le connecteur SharePoint Online Azure App Service dans vos applications logiques."
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
ms.date="08/18/2016"
ms.author="deonhe"/>

# Prise en main du connecteur SharePoint Online 

Le connecteur SharePoint permet d’utiliser des listes dans SharePoint.

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.


Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## À propos des déclencheurs et des actions

Le connecteur SharePoint peut être utilisé en tant qu’action ; il possède un ou plusieurs déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML.

Le connecteur SharePoint met à votre disposition les actions et/ou les déclencheurs ci-après.

### Actions SharePoint
Vous pouvez effectuer les actions suivantes :

|Action|Description|
|--- | ---|
|GetFileMetadata|Obtenir les métadonnées d’un fichier dans la bibliothèque de documents|
|UpdateFile|Mettre à jour un fichier dans la bibliothèque de documents|
|DeleteFile|Supprimer un fichier dans la bibliothèque de documents|
|GetFileMetadataByPath|Obtenir les métadonnées d’un fichier dans la bibliothèque de documents|
|GetFileContentByPath|Obtenir un fichier dans la bibliothèque de documents|
|GetFileContent|Obtenir un fichier dans la bibliothèque de documents|
|CreateFile|Charger un fichier sur la bibliothèque de documents|
|CopyFile|Copier un fichier dans la bibliothèque de documents|
|ExtractFolderV2|Extraire un dossier de la bibliothèque de documents|
|PostItem|Crée un élément dans une liste SharePoint|
|GetItem|Récupère un élément unique d’une liste SharePoint|
|DeleteItem|Supprime un élément d’une liste SharePoint|
|PatchItem|Met à jour un élément dans une liste SharePoint|
### Déclencheurs SharePoint
Vous pouvez écouter les événements suivants :

|Déclencheur | Description|
|--- | ---|
|OnNewFile|Déclenche un flux quand un fichier est créé dans un dossier SharePoint.|
|OnUpdatedFile|Déclenche un flux quand un fichier est modifié dans un dossier SharePoint.|
|GetOnNewItems|Quand un élément est créé dans une liste SharePoint|
|GetOnUpdatedItems|Quand un élément existant est modifié dans une liste SharePoint|


## Créer une connexion à SharePoint
Pour utiliser le connecteur SharePoint, vous devez créer une **connexion**, puis fournir les détails de ces propriétés :

|Propriété| Requis|Description|
| ---|---|---|
|Jeton|Oui|Fournir les informations d’identification SharePoint|

Pour vous connecter à **SharePoint Online**, vous devez fournir votre identité (nom d’utilisateur et mot de passe, informations d’identification de la carte à puce, etc.) à SharePoint Online. Une fois que vous avez été authentifié, vous pouvez alors utiliser le connecteur SharePoint Online dans votre application logique.

Dans le concepteur de votre application logique, procédez comme suit pour vous connecter à SharePoint afin de créer la **connexion** à utiliser dans votre application logique :

1. Entrez SharePoint dans la zone de recherche et attendez que la recherche renvoie toutes les entrées dont le nom contient SharePoint : ![Configurer SharePoint][1]
2. Sélectionnez **SharePoint Online - Quand un fichier est créé**
3. Sélectionnez **Connexion à SharePoint Online** : ![Configurer SharePoint][2]
4. Entrez vos informations d’identification SharePoint pour vous connecter et vous authentifier auprès de SharePoint. ![Configurer SharePoint][3]
5. Une fois l’authentification terminée, vous êtes redirigé vers votre application logique pour la terminer en configurant la boîte de dialogue SharePoint **Quand un fichier est créé**. ![Configurer SharePoint][4]
6. Vous pouvez ensuite ajouter d’autres déclencheurs et actions dont vous avez besoin pour terminer votre application logique.
7. Enregistrez votre travail en sélectionnant **Enregistrer** sur la barre de menus supérieure.


## Informations de référence sur l’API REST de SharePoint
#### Cette documentation concerne la version 1.0.


### Obtenir les métadonnées d’un fichier dans la bibliothèque de documents
**```GET: /datasets/{dataset}/files/{id}```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|URL du site SharePoint. Par exemple, http://contoso.sharepoint.com/sites/mysite|
|id|string|yes|path|(aucun)|Identificateur unique du fichier|


### Voici les réponses possibles :

|Name|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Mettre à jour un fichier dans la bibliothèque de documents
**```PUT: /datasets/{dataset}/files/{id}```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|URL du site SharePoint. Par exemple, http://contoso.sharepoint.com/sites/mysite|
|id|string|yes|path|(aucun)|Identificateur unique du fichier|
|body| |yes|body|(aucun)|Contenu du fichier|


### Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Supprimer un fichier dans la bibliothèque de documents
**```DELETE: /datasets/{dataset}/files/{id}```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|URL du site SharePoint. Par exemple, http://contoso.sharepoint.com/sites/mysite|
|id|string|yes|path|(aucun)|Identificateur unique du fichier|


### Voici les réponses possibles :

|Name|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Obtenir les métadonnées d’un fichier dans la bibliothèque de documents
**```GET: /datasets/{dataset}/GetFileByPath```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|URL du site SharePoint. Par exemple, http://contoso.sharepoint.com/sites/mysite|
|path|string|yes|query|(aucun)|Chemin du fichier|


### Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Obtenir un fichier dans la bibliothèque de documents
**```GET: /datasets/{dataset}/GetFileContentByPath```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|URL du site SharePoint. Par exemple, http://contoso.sharepoint.com/sites/mysite|
|path|string|yes|query|(aucun)|Chemin du fichier|


### Voici les réponses possibles :

|Name|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Obtenir un fichier dans la bibliothèque de documents
**```GET: /datasets/{dataset}/files/{id}/content```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|URL du site SharePoint. Par exemple, http://contoso.sharepoint.com/sites/mysite|
|id|string|yes|path|(aucun)|Identificateur unique du fichier|


### Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Charger un fichier sur la bibliothèque de documents
**```POST: /datasets/{dataset}/files```**



| Name| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|URL du site SharePoint. Par exemple, http://contoso.sharepoint.com/sites/mysite|
|folderPath|string|yes|query|(aucun)|Chemin du dossier|
|name|string|yes|query|(aucun)|Nom du fichier|
|body| |yes|body|(aucun)|Contenu du fichier|


### Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Copier un fichier dans la bibliothèque de documents
**```POST: /datasets/{dataset}/copyFile```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|URL du site SharePoint. Par exemple, http://contoso.sharepoint.com/sites/mysite|
|source|string|yes|query|(aucun)|Chemin du fichier source|
|destination|string|yes|query|(aucun)|Chemin du fichier de destination|
|overwrite|booléenne|no|query|false|Indique si un fichier existant doit être remplacé|


### Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Déclenche un flux quand un fichier est créé dans un dossier SharePoint.
**```GET: /datasets/{dataset}/triggers/onnewfile```**



| Name| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|URL du site SharePoint|
|folderId|string|yes|query|(aucun)|Identificateur unique du dossier dans SharePoint|


### Voici les réponses possibles :

|Name|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Déclenche un flux quand un fichier est modifié dans un dossier SharePoint.
**```GET: /datasets/{dataset}/triggers/onupdatedfile```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|URL du site SharePoint|
|folderId|string|yes|query|(aucun)|Identificateur unique du dossier dans SharePoint|


### Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Extraire un dossier de la bibliothèque de documents
**```POST: /datasets/{dataset}/extractFolderV2```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|URL du site SharePoint. Par exemple, http://contoso.sharepoint.com/sites/mysite|
|source|string|yes|query|(aucun)|Chemin du fichier source|
|destination|string|yes|query|(aucun)|Chemin du dossier de destination|
|overwrite|booléenne|no|query|false|Indique si un fichier existant doit être remplacé|


### Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Quand un élément est créé dans une liste SharePoint
**```GET: /datasets/{dataset}/tables/{table}/onnewitems```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|URL du site SharePoint (exemple : http://contoso.sharepoint.com/sites/mysite)|
|table|string|yes|path|(aucun)|Nom de la liste SharePoint|
|$skip|integer|no|query|(aucun)|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|integer|no|query|(aucun)|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|
|$filter|string|no|query|(aucun)|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|string|no|query|(aucun)|Requête orderBy ODATA pour spécifier l’ordre des entrées|


### Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Quand un élément existant est modifié dans une liste SharePoint
**```GET: /datasets/{dataset}/tables/{table}/onupdateditems```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|URL du site SharePoint (exemple : http://contoso.sharepoint.com/sites/mysite)|
|table|string|yes|path|(aucun)|Nom de la liste SharePoint|
|$skip|integer|no|query|(aucun)|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|integer|no|query|(aucun)|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|
|$filter|string|no|query|(aucun)|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|string|no|query|(aucun)|Requête orderBy ODATA pour spécifier l’ordre des entrées|


### Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Crée un élément dans une liste SharePoint
**```POST: /datasets/{dataset}/tables/{table}/items```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|URL du site SharePoint (exemple : http://contoso.sharepoint.com/sites/mysite)|
|table|string|yes|path|(aucun)|Nom de la liste SharePoint|
|item| |yes|body|(aucun)|Élément à créer|


### Voici les réponses possibles :

|Name|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Récupère un élément unique d’une liste SharePoint
**```GET: /datasets/{dataset}/tables/{table}/items/{id}```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|URL du site SharePoint (exemple : http://contoso.sharepoint.com/sites/mysite)|
|table|string|yes|path|(aucun)|Nom de la liste SharePoint|
|id|integer|yes|path|(aucun)|Identificateur unique de l’élément à récupérer|


### Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Supprime un élément d’une liste SharePoint
**```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|URL du site SharePoint (exemple : http://contoso.sharepoint.com/sites/mysite)|
|table|string|yes|path|(aucun)|Nom de la liste SharePoint|
|id|integer|yes|path|(aucun)|Identificateur unique de l’élément à supprimer|


### Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Met à jour un élément dans une liste SharePoint
**```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```**



| Name| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun)|URL du site SharePoint (exemple : http://contoso.sharepoint.com/sites/mysite)|
|table|string|yes|path|(aucun)|Nom de la liste SharePoint|
|id|integer|yes|path|(aucun)|Identificateur unique de l’élément à mettre à jour|
|item| |yes|body|(aucun)|Élément avec des propriétés modifiées|


### Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



## Définition(s) d'objet : 

 **DataSetsMetadata** :

Propriétés requises pour DataSetsMetadata :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|tabular|non défini|
|objet blob|non défini|



 **TabularDataSetsMetadata** :

Propriétés requises pour TabularDataSetsMetadata :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Name | Type de données |
|---|---|
|source|string|
|displayName|string|
|urlEncoding|string|
|tableDisplayName|string|
|tablePluralName|string|



 **BlobDataSetsMetadata** :

Propriétés requises pour BlobDataSetsMetadata :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|source|string|
|displayName|string|
|urlEncoding|string|



 **BlobMetadata** :

Propriétés requises pour BlobMetadata :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|ID|string|
|Nom|string|
|DisplayName|string|
|Chemin|string|
|LastModified|string|
|Taille|integer|
|MediaType|string|
|IsFolder|booléenne|
|ETag|string|
|FileLocator|string|



 **Object** :

Propriétés requises pour Object :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Name | Type de données |
|---|---|



 **TableMetadata** :

Propriétés requises pour TableMetadata :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Name | Type de données |
|---|---|
|name|string|
|title|string|
|x-ms-permission|string|
|schema|non défini|



 **DataSetsList** :

Propriétés requises pour DataSetsList :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|value|array|



 **DataSet** :

Propriétés requises pour DataSet :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Name | Type de données |
|---|---|
|Name|string|
|DisplayName|string|



 **Table** :

Propriétés requises pour Table :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|Nom|string|
|DisplayName|string|



 **Item** :

Propriétés requises pour Item :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Name | Type de données |
|---|---|
|ItemInternalId|string|



 **ItemsList** :

Propriétés requises pour ItemsList :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|value|array|



 **TablesList** :

Propriétés requises pour TablesList :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|value|array|


## Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png

<!---HONumber=AcomDC_0824_2016-->