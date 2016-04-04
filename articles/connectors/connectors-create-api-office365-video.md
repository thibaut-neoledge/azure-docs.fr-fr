<properties
pageTitle="Utiliser l'API Office 365 Video dans vos applications logiques | Microsoft Azure"
description="Prendre en main l’utilisation de l’API Office 365 Video (connecteur) dans vos applications logiques Microsoft Azure App Service"
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
ms.date="02/25/2016"
ms.author="deonhe"/>

# Prendre en main l'API Office 365 Video
Connexion à Office 365 Video pour obtenir des informations sur une vidéo Office 365, la liste des vidéos, et bien plus encore. L’API Office 365 Video peut être utilisée à partir de :

- Logic Apps 

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques. Cette API n’est pas pris en charge sur les versions de schéma précédentes.

Avec Office 365 Video, vous pouvez :

- Créer votre flux d’activité en fonction des données que vous obtenez d’Office 365 Video. 
- Utiliser des actions pour vérifier l’état du portail vidéo, obtenir une liste de toutes les vidéos dans un canal, et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d'autres actions. Vous pouvez, par exemple, utiliser l’API Bing Search pour rechercher des vidéos Office 365, puis utiliser l’API Office 365 Video pour obtenir des informations relatives à cette vidéo. Si la vidéo répond à vos besoins, vous pouvez la publier sur Facebook. 

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions

L’API Office 365 Users met à votre disposition les actions ci-après. Il n'y a aucun déclencheur.

| Déclencheurs | Actions|
| --- | --- |
| Aucun | <ul><li>Vérifier l’état du portail vidéo</li><li>Obtenir tous les canaux visibles</li><li>Obtenir l’URL de la lecture du manifeste Azure Media Services pour une vidéo</li><li>Obtenir le jeton du porteur pour accéder au déchiffrage de la vidéo</li><li>Obtient des informations sur une vidéo Office 365 particulière</li><li>Répertorie toutes les vidéos Office 365 présentes dans un canal</li></ul>

Toutes les API prennent en charge les données aux formats JSON et XML.

## Créer une connexion à l’API Office 365 Video
Quand vous ajoutez cette API à vos applications logiques, vous devez vous connecter à votre compte Office 365 Video et autoriser les applications logiques à se connecter à votre compte.

1. Connectez-vous à votre compte Office 365 Video.
2. Autorisez vos applications logiques à se connecter à votre compte Office 365 et à l’utiliser. 

Après avoir créé la connexion, vous entrez les propriétés Office 365 Video, comme le nom du client ou l’ID du canal. La section **Informations de référence sur l'API REST** dans cette rubrique décrit ces propriétés.

>[AZURE.TIP] Vous pouvez utiliser cette même connexion Office 365 Video dans d’autres applications logiques.

## Informations de référence sur l'API REST Swagger
S’applique à la version 1.0.

### Vérifie l’état du portail vidéo 
Vérifie l’état du portail vidéo pour voir si les services vidéo sont activés. ```GET: /{tenant}/IsEnabled```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|locataire|string|yes|path|(aucun)|Le nom du client pour le répertoire dont fait partie l'utilisateur|


#### Response

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|404|Introuvable|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|



### Obtenir tous les canaux visibles 
Permet d’obtenir tous les canaux que l’utilisateur est autorisé à afficher. ```GET: /{tenant}/Channels```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|locataire|string|yes|path|(aucun)|Le nom du client pour le répertoire dont fait partie l'utilisateur|


#### Response

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|404|Introuvable|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|




### Répertorie toutes les vidéos Office 365 présentes dans un canal 
Répertorie toutes les vidéos Office 365 présentes dans un canal. ```GET: /{tenant}/Channels/{channelId}/Videos```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|locataire|string|yes|path|(aucun)|Le nom du client pour le répertoire dont fait partie l'utilisateur|
|channelId|string|yes|path|(aucun)|L'ID du canal où les vidéos doivent être récupérées|


#### Response

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|404|Introuvable|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|




### Obtient des informations sur une vidéo Office 365 particulière 
Obtient des informations sur une vidéo Office 365 particulière. ```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|locataire|string|yes|path|(aucun)|Le nom du client pour le répertoire dont fait partie l'utilisateur|
|channelId|string|yes|path|(aucun)|L’ID du canal|
|videoId|string|yes|path|(aucun)|L'ID de la vidéo|


#### Response

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|404|Introuvable|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|




### Obtenir l'URL de la lecture du manifeste Azure Media Services pour une vidéo 
Obtenir l’URL de la lecture du manifeste Azure Media Services pour une vidéo. ```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|locataire|string|yes|path|(aucun)|Le nom du client pour le répertoire dont fait partie l'utilisateur|
|channelId|string|yes|path|(aucun)|L’ID du canal|
|videoId|string|yes|path|(aucun)|L'ID de la vidéo|
|streamingFormatType|string|yes|query|(aucun)|Type de format de la diffusion en continu. 1 - Smooth Streaming ou MPEG-DASH. 0 - HLS Streaming|


#### Response

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|404|Introuvable|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|




### Obtenir le jeton du porteur pour accéder au déchiffrage de la vidéo 
Obtenir le jeton du porteur pour accéder au déchiffrage de la vidéo. ```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|locataire|string|yes|path|(aucun)|Le nom du client pour le répertoire dont fait partie l'utilisateur|
|channelId|string|yes|path|(aucun)|L’ID du canal|
|videoId|string|yes|path|(aucun)|L'ID de la vidéo|


#### Response

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|404|Introuvable|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


## Définitions d'objet

#### Channel : classe de canal

| Nom | Type de données | Requis|
|---|---|---|
|ID|string|no|
|Intitulé|string|no|
|Description|string|no|


#### Vidéo 

| Nom | Type de données |Requis|
|---|---|---|
|ID|string|no|
|Intitulé|string|no|
|Description|string|no|
|CreationDate|string|no|
|Propriétaire|string|no|
|ThumbnailUrl|string|no|
|VideoUrl|string|no|
|VideoDuration|integer|no|
|VideoProcessingStatus|integer|no|
|ViewCount|integer|no|


## Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0323_2016-->