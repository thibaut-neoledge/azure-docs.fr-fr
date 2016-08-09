<properties
	pageTitle="Ajout de l'action HTTP + Swagger dans des applications logiques | Microsoft Azure"
	description="Vue d’ensemble de l’action et des opérations HTTP + Swagger"
	services=""
	documentationCenter="" 
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# Prise en main de l’action HTTP + Swagger

Avec l’action HTTP + Swagger, vous pouvez créer un connecteur de première classe pour n’importe quel point de terminaison REST avec un [document Swagger](https://swagger.io).

- Étendre une application logique pour appeler n’importe quel point de terminaison REST avec une expérience de conception de première classe

Pour commencer à utiliser l’action HTTP + Swagger dans une application logique, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Utiliser HTTP + Swagger comme un déclencheur ou d’une action

Le déclencheur et l’action HTTP + Swagger fonctionnement comme une [action HTTP](connectors-native-http.md) mais en fournissant une meilleure expérience de conception en affichant la forme de l’API et émet dans l’outil de création à partir des [métadonnées swagger](https://swagger.io). En outre, vous pouvez utiliser HTTP + Swagger comme déclencheur, et si vous souhaitez implémenter un déclencheur d’interrogation, vous devez suivre le modèle d’interrogation [décrit dans ce document](../app-service-logic/app-service-logic-create-api-app.md#polling-triggers).
	
[En savoir plus sur les déclencheurs et actions de l’application logique.](connectors-overview.md)

Voici un exemple d’utilisation de l’opération HTTP + Swagger en tant qu’action dans un flux de travail.

1. Sélectionnez le bouton **Nouvelle étape**
1. Choisissez **Ajouter une action**
1. Dans la zone de recherche Action , tapez « Swagger » pour répertorier l’action HTTP + Swagger

	![Sélectionnez une action HTTP + Swagger](./media/connectors-native-http-swagger/using-action-1.png)

1. Entrez l’URL pour un document swagger
	- L’URL doit être un point de terminaison HTTPS et avoir CORS activé pour fonctionner à partir du concepteur. Si le document swagger ne répond pas à ces critères, vous pouvez utiliser [Azure Storage avec CORS activé](#hosting-swagger-from-storage) pour stocker le document.
1. Cliquez sur Suivant pour lire et effectuer le rendu du document swagger
1. Ajoutez tout paramètre nécessaire à l’appel HTTP

	![Exécuter l’action HTTP](./media/connectors-native-http-swagger/using-action-2.png)

1. Cliquez sur Enregistrer dans le coin supérieur gauche de la barre d’outils, et votre application logique est à la fois enregistrée et publiée (activation)

### Hébergement de Swagger à partir de Storage

Vous pouvez souhaiter faire référence à un document de swagger qui n’est pas hébergé ou ne répond pas aux conditions de configuration de sécurité et de cross-origin requises dans le concepteur. Pour contourner ce problème, vous pouvez stocker le document swagger dans Azure Storage et activer CORS pour référencer le document. Voici les étapes pour créer, configurer et stocker un document swagger dans Azure Storage :

1. [Créer un compte Azure Storage avec stockage d’objets blob](../storage/storage-create-storage-account.md) --Définir des autorisations pour l’accès « Public »
1. Activer CORS sur l’objet blob
	- Vous pouvez utiliser [ce script PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1) pour configurer ce paramètre automatiquement.
1. Téléchargez le fichier swagger dans l’objet blob Azure
	- Vous pouvez le faire via le [portail Azure](https://portal.azure.com) ou à partir d’un outil tel que [Azure Storage Explorer](http://storageexplorer.com/)
1. Référencez un lien HTTPS vers le document dans l’objet blob Azure (au format `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`)

---

## Détails techniques

Vous trouverez ci-dessous les détails des déclencheurs et des actions de que ce connecteur prend en charge.

## Déclencheurs HTTP + Swagger

Un déclencheur est un événement qui peut être utilisé pour lancer le workflow défini dans une application logique. [En savoir plus sur les déclencheurs.](connectors-overview.md) Le connecteur HTTP + Swagger a 1 déclencheur.

|Déclencheur|Description|
|---|---|
|HTTP + Swagger|Exécuter un appel HTTP et obtenir le contenu de la réponse|

## Actions HTTP + Swagger

Une action est une opération effectuée par le workflow défini dans une application logique. [En savoir plus sur les actions.](connectors-overview.md) Le connecteur HTTP + Swagger a 1 action possible.

|Action|Description|
|---|---|
|HTTP + Swagger|Exécuter un appel HTTP et obtenir le contenu de la réponse|

### Détails de l’action

Le connecteur HTTP + Swagger est créé avec 1 action possible. Vous trouverez ci-dessous plus d’informations sur chacune des actions, leurs champs obligatoires et facultatifs, et les détails des résultats correspondants associés à leur utilisation.

#### HTTP + Swagger

Faites une demande HTTP sortante avec assistance des métadonnées swagger. Une * signifie que le champ est obligatoire.

|Display Name|Nom de la propriété|Description|
|---|---|---|
|Method (Méthode)*|statique|Verbe HTTP à utiliser|
|URI*|URI|URI de la requête HTTP|
|En-têtes|headers|Un objet JSON d’en-têtes HTTP à inclure|
|Corps|body|Le texte de la requête HTTP|
|Authentification|authentication|Authentification à utiliser pour la demande - [Pour plus d’informations, consultez HTTP](./connectors-native-http.md#authentication)|
<br>

**Détails des résultats**

Réponse HTTP

|Nom de la propriété|Type de données|Description|
|---|---|---|
|En-têtes|objet|En-têtes de réponse|
|Corps|objet|Objet Réponse|
|Code d’état|int|Code d'état HTTP|

### Réponses HTTP

Lorsque vous exécutez des appels de diverses actions, vous pouvez obtenir certaines réponses. La table ci-dessous indique les réponses correspondantes et leurs descriptions.

|Nom|Description|
|---|---|
|200|OK|
|202|Acceptée|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s'est produite|

---

## Étapes suivantes

Vous trouverez ci-dessous plus d’informations sur la manière d’avancer le développement de vos applications logiques, ainsi que sur notre communauté.

## Créer une application logique

Essayez la plateforme et [créez une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md) dès maintenant. Vous pouvez explorer les autres connecteurs disponibles dans les applications logiques en examinant notre [liste d’API](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->