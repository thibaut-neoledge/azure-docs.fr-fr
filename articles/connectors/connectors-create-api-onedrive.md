<properties
	pageTitle="Ajouter le connecteur OneDrive à vos applications logiques | Microsoft Azure"
	description="Vue d’ensemble du connecteur OneDrive avec les paramètres de l’API REST"
	services="app-servicelogic"    
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
   ms.date="07/26/2016"
   ms.author="mandia"/>

# Prise en main du connecteur OneDrive

Connexion à OneDrive pour gérer vos fichiers, y compris le téléchargement de fichiers, la suppression de fichiers, et plus encore.

Avec OneDrive, vous pouvez effectuer les opérations suivantes :

- Créer votre flux de travail en stockant des fichiers dans OneDrive, ou mettre à jour des fichiers existants dans OneDrive.
- Utiliser des déclencheurs pour lancer votre flux de travail lorsqu’un fichier est créé ou mis à jour dans votre OneDrive.
- Utiliser des actions pour créer un fichier, supprimer un fichier et bien plus encore. Par exemple, lorsqu’un nouveau courrier électronique Office 365 est reçu avec une pièce jointe (déclencheur), créer un nouveau fichier dans OneDrive (action).

Cette rubrique décrit comment utiliser le connecteur OneDrive dans une application logique et répertorie également les déclencheurs et les actions.

>[AZURE.NOTE] Cette version de l’article s’applique à la disponibilité générale des applications logiques.

Pour plus d’informations sur Logic Apps, voir [Qu’est-ce qu’une application logique ?](../app-service-logic/app-service-logic-what-are-logic-apps.md) et [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Connexion à OneDrive

Pour que votre application logique puisse accéder à un service, vous devez d’abord créer une *connexion* à celui-ci. Une connexion permet d’assurer la connectivité entre une application logique et un autre service. Par exemple, pour vous connecter à OneDrive, vous devez préalablement disposer d’une *connexion* OneDrive. Pour créer une connexion, entrez les informations d’identification que vous utilisez généralement pour accéder au service auquel vous souhaitez vous connecter. Ensuite, dans OneDrive, entrez les informations d’identification à votre compte OneDrive pour créer la connexion.

### Créer la connexion

>[AZURE.INCLUDE [Procédure de création d’une connexion à OneDrive](../../includes/connectors-create-api-onedrive.md)]

## Utilisation d’un déclencheur

Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. Les déclencheurs « interrogent » le service à l’intervalle et à la fréquence de votre choix. [Apprenez-en davantage sur les déclencheurs](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. Dans l’application logique, saisissez « onedrive » pour obtenir la liste des déclencheurs :

	![](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Sélectionnez **Quand un fichier est modifié**. Si une connexion existe déjà, sélectionnez le bouton Afficher le sélecteur pour sélectionner un dossier.

	![](./media/connectors-create-api-onedrive/sample-folder.png)

	Si vous êtes invité à vous connecter, entrez les informations de connexion pour créer la connexion. La section [Créer la connexion](connectors-create-api-onedrive.md#create-the-connection) figurant dans cette rubrique répertorie les étapes.

	> [AZURE.NOTE] Dans cet exemple, l’application logique s’exécute lorsqu’un fichier est mis à jour dans le dossier que vous avez choisi. Pour consulter les résultats de ce déclencheur, ajoutez une autre action qui vous envoie un courrier électronique. Par exemple, ajoutez l’action Office 365 Outlook *Envoyer un courrier électronique* qui vous avertit par e-mail lorsqu’un fichier est mis à jour.

3. Sélectionnez le bouton **Modifier**, puis renseignez les champs **Fréquence** et **Intervalle**. Par exemple, si vous souhaitez que le déclencheur interroge le service toutes les 15 minutes, définissez le champ **Fréquence** sur **Minute**, et le champ **Intervalle** sur **15**.

	![](./media/connectors-create-api-onedrive/trigger-properties.png)

4. **Enregistrez** vos modifications (dans le coin supérieur gauche de la barre d’outils). Votre application logique est enregistrée et peut être activée automatiquement.


## Utilisation d’une action

Une action est une opération effectuée par le flux de travail défini dans une application logique. [Apprenez-en davantage sur les actions](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. Sélectionnez le signe plus. Vous disposez de plusieurs options : **Ajouter une action**, **Ajouter une condition** ou l’une des options **Plus**.

	![](./media/connectors-create-api-onedrive/add-action.png)

2. Choisissez **Ajouter une action**.

3. Dans la zone de texte, saisissez « onedrive » pour obtenir la liste de toutes les actions disponibles.

	![](./media/connectors-create-api-onedrive/onedrive-actions.png)

4. Dans notre exemple, choisissez **OneDrive - Créer un fichier**. Si une connexion existe déjà, sélectionnez le **chemin du dossier** où placer le fichier, entrez le **nom de fichier** et choisissez le **contenu du fichier** souhaité :

	![](./media/connectors-create-api-onedrive/sample-action.png)

	Si vous êtes invité à saisir les informations de connexion, entrez les informations requises pour créer la connexion. La section [Créer la connexion](connectors-create-api-onedrive.md#create-the-connection) figurant dans cette rubrique décrit ces propriétés.

	> [AZURE.NOTE] Dans cet exemple, nous allons créer un nouveau fichier dans un dossier OneDrive. Vous pouvez utiliser les résultats d’un autre déclencheur pour créer le fichier OneDrive. Par exemple, ajoutez le déclencheur Outlook Office 365 *Lorsqu’un nouveau courrier électronique arrive*. Puis ajoutez l’action OneDrive *Créer un fichier* qui utilise les champs Pièces jointes et Type de contenu d’une instruction ForEach pour créer le fichier dans OneDrive.
	> 
	> ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Enregistrez** vos modifications (dans le coin supérieur gauche de la barre d’outils). Votre application logique est enregistrée et peut être activée automatiquement.


## Détails techniques

## Déclencheurs

|Déclencheur | Description|
|--- | ---|
|[Quand un fichier est créé](connectors-create-api-onedrive.md#when-a-file-is-created)|Cette opération déclenche un flux lorsqu’un fichier est créé dans un dossier.|
|[Quand un fichier est modifié](connectors-create-api-onedrive.md#when-a-file-is-modified)|Cette opération déclenche un flux lorsqu’un fichier est modifié dans un dossier.|


## Actions

|Action|Description|
|--- | ---|
|[Obtenir les métadonnées d’un fichier](connectors-create-api-onedrive.md#get-file-metadata)|Cette opération obtient les métadonnées d’un fichier.|
|[Mettre à jour un fichier](connectors-create-api-onedrive.md#update-file)|Cette opération met à jour un fichier.|
|[Supprimer un fichier](connectors-create-api-onedrive.md#delete-file)|Cette opération supprime un fichier.|
|[Obtenir les métadonnées d’un fichier à l’aide du chemin](connectors-create-api-onedrive.md#get-file-metadata-using-path)|Cette opération obtient les métadonnées d’un fichier à l’aide du chemin.|
|[Obtenir le contenu d’un fichier à l’aide du chemin](connectors-create-api-onedrive.md#get-file-content-using-path)|Cette opération obtient le contenu d’un fichier à l’aide du chemin.|
|[Obtenir le contenu d’un fichier](connectors-create-api-onedrive.md#get-file-content)|Cette opération obtient le contenu d’un fichier.|
|[Créer un fichier](connectors-create-api-onedrive.md#create-file)|Cette opération crée un fichier.|
|[Copier un fichier](connectors-create-api-onedrive.md#copy-file)|Cette opération copie un fichier dans OneDrive.|
|[Répertorier les fichiers dans un dossier](connectors-create-api-onedrive.md#list-files-in-folder)|Cette opération obtient la liste des fichiers et sous-dossiers d’un dossier.|
|[Répertorier les fichiers dans le dossier racine](connectors-create-api-onedrive.md#list-files-in-root-folder)|Cette opération obtient la liste des fichiers et sous-dossiers du dossier racine.|
|[Extraire une archive dans un dossier](connectors-create-api-onedrive.md#extract-archive-to-folder)|Cette opération extrait un fichier d’archive dans un dossier (exemple : .zip).|

### Détails de l’action

Dans cette section, consultez les détails relatifs à chaque action, y compris toutes les propriétés d’entrée requises ou facultatives et toute sortie correspondante associée au connecteur.


#### Obtenir les métadonnées d’un fichier
Cette opération obtient les métadonnées d’un fichier.

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
|body*|le contenu d’un fichier ;|Contenu du fichier|

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
Cette opération obtient les métadonnées d’un fichier à l’aide du chemin.

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
Cette opération obtient le contenu d’un fichier à l’aide du chemin.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|path*|Chemin de fichier|Sélectionner un fichier|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
Aucun.


#### Obtenir le contenu d’un fichier
Cette opération obtient le contenu d’un fichier.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|id*|Fichier|Sélectionner un fichier|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
Aucun.

#### Créer un fichier
Cette opération crée un fichier.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|folderPath*|Chemin d’accès du dossier|Sélectionner un dossier|
|name*|Nom de fichier|Nom du fichier|
|body*|le contenu d’un fichier ;|Contenu du fichier|

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
Cette opération copie un fichier dans OneDrive.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|source*|URL de la source|URL du fichier source|
|destination*|Chemin de destination du fichier|Chemin de destination du fichier, y compris le nom de fichier cible|
|overwrite|Remplacer ?|Remplace le fichier de destination si la valeur est « true »|

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


#### Quand un fichier est créé
Cette opération déclenche un flux lorsqu’un fichier est créé dans un dossier.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|folderId*|Dossier|Sélectionner un dossier|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
Aucun.

#### Quand un fichier est modifié
Cette opération déclenche un flux lorsqu’un fichier est modifié dans un dossier.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|folderId*|Dossier|Sélectionner un dossier|

Un astérisque (*) signifie que la propriété est requise.

##### Détails des résultats
Aucun.

#### Répertorier les fichiers dans un dossier
Cette opération obtient la liste des fichiers et sous-dossiers d’un dossier.

|Nom de la propriété| Display Name|Description|
| ---|---|---|
|id*|Dossier|Sélectionner un dossier|

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
|MediaType|string||
|IsFolder|booléenne|
|ETag|string|
|FileLocator|string|


#### Répertorier les fichiers dans le dossier racine
Cette opération obtient la liste des fichiers et sous-dossiers du dossier racine.

Il n'existe aucun paramètre pour cet appel.


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
|source*|Chemin d’accès du fichier d’archive source|Chemin du fichier d'archive|
|destination*|Chemin de destination du dossier|Chemin indiquant où extraire le contenu de l’archive|
|overwrite|Remplacer ?|Remplace les fichiers de destination si la valeur est « true »|

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

Le tableau suivant présente les réponses aux actions et aux déclencheurs et leurs descriptions :

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