---
title: Dropbox | Microsoft Docs
description: "Créez des applications logiques avec Azure App Service. Connectez-vous à Dropbox pour gérer vos fichiers. Vous pouvez exécuter différentes actions, comme charger, mettre à jour, obtenir et supprimer des fichiers dans Dropbox."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: cb0ae033-aba7-4ac9-beaa-be561a0f0cac
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: a8ecad3552f35c02c07fe33aa8531fb462b27002


---
# <a name="get-started-with-the-dropbox-connector"></a>Prise en main du connecteur Dropbox
Connectez-vous à Dropbox pour gérer vos fichiers. Vous pouvez exécuter différentes actions, comme charger, mettre à jour, obtenir et supprimer des fichiers dans Dropbox.

Pour utiliser [n’importe quel connecteur](apis-list.md), vous devez commencer par créer une application logique. Vous pouvez démarrer maintenant en [créant une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-dropbox"></a>Se connecter à Dropbox
Pour que votre application logique puisse accéder à un service, vous devez commencer par créer une *connexion* à celui-ci. Une connexion permet d’assurer la connectivité entre une application logique et un autre service. Par exemple, pour vous connecter à Dropbox, vous devez préalablement disposer d’une *connexion* Dropbox. Pour créer une connexion, vous devez fournir les informations d’identification que vous utilisez généralement pour accéder au service auquel vous souhaitez vous connecter. Ainsi, dans l’exemple Dropbox, entrez les informations d’identification de votre compte Dropbox pour créer la connexion correspondante. [Apprenez-en davantage sur les connexions]()

### <a name="create-a-connection-to-dropbox"></a>Créer une connexion à Dropbox
> [!INCLUDE [Steps to create a connection to Dropbox](../../includes/connectors-create-api-dropbox.md)]
> 
> 

## <a name="use-a-dropbox-trigger"></a>Utiliser un déclencheur Dropbox
Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. [En savoir plus sur les déclencheurs](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

Dans cet exemple, nous allons utiliser le déclencheur **Lorsqu’un fichier est créé**. Lorsque ce déclencheur apparaîtra, nous appellerons l’action Dropbox **Obtenir le contenu d’un fichier à l’aide du chemin**. 

1. Entrez *dropbox* dans la zone de recherche du concepteur Logic Apps, puis sélectionnez le déclencheur **Dropbox - Quand un fichier est créé**.      
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)  
2. Sélectionnez le dossier dans lequel vous souhaitez effectuer le suivi de la création de fichiers. Sélectionnez le bouton ... (encadré en rouge ci-après) et accédez au dossier que vous souhaitez sélectionner pour l’entrée du déclencheur.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)  

## <a name="use-a-dropbox-action"></a>Utiliser une action Dropbox
Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

Une fois le déclencheur ajouté, procédez comme suit pour ajouter une action qui récupérera le contenu du nouveau fichier.

1. Sélectionnez **+ Nouvelle étape** pour ajouter l’action à exécuter lorsqu’un fichier est créé.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)
2. Sélectionnez **Ajouter une action**. Ouvre la zone de recherche dans laquelle vous pouvez rechercher l’action que vous souhaitez effectuer.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)
3. Entrez *dropbox* pour rechercher les actions associées à Dropbox.  
4. Sélectionnez **Dropbox - Obtenir le contenu d’un fichier à l’aide du chemin** comme action à exécuter lorsqu’un fichier est créé dans le dossier Dropbox sélectionné. Le bloc de contrôles de l’action s’affiche. Vous serez invité à autoriser votre application logique à accéder à votre compte Dropbox, si vous ne l’avez pas fait précédemment.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  
5. Sélectionnez le bouton ... (situé à droite du contrôle **Chemin de fichier**) et recherchez le chemin d’accès au fichier que vous souhaitez utiliser. Une autre possibilité consiste à utiliser le jeton **chemin de fichier** pour accélérer la création de votre application logique.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  
6. Enregistrez votre travail et créez un fichier dans Dropbox pour activer votre workflow.  

## <a name="technical-details"></a>Détails techniques
Voici les détails des déclencheurs, actions et réponses pris en charge par cette connexion :

## <a name="dropbox-triggers"></a>Déclencheurs Dropbox
Le connecteur Dropbox comporte les déclencheurs suivants :  

| Déclencheur | Description |
| --- | --- |
| [Quand un fichier est créé](connectors-create-api-dropbox.md#when-a-file-is-created) |Cette opération déclenche un flux lorsqu’un fichier est créé dans un dossier. |
| [Quand un fichier est modifié](connectors-create-api-dropbox.md#when-a-file-is-modified) |Cette opération déclenche un flux lorsqu’un fichier est modifié dans un dossier. |

## <a name="dropbox-actions"></a>Actions Dropbox
Le connecteur Dropbox comporte les actions suivantes :

| Action | Description |
| --- | --- |
| [Obtenir les métadonnées d’un fichier](connectors-create-api-dropbox.md#get-file-metadata) |Cette opération obtient les métadonnées d’un fichier. |
| [Mettre à jour un fichier](connectors-create-api-dropbox.md#update-file) |Cette opération met à jour un fichier. |
| [Supprimer un fichier](connectors-create-api-dropbox.md#delete-file) |Cette opération supprime un fichier. |
| [Obtenir les métadonnées d’un fichier à l’aide du chemin](connectors-create-api-dropbox.md#get-file-metadata-using-path) |Cette opération obtient les métadonnées d’un fichier à l’aide du chemin. |
| [Obtenir le contenu d’un fichier à l’aide du chemin](connectors-create-api-dropbox.md#get-file-content-using-path) |Cette opération obtient le contenu d’un fichier à l’aide du chemin. |
| [Obtenir le contenu d’un fichier](connectors-create-api-dropbox.md#get-file-content) |Cette opération obtient le contenu d’un fichier. |
| [Créer un fichier](connectors-create-api-dropbox.md#create-file) |Cette opération crée un fichier. |
| [Copier un fichier](connectors-create-api-dropbox.md#copy-file) |Cette opération copie un fichier dans Dropbox. |
| [Répertorier les fichiers dans un dossier](connectors-create-api-dropbox.md#list-files-in-folder) |Cette opération obtient la liste des fichiers et sous-dossiers d’un dossier. |
| [Répertorier les fichiers dans le dossier racine](connectors-create-api-dropbox.md#list-files-in-root-folder) |Cette opération obtient la liste des fichiers et sous-dossiers du dossier racine. |
| [Extraire une archive dans un dossier](connectors-create-api-dropbox.md#extract-archive-to-folder) |Cette opération extrait un fichier d’archive dans un dossier (exemple : .zip). |

### <a name="action-details"></a>Détails de l’action
Voici les détails des actions et des déclencheurs de ce connecteur, ainsi que leurs réponses :

### <a name="get-file-metadata"></a>Obtenir les métadonnées d’un fichier
Cette opération obtient les métadonnées d’un fichier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| id* |Fichier |Sélectionner un fichier |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
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

### <a name="update-file"></a>Mettre à jour un fichier
Cette opération met à jour un fichier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| id* |Fichier |Sélectionner un fichier |
| body* |le contenu d’un fichier ; |Contenu du fichier |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
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

### <a name="delete-file"></a>Supprimer un fichier
Cette opération supprime un fichier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| id* |Fichier |Sélectionner un fichier |

Le caractère * indique qu’une propriété est obligatoire.

### <a name="get-file-metadata-using-path"></a>Obtenir les métadonnées d’un fichier à l’aide du chemin
Cette opération obtient les métadonnées d’un fichier à l’aide du chemin. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| path* |Chemin de fichier |Sélectionner un fichier |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
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

### <a name="get-file-content-using-path"></a>Obtenir le contenu d’un fichier à l’aide du chemin
Cette opération obtient le contenu d’un fichier à l’aide du chemin. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| path* |Chemin de fichier |Sélectionner un fichier |

Le caractère * indique qu’une propriété est obligatoire.

### <a name="get-file-content"></a>Obtenir le contenu d’un fichier
Cette opération obtient le contenu d’un fichier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| id* |Fichier |Sélectionner un fichier |

Le caractère * indique qu’une propriété est obligatoire.

### <a name="create-file"></a>Créer un fichier
Cette opération crée un fichier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| folderPath* |Chemin d’accès du dossier |Sélectionner un dossier |
| name* |Nom de fichier |Nom du fichier |
| body* |le contenu d’un fichier ; |Contenu du fichier |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
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

### <a name="copy-file"></a>Copier un fichier
Cette opération copie un fichier dans Dropbox. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| source* |URL de la source |URL du fichier source |
| destination* |Chemin de destination du fichier |Chemin de destination du fichier, y compris le nom de fichier cible |
| overwrite |Remplacer ? |Remplace le fichier de destination si la valeur est « true » |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
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

### <a name="when-a-file-is-created"></a>Quand un fichier est créé
Cette opération déclenche un flux lorsqu’un fichier est créé dans un dossier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| folderId* |Dossier |Sélectionner un dossier |

Le caractère * indique qu’une propriété est obligatoire.

### <a name="when-a-file-is-modified"></a>Quand un fichier est modifié
Cette opération déclenche un flux lorsqu’un fichier est modifié dans un dossier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| folderId* |Dossier |Sélectionner un dossier |

Le caractère * indique qu’une propriété est obligatoire.

### <a name="list-files-in-folder"></a>Répertorier les fichiers dans un dossier
Cette opération obtient la liste des fichiers et sous-dossiers d’un dossier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| id* |Dossier |Sélectionner un dossier |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
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

### <a name="list-files-in-root-folder"></a>Répertorier les fichiers dans le dossier racine
Cette opération obtient la liste des fichiers et sous-dossiers du dossier racine. 

Il n’existe aucun paramètre pour cet appel

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
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

### <a name="extract-archive-to-folder"></a>Extraire une archive dans un dossier
Cette opération extrait un fichier d’archive dans un dossier (exemple : .zip). 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| source* |Chemin d’accès du fichier d’archive source |Chemin du fichier d'archive |
| destination* |Chemin de destination du dossier |Chemin indiquant où extraire le contenu de l’archive |
| overwrite |Remplacer ? |Remplace les fichiers de destination si la valeur est « true » |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
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


