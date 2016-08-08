<properties
    pageTitle="Ajouter le connecteur Azure Blob Storage à vos applications logiques | Microsoft Azure"
    description="Vue d’ensemble du connecteur Azure Blob Storage avec les paramètres de l’API REST"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="07/25/2016"
   ms.author="mandia"/>

# Prise en main du connecteur Azure Blob Storage
Le stockage d’objets blob Azure est un service permettant de stocker de grandes quantités de données non structurées. Effectuez diverses actions, comme charger, mettre à jour, obtenir et supprimer des objets blob dans Azure Blob Storage.

Avec Azure Blob Storage, vous pouvez effectuer les opérations suivantes :

- Créez votre flux de travail en téléchargeant les nouveaux projets ou en extrayant les fichiers récemment mis à jour.
- Utilisez des actions pour obtenir les métadonnées d’un fichier, supprimer un fichier, copier des fichiers, etc. Par exemple, lorsqu’un outil est mis à jour dans un site web Azure (déclencheur), mettre à jour un fichier dans le stockage d’objets blob (action).

Cette rubrique décrit comment utiliser le connecteur Blob Storage dans une application logique et répertorie également les actions.

>[AZURE.NOTE] Cette version de l’article s’applique à la disponibilité générale des applications logiques.

Pour plus d’informations sur Logic Apps, voir [Qu’est-ce qu’une application logique ?](../app-service-logic/app-service-logic-what-are-logic-apps.md) et [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Connexion au stockage d’objets blob Azure

Pour que votre application logique puisse accéder à un service, vous devez commencer par créer une *connexion* à celui-ci. Une connexion permet d’assurer la connectivité entre une application logique et un autre service. Par exemple, pour vous connecter à un compte de stockage, commencez par créer une *connexion* Blob Storage. Pour créer une connexion, entrez les informations d’identification que vous utilisez généralement pour accéder au service auquel vous souhaitez vous connecter. Ainsi, dans le cas d’Azure Storage, entrez les informations d’identification de votre compte de stockage pour créer la connexion.

#### Créer la connexion

>[AZURE.INCLUDE [Créer une connexion à Azure Blob Storage](../../includes/connectors-create-api-azureblobstorage.md)]
 
## Utilisation d’un déclencheur

Ce connecteur ne possède aucun déclencheur. Utilisez d’autres déclencheurs pour démarrer l’application logique, notamment un déclencheur de périodicité, un déclencheur Webhook HTTP, des déclencheurs disponibles avec d’autres connecteurs, etc. La page [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md) vous fournit un exemple.

## Utilisation d’une action
	
Une action est une opération effectuée par le flux de travail défini dans une application logique.

1. Sélectionnez le signe plus. Vous disposez de plusieurs options : **Ajouter une action**, **Ajouter une condition** ou l’une des options **Plus**.

	![](./media/connectors-create-api-azureblobstorage/add-action.png)

2. Choisissez **Ajouter une action**.

3. Dans la zone de texte, saisissez « blob » pour obtenir la liste de toutes les actions disponibles.

	![](./media/connectors-create-api-azureblobstorage/actions.png)

4. Dans notre exemple, choisissez **AzureBlob - Obtenir les métadonnées d’un fichier à l’aide du chemin**. Si une connexion existe déjà, sélectionnez le bouton **...** (Afficher le sélecteur) pour sélectionner un fichier.

	![](./media/connectors-create-api-azureblobstorage/sample-file.png)

	Si vous êtes invité à saisir les informations de connexion, entrez les informations requises pour créer la connexion. La section [Créer la connexion](connectors-create-api-azureblobstorage.md#create-the-connection) figurant dans cette rubrique décrit ces propriétés.

	> [AZURE.NOTE] Dans cet exemple, nous obtiendrons les métadonnées d’un fichier. Pour consulter les métadonnées, ajoutez une autre action qui crée un nouveau fichier à l’aide d’un autre connecteur. Par exemple, ajoutez une action OneDrive qui crée un nouveau fichier « test » basé sur les métadonnées.

5. **Enregistrez** vos modifications (dans le coin supérieur gauche de la barre d’outils). Votre application logique est enregistrée et peut être activée automatiquement.

> [AZURE.TIP] [Storage Explorer](http://storageexplorer.com/) est un excellent outil pour gérer plusieurs comptes de stockage.

## Détails techniques

## Actions Blob Storage

|Action|Description|
|--- | ---|
|[Obtenir les métadonnées d’un fichier](connectors-create-api-azureblobstorage.md#get-file-metadata)|Cette opération récupère les métadonnées d’un fichier à l’aide de l’ID du fichier.|
|[Mettre à jour un fichier](connectors-create-api-azureblobstorage.md#update-file)|Cette opération met à jour un fichier.|
|[Supprimer un fichier](connectors-create-api-azureblobstorage.md#delete-file)|Cette opération supprime un fichier.|
|[Obtenir les métadonnées d’un fichier à l’aide du chemin](connectors-create-api-azureblobstorage.md#get-file-metadata-using-path)|Cette opération récupère les métadonnées du fichier à l’aide du chemin.|
|[Obtenir le contenu d’un fichier à l’aide du chemin](connectors-create-api-azureblobstorage.md#get-file-content-using-path)|Cette opération récupère le contenu d’un fichier à l’aide du chemin.|
|[Obtenir le contenu d’un fichier](connectors-create-api-azureblobstorage.md#get-file-content)|Cette opération récupère le contenu d’un fichier à l’aide de l’ID.|
|[Créer un fichier](connectors-create-api-azureblobstorage.md#create-file)|Cette opération télécharge un fichier.|
|[Copier un fichier](connectors-create-api-azureblobstorage.md#copy-file)|Cette opération copie un fichier dans Azure Blob Storage.|
|[Extraire une archive dans un dossier](connectors-create-api-azureblobstorage.md#extract-archive-to-folder)|Cette opération extrait un fichier d’archive dans un dossier (exemple : .zip).|

### Détails de l’action

Dans cette section, consultez les détails relatifs à chaque action, y compris toutes les propriétés d’entrée requises ou facultatives et toute sortie correspondante associée au connecteur.

#### Obtenir les métadonnées d’un fichier
Cette opération récupère les métadonnées d’un fichier à l’aide de l’ID du fichier.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|id*|Fichier|Sélectionner un fichier|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
|---|---|
|ID|string|
|Nom|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Taille|integer|
|MediaType|string|
|IsFolder|booléenne|
|ETag|string|
|FileLocator|string|


#### Mettre à jour un fichier
Cette opération met à jour un fichier.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|id*|Fichier|Sélectionner un fichier|
|body*|le contenu d’un fichier ;|Contenu du fichier à mettre à jour|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
|---|---|
|ID|string|
|Nom|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Taille|integer|
|MediaType|string|
|IsFolder|booléenne|
|ETag|string|
|FileLocator|string|


#### Supprimer un fichier
Cette opération supprime un fichier.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|id*|Fichier|Sélectionner un fichier|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
Aucun.


#### Obtenir les métadonnées d’un fichier à l’aide du chemin
Cette opération récupère les métadonnées du fichier à l’aide du chemin.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|path*|Chemin de fichier|Sélectionner un fichier|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
|---|---|
|ID|string|
|Nom|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Taille|integer|
|MediaType|string|
|IsFolder|booléenne|
|ETag|string|
|FileLocator|string|


#### Obtenir le contenu d’un fichier à l’aide du chemin
Cette opération récupère le contenu d’un fichier à l’aide du chemin.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|path*|Chemin de fichier|Sélectionner un fichier|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
Aucun.


#### Obtenir le contenu d’un fichier
Cette opération récupère le contenu d’un fichier à l’aide de l’ID.

|Nom de la propriété| Type de données|Description|
| ---|---|---|
|id*|string|Sélectionner un fichier|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
Aucun.


#### Créer un fichier
Cette opération télécharge un fichier.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|folderPath*|Chemin d’accès du dossier|Sélectionner un dossier|
|name*|Nom de fichier|Nom du fichier à télécharger|
|body*|le contenu d’un fichier ;|Contenu du fichier à charger|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données | 
|---|---|
|ID|string|
|Nom|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Taille|integer|
|MediaType|string|
|IsFolder|booléenne|
|ETag|string|
|FileLocator|string|


#### Copier un fichier
Cette opération copie un fichier dans Azure Blob Storage.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|source*|URL de la source|Spécifier l’URL du fichier source|
|destination*|Chemin de destination du fichier|Spécifier le chemin de destination du fichier, y compris le nom du fichier cible|
|overwrite|Remplacer ?|Un fichier de destination existant doit-il être remplacé (True/False) ? |

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
|---|---|
|ID|string|
|Nom|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Taille|integer|
|MediaType|string|
|IsFolder|booléenne|
|ETag|string|
|FileLocator|string|

#### Extraire une archive dans un dossier
Cette opération extrait un fichier d’archive dans un dossier (exemple : .zip).

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|source*|Chemin d’accès du fichier d’archive source|Sélectionner un fichier d’archive|
|destination*|Chemin de destination du dossier|Sélectionner le contenu à extraire|
|overwrite|Remplacer ?|Un fichier de destination existant doit-il être remplacé (True/False) ?|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
|---|---|
|ID|string|
|Nom|string|
|DisplayName|string|
|Path|string|
|LastModified|string|
|Taille|integer|
|MediaType|string|
|IsFolder|booléenne|
|ETag|string|
|FileLocator|string|


## Réponses HTTP

Lorsque vous exécutez des appels de diverses actions, vous pouvez obtenir certaines réponses. Le tableau suivant présente les réponses et leurs descriptions :

|Nom|Description|
|---|---|
|200|OK|
|202|Acceptée|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s'est produite|
|default|L’opération a échoué.|

## Étapes suivantes

[Créez une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md). Explorez les autres connecteurs disponibles dans Logic Apps en consultant notre [liste d’API](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->