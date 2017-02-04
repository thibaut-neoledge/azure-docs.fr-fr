---
title: "Ajouter le connecteur Stockage Blob Azure à vos applications logiques | Microsoft Docs"
description: "Vue d’ensemble du connecteur Azure Blob Storage avec les paramètres de l’API REST"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b5dc3f75-6bea-420b-b250-183668d2848d
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 6b7c73576e09af3d1b3c886efa88044846e91494


---
# <a name="get-started-with-the-azure-blob-storage-connector"></a>Prise en main du connecteur Azure Blob Storage
Le stockage d’objets blob Azure est un service permettant de stocker de grandes quantités de données non structurées. Effectuez diverses actions, comme charger, mettre à jour, obtenir et supprimer des objets blob dans Azure Blob Storage. 

Avec Azure Blob Storage, vous pouvez effectuer les opérations suivantes :

* Créez votre flux de travail en téléchargeant les nouveaux projets ou en extrayant les fichiers récemment mis à jour.
* Utilisez des actions pour obtenir les métadonnées d’un fichier, supprimer un fichier, copier des fichiers, etc. Par exemple, lorsqu’un outil est mis à jour dans un site web Azure (déclencheur), mettre à jour un fichier dans le stockage d’objets blob (action). 

Cette rubrique décrit comment utiliser le connecteur de stockage d’objet blob dans une application logique, et répertorie les actions.

> [!NOTE]
> Cette version de l’article s’applique à la disponibilité générale des applications logiques. 
> 
> 

Pour plus d’informations sur Logic Apps, voir [Qu’est-ce qu’une application logique ?](../logic-apps/logic-apps-what-are-logic-apps.md) et [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-azure-blob-storage"></a>Connexion au stockage d’objets blob Azure
Pour que votre application logique puisse accéder à un service, vous devez d’abord créer une *connexion* à celui-ci. Une connexion permet d’assurer la connectivité entre une application logique et un autre service. Par exemple, pour vous connecter à un compte de stockage, commencez par créer une *connexion* de stockage des objets blob. Pour créer une connexion, entrez les informations d’identification que vous utilisez généralement pour accéder au service auquel vous souhaitez vous connecter. Ainsi, dans le cas d’Azure Storage, entrez les informations d’identification de votre compte de stockage pour créer la connexion. 

#### <a name="create-the-connection"></a>Créer la connexion
> [!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]
> 
> 

## <a name="use-a-trigger"></a>Utilisation d’un déclencheur
Ce connecteur ne possède aucun déclencheur. Utilisez d’autres déclencheurs pour démarrer l’application logique, notamment un déclencheur de périodicité, un déclencheur Webhook HTTP, des déclencheurs disponibles avec d’autres connecteurs, etc. [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md) vous fournit un exemple.

## <a name="use-an-action"></a>Utilisation d’une action
Une action est une opération effectuée par le flux de travail défini dans une application logique.

1. Sélectionnez le signe plus. Vous disposez de plusieurs options : **Ajouter une action**, **Ajouter une condition** ou l’une des options **Plus**.
   
    ![](./media/connectors-create-api-azureblobstorage/add-action.png)
2. Choisissez **Ajouter une action**.
3. Dans la zone de texte, saisissez « blob » pour obtenir la liste de toutes les actions disponibles.
   
    ![](./media/connectors-create-api-azureblobstorage/actions.png) 
4. Dans notre exemple, choisissez **AzureBlob - Obtenir les métadonnées d’un fichier à l’aide du chemin**. Si une connexion existe déjà, sélectionnez le bouton **...** (Afficher le sélecteur) pour sélectionner un fichier.
   
    ![](./media/connectors-create-api-azureblobstorage/sample-file.png)
   
    Si vous êtes invité à saisir les informations de connexion, entrez les informations requises pour créer la connexion. La section [Créer la connexion](connectors-create-api-azureblobstorage.md#create-the-connection) dans cette rubrique décrit ces propriétés. 
   
   > [!NOTE]
   > Dans cet exemple, nous obtiendrons les métadonnées d’un fichier. Pour consulter les métadonnées, ajoutez une autre action qui crée un nouveau fichier à l’aide d’un autre connecteur. Par exemple, ajoutez une action OneDrive qui crée un nouveau fichier « test » basé sur les métadonnées. 
   > 
   > 
5. **Enregistrez** vos modifications (dans le coin supérieur gauche de la barre d’outils). Votre application logique est enregistrée et peut être activée automatiquement.

> [!TIP]
> L’[Explorateur de stockage](http://storageexplorer.com/) est un excellent outil pour gérer plusieurs comptes de stockage.
> 
> 

## <a name="technical-details"></a>Détails techniques
## <a name="storage-blob-actions"></a>Actions Blob Storage
| Action | Description |
| --- | --- |
| [Obtenir les métadonnées d’un fichier](connectors-create-api-azureblobstorage.md#get-file-metadata) |Cette opération récupère les métadonnées d’un fichier à l’aide de l’ID du fichier. |
| [Mettre à jour un fichier](connectors-create-api-azureblobstorage.md#update-file) |Cette opération met à jour un fichier. |
| [Supprimer un fichier](connectors-create-api-azureblobstorage.md#delete-file) |Cette opération supprime un fichier. |
| [Obtenir les métadonnées d’un fichier à l’aide du chemin](connectors-create-api-azureblobstorage.md#get-file-metadata-using-path) |Cette opération récupère les métadonnées du fichier à l’aide du chemin. |
| [Obtenir le contenu d’un fichier à l’aide du chemin](connectors-create-api-azureblobstorage.md#get-file-content-using-path) |Cette opération récupère le contenu d’un fichier à l’aide du chemin. |
| [Obtenir le contenu d’un fichier](connectors-create-api-azureblobstorage.md#get-file-content) |Cette opération récupère le contenu d’un fichier à l’aide de l’ID. |
| [Créer un fichier](connectors-create-api-azureblobstorage.md#create-file) |Cette opération télécharge un fichier. |
| [Copier un fichier](connectors-create-api-azureblobstorage.md#copy-file) |Cette opération copie un fichier dans Azure Blob Storage. |
| [Extraire une archive dans un dossier](connectors-create-api-azureblobstorage.md#extract-archive-to-folder) |Cette opération extrait un fichier d’archive dans un dossier (exemple : .zip). |

### <a name="action-details"></a>Détails de l’action
Dans cette section, consultez les détails relatifs à chaque action, y compris toutes les propriétés d’entrée requises ou facultatives et toute sortie correspondante associée au connecteur.

#### <a name="get-file-metadata"></a>Obtenir les métadonnées d’un fichier
Cette opération récupère les métadonnées d’un fichier à l’aide de l’ID du fichier.  

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| id* |Fichier |Sélectionner un fichier |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
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

#### <a name="update-file"></a>Mettre à jour un fichier
Cette opération met à jour un fichier.  

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| id* |Fichier |Sélectionner un fichier |
| body* |le contenu d’un fichier ; |Contenu du fichier à mettre à jour |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
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

#### <a name="delete-file"></a>Supprimer un fichier
Cette opération supprime un fichier.  

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| id* |Fichier |Sélectionner un fichier |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
Aucune.

#### <a name="get-file-metadata-using-path"></a>Obtenir les métadonnées d’un fichier à l’aide du chemin
Cette opération récupère les métadonnées du fichier à l’aide du chemin.  

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| path* |Chemin de fichier |Sélectionner un fichier |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
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

#### <a name="get-file-content-using-path"></a>Obtenir le contenu d’un fichier à l’aide du chemin
Cette opération récupère le contenu d’un fichier à l’aide du chemin.  

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| path* |Chemin de fichier |Sélectionner un fichier |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
Aucune.

#### <a name="get-file-content"></a>Obtenir le contenu d’un fichier
Cette opération récupère le contenu d’un fichier à l’aide de l’ID.  

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| id* |string |Sélectionner un fichier |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
Aucune.

#### <a name="create-file"></a>Créer un fichier
Cette opération télécharge un fichier.  

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| folderPath* |Chemin d’accès du dossier |Sélectionner un dossier |
| name* |Nom de fichier |Nom du fichier à télécharger |
| body* |le contenu d’un fichier ; |Contenu du fichier à charger |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
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

#### <a name="copy-file"></a>Copier un fichier
Cette opération copie un fichier dans Azure Blob Storage.  

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| source* |URL de la source |Spécifier l’URL du fichier source |
| destination* |Chemin de destination du fichier |Spécifier le chemin de destination du fichier, y compris le nom du fichier cible |
| overwrite |Remplacer ? |Un fichier de destination existant doit-il être remplacé (True/False) ? |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
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

#### <a name="extract-archive-to-folder"></a>Extraire une archive dans un dossier
Cette opération extrait un fichier d’archive dans un dossier (exemple : .zip).  

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| source* |Chemin d’accès du fichier d’archive source |Sélectionner un fichier d’archive |
| destination* |Chemin de destination du dossier |Sélectionner le contenu à extraire |
| overwrite |Remplacer ? |Un fichier de destination existant doit-il être remplacé (True/False) ? |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
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
Lorsque vous exécutez des appels de diverses actions, vous pouvez obtenir certaines réponses. Le tableau suivant présente les réponses et leurs descriptions :  

| Nom | Description |
| --- | --- |
| 200 |OK |
| 202 |Acceptée |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s'est produite |
| default |L’opération a échoué. |

## <a name="next-steps"></a>Étapes suivantes
[Créez une application logique](../logic-apps/logic-apps-create-a-logic-app.md). Explorez les autres connecteurs disponibles dans les applications logiques en consultant notre [liste d’API](apis-list.md).




<!--HONumber=Jan17_HO3-->


