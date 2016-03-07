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
ms.date="02/18/2016"
ms.author="deonhe"/>

# Prendre en main l'API Office 365 Video

L'API Office 365 Video fournit une API compatible avec les vidéos et canaux d'Office 365.

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques. Pour la version de schéma 2014-12-01-preview, cliquez sur [Office365 Video Connector](../app-service-logic/app-service-logic-connector-Office365 Video Connector.md).

Avec Office 365 Video, vous pouvez :

* Créer des applications logiques

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## À propos des déclencheurs et des actions

L’API Office 365 Video peut être utilisée en tant qu'action ; il n'y a aucun déclencheur. Toutes les API prennent en charge les données aux formats JSON et XML.

 L'API Office 365 Video met à votre disposition les actions et/ou les déclencheurs ci-après.

### Actions de l’API Office 365 Video
Vous pouvez effectuer les actions suivantes :

|Action|Description|
|--- | ---|
|IsVideoPortalEnabled|Vérifie l'état du portail vidéo pour voir si des services vidéo sont activés.|
|ListViewableChannels|Obtient tous les canaux que l'utilisateur peut afficher|
|ListVideos|Répertorie toutes les vidéos Office 365 présentes dans un canal|
|GetVideo|Obtient des informations sur une vidéo Office 365 particulière|
|GetPlaybackUrl|Obtenir l'URL de la lecture du manifeste Azure Media Services pour une vidéo|
|GetStreamingKeyAccessToken|Obtenir le jeton du porteur pour accéder au déchiffrage de la vidéo|
ActionsTableReplaceMeLater## Créer une connexion à l'API Office 365 Video Pour utiliser l'API Office 365 Video, vous créez d'abord une **connexion** puis fournissez les détails de ces propriétés :

|Propriété| Requis|Description|
| ---|---|---|
|Jeton|Oui|Fournir les informations d'identification SharePoint Online|


>[AZURE.TIP] Vous pouvez utiliser cette connexion dans d'autres applications logiques.

## Informations de référence sur l’API REST Office 365 Video
#### Cette documentation concerne la version 1.0.


### Vérifie l'état du portail vidéo pour voir si des services vidéo sont activés.
**```GET: /{tenant}/IsEnabled```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|locataire|string|yes|path|(aucun)|Le nom du client pour le répertoire dont fait partie l'utilisateur|


### Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|404|Introuvable|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|
------



### Obtient tous les canaux que l'utilisateur peut afficher
**```GET: /{tenant}/Channels```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|locataire|string|yes|path|(aucun)|Le nom du client pour le répertoire dont fait partie l'utilisateur|


### Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|404|Introuvable|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|
------



### Répertorie toutes les vidéos Office 365 présentes dans un canal
**```GET: /{tenant}/Channels/{channelId}/Videos```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|locataire|string|yes|path|(aucun)|Le nom du client pour le répertoire dont fait partie l'utilisateur|
|channelId|string|yes|path|(aucun)|L'ID du canal où les vidéos doivent être récupérées|


### Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|404|Introuvable|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|
------



### Obtient des informations sur une vidéo Office 365 particulière
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|locataire|string|yes|path|(aucun)|Le nom du client pour le répertoire dont fait partie l'utilisateur|
|channelId|string|yes|path|(aucun)|L’ID du canal|
|videoId|string|yes|path|(aucun)|L'ID de la vidéo|


### Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|404|Introuvable|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|
------



### Obtenir l'URL de la lecture du manifeste Azure Media Services pour une vidéo
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|locataire|string|yes|path|(aucun)|Le nom du client pour le répertoire dont fait partie l'utilisateur|
|channelId|string|yes|path|(aucun)|L’ID du canal|
|videoId|string|yes|path|(aucun)|L'ID de la vidéo|
|streamingFormatType|string|yes|query|(aucun)|Type de format de la diffusion en continu. 1 - Smooth Streaming ou MPEG-DASH. 0 - HLS Streaming|


### Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|404|Introuvable|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|
------



### Obtenir le jeton du porteur pour accéder au déchiffrage de la vidéo
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|locataire|string|yes|path|(aucun)|Le nom du client pour le répertoire dont fait partie l'utilisateur|
|channelId|string|yes|path|(aucun)|L’ID du canal|
|videoId|string|yes|path|(aucun)|L'ID de la vidéo|


### Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|404|Introuvable|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|
------



## Définition(s) d'objet : 

 **Channel** : classe de canal

Propriétés requises pour Channel :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|ID|string|
|Intitulé|string|
|Description|string|



 **Video** : classe de vidéo

Propriétés requises pour Video :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|ID|string|
|Intitulé|string|
|Description|string|
|CreationDate|string|
|Propriétaire|string|
|ThumbnailUrl|string|
|VideoUrl|string|
|VideoDuration|integer|
|VideoProcessingStatus|integer|
|ViewCount|integer|


## Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->