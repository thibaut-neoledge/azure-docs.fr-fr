---
title: "Découvrez comment utiliser le connecteur SFTP dans vos applications logiques | Microsoft Docs"
description: "Créez des applications logiques avec Azure App Service. Connectez-vous à l’API SFTP pour envoyer et recevoir des fichiers. Vous pouvez effectuer diverses opérations, telles que créer, mettre à jour, obtenir ou supprimer des fichiers."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/20/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 2977404fb408ea5301c88caa7ce6767a906ca9c7


---
# <a name="get-started-with-the-sftp-connector"></a>Prise en main du connecteur SFTP
Utilisez le connecteur SFTP pour accéder à un compte SFTP afin d’envoyer et de recevoir des fichiers. Vous pouvez effectuer diverses opérations, telles que créer, mettre à jour, obtenir ou supprimer des fichiers.  

Pour utiliser [n’importe quel connecteur](apis-list.md), vous devez commencer par créer une application logique. Vous pouvez démarrer maintenant en [créant une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-sftp"></a>Se connecter à SFTP
Pour que votre application logique puisse accéder à un service, vous devez commencer par créer une *connexion* à celui-ci. Une [connexion](connectors-overview.md) permet d’assurer la connectivité entre une application logique et un autre service.  

### <a name="create-a-connection-to-sftp"></a>Créer une connexion à SFTP
> [!INCLUDE [Steps to create a connection to SFTP](../../includes/connectors-create-api-sftp.md)]
> 
> 

## <a name="use-an-sftp-trigger"></a>Utiliser un déclencheur SFTP
Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. [En savoir plus sur les déclencheurs](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

Dans cet exemple, nous allons vous indiquer comment utiliser le déclencheur **SFTP - Lors de l’ajout ou de la modification d’un fichier** pour initialiser un workflow d’application logique lorsqu’un fichier est ajouté à un serveur SFTP ou modifié sur ce dernier. Cet exemple vous apprendra également à ajouter une condition qui vérifie le contenu du fichier nouveau ou modifié, puis à prendre une décision pour extraire le fichier si son contenu indique qu’il doit être extrait avant d’être utilisé. Enfin, vous apprendrez à ajouter une action pour extraire le contenu d’un fichier et placer le contenu extrait dans un dossier du serveur SFTP. 

Dans un contexte d’entreprise, vous pourriez utiliser ce déclencheur pour surveiller l’apparition dans un dossier SFTP de nouveaux fichiers représentant des commandes émanant de clients.  Vous pourriez ensuite utiliser une action de connecteur SFTP telle que **Obtenir le contenu d’un fichier** pour récupérer le contenu de la commande à des fins de traitement ultérieur et de stockage dans votre base de données de commandes.

> [!INCLUDE [Steps to create an SFTP trigger](../../includes/connectors-create-api-sftp-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Ajouter une condition
> [!INCLUDE [Steps to add a condition](../../includes/connectors-create-api-sftp-condition.md)]
> 
> 

## <a name="use-an-sftp-action"></a>Utiliser une action SFTP
Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!INCLUDE [Steps to create an SFTP action](../../includes/connectors-create-api-sftp-action.md)]
> 
> 

## <a name="technical-details"></a>Détails techniques
Voici les détails des déclencheurs, actions et réponses pris en charge par cette connexion :

## <a name="sftp-triggers"></a>Déclencheurs SFTP
SFTP comporte le déclencheur suivant :  

| Déclencheur | Description |
| --- | --- |
| [Lors de l’ajout ou de la modification d’un fichier](connectors-create-api-sftp.md#when-a-file-is-added-or-modified) |Cette opération déclenche un flux lorsqu’un fichier est ajouté ou modifié dans un dossier. |

## <a name="sftp-actions"></a>Actions SFTP
SFTP comporte les actions suivantes :

| Action | Description |
| --- | --- |
| [Obtenir les métadonnées d’un fichier](connectors-create-api-sftp.md#get-file-metadata) |Cette opération récupère les métadonnées d’un fichier à l’aide de l’identificateur du fichier. |
| [Mettre à jour un fichier](connectors-create-api-sftp.md#update-file) |Cette opération met à jour le contenu d’un fichier. |
| [Supprimer un fichier](connectors-create-api-sftp.md#delete-file) |Cette opération supprime un fichier. |
| [Obtenir les métadonnées d’un fichier à l’aide du chemin](connectors-create-api-sftp.md#get-file-metadata-using-path) |Cette opération récupère les métadonnées d’un fichier à l’aide du chemin d’accès au fichier. |
| [Obtenir le contenu d’un fichier à l’aide du chemin](connectors-create-api-sftp.md#get-file-content-using-path) |Cette opération récupère le contenu d’un fichier à l’aide du chemin d’accès au fichier. |
| [Obtenir le contenu d’un fichier](connectors-create-api-sftp.md#get-file-content) |Cette opération récupère le contenu d’un fichier à l’aide de l’identificateur du fichier. |
| [Créer un fichier](connectors-create-api-sftp.md#create-file) |Cette opération charge un fichier sur un serveur SFTP. |
| [Copier un fichier](connectors-create-api-sftp.md#copy-file) |Cette opération copie un fichier sur un serveur SFTP. |
| [Répertorier les fichiers dans un dossier](connectors-create-api-sftp.md#list-files-in-folder) |Cette opération récupère la liste des fichiers contenus dans un dossier. |
| [Répertorier les fichiers dans le dossier racine](connectors-create-api-sftp.md#list-files-in-root-folder) |Cette opération récupère la liste des fichiers du dossier racine. |
| [Extraire un dossier](connectors-create-api-sftp.md#extract-folder) |Cette opération extrait un fichier d’archive dans un dossier (exemple : .zip). |

### <a name="action-details"></a>Détails de l’action
Voici les détails des actions et des déclencheurs de ce connecteur, ainsi que leurs réponses :

### <a name="get-file-metadata"></a>Obtenir les métadonnées d’un fichier
Cette opération récupère les métadonnées d’un fichier à l’aide de l’identificateur du fichier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| id* |Fichier |Spécifier le fichier |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
| --- | --- | --- |
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
Cette opération met à jour le contenu d’un fichier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| id* |Fichier |Spécifier le fichier |
| body* |le contenu d’un fichier ; |Contenu du fichier à mettre à jour |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
| --- | --- | --- |
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
| id* |Fichier |Spécifier le fichier |

Le caractère * indique qu’une propriété est obligatoire.

### <a name="get-file-metadata-using-path"></a>Obtenir les métadonnées d’un fichier à l’aide du chemin
Cette opération récupère les métadonnées d’un fichier à l’aide du chemin d’accès au fichier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| path* |Chemin de fichier |Chemin d’accès unique au fichier |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
| --- | --- | --- |
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
Cette opération récupère le contenu d’un fichier à l’aide du chemin d’accès au fichier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| path* |Chemin de fichier |Chemin d’accès unique au fichier |

Le caractère * indique qu’une propriété est obligatoire.

### <a name="get-file-content"></a>Obtenir le contenu d’un fichier
Cette opération récupère le contenu d’un fichier à l’aide de l’identificateur du fichier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| id* |Fichier |Spécifier le fichier |

Le caractère * indique qu’une propriété est obligatoire.

### <a name="create-file"></a>Créer un fichier
Cette opération charge un fichier sur un serveur SFTP. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| folderPath* |Chemin d’accès du dossier |Chemin d’accès unique au dossier |
| name* |Nom de fichier |Nom du fichier |
| body* |le contenu d’un fichier ; |Contenu du fichier à créer |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

|  | Nom de la propriété | Type de données |
| --- | --- | --- |
| id |string | |
| Nom |string | |
| displayName |string | |
| path |string | |
| LastModified |string | |
| Taille |integer | |
| MediaType |string | |
| IsFolder |booléenne | |
| ETag |string | |
| FileLocator |string | |

### <a name="copy-file"></a>Copier un fichier
Cette opération copie un fichier sur un serveur SFTP. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| source* |Source file Path (Chemin d’accès au fichier source) |Chemin du fichier source |
| destination* |Chemin de destination du fichier |Chemin du fichier de destination, y compris le nom du fichier |
| overwrite |Remplacer ? |Remplace le fichier de destination si la valeur est « true » |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
| --- | --- | --- |
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

### <a name="when-a-file-is-added-or-modified"></a>Lors de l’ajout ou de la modification d’un fichier
Cette opération déclenche un flux lorsqu’un fichier est ajouté ou modifié dans un dossier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| folderId* |Dossier |Spécifier un dossier |

Le caractère * indique qu’une propriété est obligatoire.

### <a name="list-files-in-folder"></a>Répertorier les fichiers dans un dossier
Cette opération récupère la liste des fichiers contenus dans un dossier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| id* |Dossier |Spécifier le dossier |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
| --- | --- | --- |
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
Cette opération récupère la liste des fichiers du dossier racine. 

Il n’existe aucun paramètre pour cet appel

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
| --- | --- | --- |
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

### <a name="extract-folder"></a>Extraire un dossier
Cette opération extrait un fichier d’archive dans un dossier (exemple : .zip). 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| source* |Chemin d’accès du fichier d’archive source |Chemin du fichier d'archive |
| destination* |Chemin de destination du dossier |Chemin du dossier de destination |
| overwrite |Remplacer ? |Remplace les fichiers de destination si la valeur est « true » |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
| --- | --- | --- |
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




<!--HONumber=Feb17_HO1-->


