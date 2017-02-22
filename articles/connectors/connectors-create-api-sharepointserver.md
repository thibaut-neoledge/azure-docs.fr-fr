---
title: "Utiliser le connecteur SharePoint Online dans vos applications logiques | Microsoft Docs"
description: "Commencer à utiliser le connecteur SharePoint Online dans vos applications logiques"
services: 
documentationcenter: 
author: msftman
manager: anneta
editor: 
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: c462c98e2ecef1afe62ef921265b8842b6ae717d


---
# <a name="get-started-with-the-sharepoint-online-connector"></a>Prise en main du connecteur SharePoint Online
Le connecteur SharePoint permet d’utiliser des listes dans SharePoint.

> [!NOTE]
> Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.
> 
> 

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="lets-talk-about-triggers-and-actions"></a>À propos des déclencheurs et des actions
Le connecteur SharePoint peut être utilisé en tant qu’action ; il possède un ou plusieurs déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML. 

Le connecteur SharePoint met à votre disposition les actions et/ou les déclencheurs ci-après :

### <a name="sharepoint-actions"></a>Actions SharePoint
Vous pouvez effectuer les actions suivantes :

| Action | Description |
| --- | --- |
| GetFileMetadata |Obtenir les métadonnées d’un fichier dans la bibliothèque de documents |
| UpdateFile |Mettre à jour un fichier dans la bibliothèque de documents |
| DeleteFile |Supprimer un fichier dans la bibliothèque de documents |
| GetFileMetadataByPath |Obtenir les métadonnées d’un fichier dans la bibliothèque de documents |
| GetFileContentByPath |Obtenir un fichier dans la bibliothèque de documents |
| GetFileContent |Obtenir un fichier dans la bibliothèque de documents |
| CreateFile |Charger un fichier sur la bibliothèque de documents |
| CopyFile |Copier un fichier dans la bibliothèque de documents |
| ExtractFolderV2 |Extraire un dossier de la bibliothèque de documents |
| PostItem |Crée un élément dans une liste SharePoint |
| GetItem |Récupère un élément unique d’une liste SharePoint |
| DeleteItem |Supprime un élément d’une liste SharePoint |
| PatchItem |Met à jour un élément dans une liste SharePoint |

### <a name="sharepoint-triggers"></a>Déclencheurs SharePoint
Vous pouvez écouter les événements suivants :

| Déclencheur | Description |
| --- | --- |
| OnNewFile |Déclenche un flux quand un fichier est créé dans un dossier SharePoint. |
| OnUpdatedFile |Déclenche un flux quand un fichier est modifié dans un dossier SharePoint. |
| GetOnNewItems |Quand un élément est créé dans une liste SharePoint |
| GetOnUpdatedItems |Quand un élément existant est modifié dans une liste SharePoint |

## <a name="create-a-connection-to-sharepoint"></a>Créer une connexion à SharePoint
Pour utiliser le connecteur SharePoint, vous devez créer une **connexion** , puis fournir les détails de ces propriétés : 

| Propriété | Requis | Description |
| --- | --- | --- |
| Jeton |Oui |Fournir les informations d’identification SharePoint |

Pour vous connecter à **SharePoint Online**, vous devez fournir votre identité (nom d’utilisateur et mot de passe, informations d’identification de la carte à puce, etc.) à SharePoint Online. Une fois que vous avez été authentifié, vous pouvez utiliser le connecteur SharePoint Online dans votre application logique. 

Dans le Concepteur de votre application logique, procédez comme suit pour vous connecter à SharePoint afin de créer la **connexion** à utiliser dans votre application logique :

1. Entrez SharePoint dans la zone de recherche et attendez que la recherche renvoie toutes les entrées contenant SharePoint dans leur nom :    
   ![Configurer SharePoint][1]  
2. Sélectionnez **SharePoint Online - Quand un fichier est créé**   
3. Sélectionnez **Connexion à SharePoint Online** :   
   ![Configurer SharePoint][2]    
4. Entrez vos informations d’identification SharePoint pour vous connecter et vous authentifier auprès de SharePoint    
   ![Configurer SharePoint][3]     
5. Une fois l’authentification terminée, vous serez redirigé vers votre application logique pour la terminer en configurant la boîte de dialogue **Quand un fichier est créé** de SharePoint.          
   ![Configurer SharePoint][4]  
6. Vous pouvez ensuite ajouter d’autres déclencheurs et actions dont vous avez besoin pour terminer votre application logique.   
7. Enregistrez votre travail en sélectionnant **Enregistrer** sur la barre de menus supérieure.  

## <a name="sharepoint-rest-api-reference"></a>Informations de référence sur l’API REST de SharePoint
#### <a name="this-documentation-is-for-version-10"></a>Cette documentation concerne la version 1.0.
### <a name="used-for-getting-a-file-metadata-on-document-library"></a>Obtenir les métadonnées d’un fichier dans la bibliothèque de documents
**```GET: /datasets/{dataset}/files/{id}```** 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Oui |path |(aucun) |URL du site SharePoint. Par exemple, http://contoso.sharepoint.com/sites/monsite |
| id |string |Oui |path |(aucun) |Identificateur unique du fichier |

### <a name="here-are-the-possible-responses"></a>Voici les réponses possibles :
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

- - -
### <a name="used-for-updating-a-file-on-document-library"></a>Mettre à jour un fichier dans la bibliothèque de documents
**```PUT: /datasets/{dataset}/files/{id}```** 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Oui |path |(aucun) |URL du site SharePoint. Par exemple, http://contoso.sharepoint.com/sites/monsite |
| id |string |Oui |path |(aucun) |Identificateur unique du fichier |
| body | |Oui |body |(aucun) |Contenu du fichier |

### <a name="here-are-the-possible-responses"></a>Voici les réponses possibles :
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

- - -
### <a name="used-for-deleting-a-file-on-document-library"></a>Supprimer un fichier dans la bibliothèque de documents
**```DELETE: /datasets/{dataset}/files/{id}```** 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Oui |path |(aucun) |URL du site SharePoint. Par exemple, http://contoso.sharepoint.com/sites/monsite |
| id |string |Oui |path |(aucun) |Identificateur unique du fichier |

### <a name="here-are-the-possible-responses"></a>Voici les réponses possibles :
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

- - -
### <a name="used-for-getting-a-file-metadata-on-document-library"></a>Obtenir les métadonnées d’un fichier dans la bibliothèque de documents
**```GET: /datasets/{dataset}/GetFileByPath```** 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Oui |path |(aucun) |URL du site SharePoint. Par exemple, http://contoso.sharepoint.com/sites/monsite |
| path |string |yes |query |(aucun) |Chemin du fichier |

### <a name="here-are-the-possible-responses"></a>Voici les réponses possibles :
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

- - -
### <a name="used-for-getting-a-file-on-document-library"></a>Obtenir un fichier dans la bibliothèque de documents
**```GET: /datasets/{dataset}/GetFileContentByPath```** 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Oui |path |(aucun) |URL du site SharePoint. Par exemple, http://contoso.sharepoint.com/sites/monsite |
| path |string |yes |query |(aucun) |Chemin du fichier |

### <a name="here-are-the-possible-responses"></a>Voici les réponses possibles :
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

- - -
### <a name="used-for-getting-a-file-on-document-library"></a>Obtenir un fichier dans la bibliothèque de documents
**```GET: /datasets/{dataset}/files/{id}/content```** 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Oui |path |(aucun) |URL du site SharePoint. Par exemple, http://contoso.sharepoint.com/sites/monsite |
| id |string |Oui |path |(aucun) |Identificateur unique du fichier |

### <a name="here-are-the-possible-responses"></a>Voici les réponses possibles :
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

- - -
### <a name="used-for-uploading-a-file-on-document-library"></a>Charger un fichier sur la bibliothèque de documents
**```POST: /datasets/{dataset}/files```** 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Oui |path |(aucun) |URL du site SharePoint. Par exemple, http://contoso.sharepoint.com/sites/monsite |
| folderPath |string |yes |query |(aucun) |Chemin du dossier |
| Nom |string |yes |query |(aucun) |Nom du fichier |
| body | |Oui |body |(aucun) |Contenu du fichier |

### <a name="here-are-the-possible-responses"></a>Voici les réponses possibles :
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

- - -
### <a name="used-for-copying-a-file-on-document-library"></a>Copier un fichier dans la bibliothèque de documents
**```POST: /datasets/{dataset}/copyFile```** 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Oui |path |(aucun) |URL du site SharePoint. Par exemple, http://contoso.sharepoint.com/sites/monsite |
| source |string |yes |query |(aucun) |Chemin du fichier source |
| destination |string |yes |query |(aucun) |Chemin du fichier de destination |
| overwrite |booléenne |no |query |false |Indique si un fichier existant doit être remplacé |

### <a name="here-are-the-possible-responses"></a>Voici les réponses possibles :
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

- - -
### <a name="triggers-a-flow-when-a-new-file-is-created-in-a-sharepoint-folder"></a>Déclenche un flux quand un fichier est créé dans un dossier SharePoint.
**```GET: /datasets/{dataset}/triggers/onnewfile```** 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Oui |path |(aucun) |URL du site SharePoint |
| folderId |string |yes |query |(aucun) |Identificateur unique du dossier dans SharePoint |

### <a name="here-are-the-possible-responses"></a>Voici les réponses possibles :
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

- - -
### <a name="triggers-a-flow-when-a-file-is-modified-in-a-sharepoint-folder"></a>Déclenche un flux quand un fichier est modifié dans un dossier SharePoint.
**```GET: /datasets/{dataset}/triggers/onupdatedfile```** 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Oui |path |(aucun) |URL du site SharePoint |
| folderId |string |yes |query |(aucun) |Identificateur unique du dossier dans SharePoint |

### <a name="here-are-the-possible-responses"></a>Voici les réponses possibles :
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

- - -
### <a name="used-for-extracting-a-folder-on-document-library"></a>Extraire un dossier de la bibliothèque de documents
**```POST: /datasets/{dataset}/extractFolderV2```** 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Oui |path |(aucun) |URL du site SharePoint. Par exemple, http://contoso.sharepoint.com/sites/monsite |
| source |string |yes |query |(aucun) |Chemin du fichier source |
| destination |string |yes |query |(aucun) |Chemin du dossier de destination |
| overwrite |booléenne |no |query |false |Indique si un fichier existant doit être remplacé |

### <a name="here-are-the-possible-responses"></a>Voici les réponses possibles :
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

- - -
### <a name="when-a-new-item-is-created-in-a-sharepoint-list"></a>Quand un élément est créé dans une liste SharePoint
**```GET: /datasets/{dataset}/tables/{table}/onnewitems```** 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Oui |path |(aucun) |URL de site SharePoint (exemple : http://contoso.sharepoint.com/sites/monsite) |
| table |string |Oui |path |(aucun) |Nom de la liste SharePoint |
| $skip |integer |no |query |Aucun |Nombre d’entrées à ignorer (valeur par défaut : 0) |
| $top |integer |no |query |Aucun |Nombre maximal d’entrées à récupérer (valeur par défaut : 256) |
| $filter |string |no |query |Aucun |Requête filter ODATA pour limiter le nombre d’entrées |
| $orderby |string |no |query |Aucun |Requête orderBy ODATA pour spécifier l’ordre des entrées |

### <a name="here-are-the-possible-responses"></a>Voici les réponses possibles :
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

- - -
### <a name="when-an-existing-item-is-modified-in-a-sharepoint-list"></a>Quand un élément existant est modifié dans une liste SharePoint
**```GET: /datasets/{dataset}/tables/{table}/onupdateditems```** 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Oui |path |(aucun) |URL de site SharePoint (exemple : http://contoso.sharepoint.com/sites/monsite) |
| table |string |Oui |path |(aucun) |Nom de la liste SharePoint |
| $skip |integer |no |query |Aucun |Nombre d’entrées à ignorer (valeur par défaut : 0) |
| $top |integer |no |query |Aucun |Nombre maximal d’entrées à récupérer (valeur par défaut : 256) |
| $filter |string |no |query |Aucun |Requête filter ODATA pour limiter le nombre d’entrées |
| $orderby |string |no |query |Aucun |Requête orderBy ODATA pour spécifier l’ordre des entrées |

### <a name="here-are-the-possible-responses"></a>Voici les réponses possibles :
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

- - -
### <a name="creates-a-new-item-in-a-sharepoint-list"></a>Crée un élément dans une liste SharePoint
**```POST: /datasets/{dataset}/tables/{table}/items```** 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Oui |path |(aucun) |URL de site SharePoint (exemple : http://contoso.sharepoint.com/sites/monsite) |
| table |string |Oui |path |(aucun) |Nom de la liste SharePoint |
| item | |Oui |body |(aucun) |Élément à créer |

### <a name="here-are-the-possible-responses"></a>Voici les réponses possibles :
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

- - -
### <a name="retrieves-a-single-item-from-a-sharepoint-list"></a>Récupère un élément unique d’une liste SharePoint
**```GET: /datasets/{dataset}/tables/{table}/items/{id}```** 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Oui |path |(aucun) |URL de site SharePoint (exemple : http://contoso.sharepoint.com/sites/monsite) |
| table |string |Oui |path |(aucun) |Nom de la liste SharePoint |
| id |integer |Oui |path |(aucun) |Identificateur unique de l’élément à récupérer |

### <a name="here-are-the-possible-responses"></a>Voici les réponses possibles :
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

- - -
### <a name="deletes-an-item-from-a-sharepoint-list"></a>Supprime un élément d’une liste SharePoint
**```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```** 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Oui |path |(aucun) |URL de site SharePoint (exemple : http://contoso.sharepoint.com/sites/monsite) |
| table |string |Oui |path |(aucun) |Nom de la liste SharePoint |
| id |integer |Oui |path |(aucun) |Identificateur unique de l’élément à supprimer |

### <a name="here-are-the-possible-responses"></a>Voici les réponses possibles :
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

- - -
### <a name="updates-an-item-in-a-sharepoint-list"></a>Met à jour un élément dans une liste SharePoint
**```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```** 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Oui |path |(aucun) |URL de site SharePoint (exemple : http://contoso.sharepoint.com/sites/monsite) |
| table |string |Oui |path |(aucun) |Nom de la liste SharePoint |
| id |integer |Oui |path |(aucun) |Identificateur unique de l’élément à mettre à jour |
| item | |Oui |body |(aucun) |Élément avec des propriétés modifiées |

### <a name="here-are-the-possible-responses"></a>Voici les réponses possibles :
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

- - -
## <a name="object-definitions"></a>Définition(s) d’objet :
 **DataSetsMetadata**:

Propriétés requises pour DataSetsMetadata :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| tabular |non défini |
| objet blob |non défini |

 **TabularDataSetsMetadata**:

Propriétés requises pour TabularDataSetsMetadata :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| source |string |
| displayName |string |
| urlEncoding |string |
| tableDisplayName |string |
| tablePluralName |string |

 **BlobDataSetsMetadata**:

Propriétés requises pour BlobDataSetsMetadata :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| source |string |
| displayName |string |
| urlEncoding |string |

 **BlobMetadata**:

Propriétés requises pour BlobMetadata :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| id |string |
| Nom |string |
| displayName |string |
| path |string |
| LastModified |string |
| Taille |integer |
| MediaType |string |
| IsFolder |booléenne |
| ETag |string |
| FileLocator |string |

 **Object**:

Propriétés requises pour Object :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
|  | |

 **TableMetadata**:

Propriétés requises pour TableMetadata :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| Nom |string |
| title |string |
| x-ms-permission |string |
| schema |non défini |

 **DataSetsList**:

Propriétés requises pour DataSetsList :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| value |array |

 **DataSet**:

Propriétés requises pour DataSet :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| Nom |string |
| displayName |string |

 **Table**:

Propriétés requises pour Table :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| Nom |string |
| displayName |string |

 **Item**:

Propriétés requises pour Item :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| ItemInternalId |string |

 **ItemsList**:

Propriétés requises pour ItemsList :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| value |array |

 **TablesList**:

Propriétés requises pour TablesList :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| value |array |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md)  

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png



<!--HONumber=Jan17_HO3-->


