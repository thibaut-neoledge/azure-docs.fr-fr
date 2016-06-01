<properties
pageTitle="Ajouter l’API Yammer à vos applications logiques | Microsoft Azure"
description="Vue d’ensemble de l’API Yammer avec les paramètres de l’API REST"
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
ms.date="03/16/2016"
ms.author="deonhe"/>

# Prendre en main l’API Yammer

Connectez-vous à Yammer pour accéder aux conversations dans votre réseau d’entreprise.

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.

Avec Yammer, vous pouvez effectuer les opérations suivantes :

- Créer votre flux d’activité en fonction des données que vous obtenez de Yammer. 
- Utiliser des déclencheurs quand un nouveau message arrive dans un groupe ou un flux que vous suivez.
- Utiliser des actions pour publier un message, obtenir tous les messages et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, quand un nouveau message apparaît, vous pouvez envoyer un message électronique à l’aide d’Office 365.

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions
Yammer inclut les déclencheurs et les actions suivants.

Déclencheur | Actions
--- | ---
<ul><li>Quand il y a un nouveau message dans un groupe</li><li>Quand il y a un nouveau message dans le flux de mes abonnements</li></ul>| <ul><li>Obtenir tous les messages</li><li>Obtenir les messages d’un groupe</li><li>Obtenir les messages de mon flux d’abonnements</li><li>Publier un message</li><li>Quand il y a un nouveau message dans un groupe</li><li>Quand il y a un nouveau message dans le flux de mes abonnements</li></ul>

Toutes les API prennent en charge les données aux formats JSON et XML.

## Créer une connexion à Yammer
Pour utiliser l’API Yammer, vous devez créer une **connexion**, puis fournir les détails de ces propriétés :

|Propriété| Requis|Description|
| ---|---|---|
|Jeton|Oui|Indiquez les informations d’identification Yammer.|

>[AZURE.INCLUDE [Procédure de création d’une connexion à Yammer](../../includes/connectors-create-api-yammer.md)]


>[AZURE.TIP] Vous pouvez utiliser cette connexion dans d'autres applications logiques.

## Informations de référence sur l’API REST Yammer
Cette documentation concerne la version 1.0.


### Obtenir tous les messages publics dans le réseau Yammer de l’utilisateur connecté
Correspond à la catégorie de conversations « Toutes » dans l’interface web de Yammer. ```GET: /messages.json```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|older\_then|integer|no|query|(aucun)|Retourne des messages antérieurs à l’ID de message spécifié sous forme de chaîne numérique. Ce paramètre est utile pour paginer les messages. Par exemple, si vous voyez actuellement 20 messages et que le plus ancien porte le numéro 2 912, vous pouvez ajouter « ?older\_than=2912 » à votre demande pour obtenir les 20 messages antérieurs à ceux actuellement affichés.|
|newer\_then|integer|no|query|(aucun)|Retourne des messages postérieurs à l’ID de message spécifié sous forme de chaîne numérique. Utilisez ce paramètre pour déterminer s’il existe de nouveaux messages. Si vous voyez des messages et que le dernier message retourné porte le numéro 3 516, vous pouvez utiliser le paramètre « ?newer\_than=3516 » pour ne pas obtenir de doublons des messages actuellement affichés.|
|limit|integer|no|query|(aucun)|Retourner uniquement le nombre spécifié de messages.|
|page|integer|no|query|(aucun)|Obtenir la page spécifiée. Si les données retournées dépassent la limite, vous pouvez utiliser ce champ pour accéder aux pages suivantes.|


### Response

|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|408|Délai d’expiration de la demande|
|429|Trop de demandes|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|503|Service Yammer indisponible|
|504|Dépassement du délai de la passerelle|
|default|L’opération a échoué.|


### Publier un message dans un groupe ou dans tous les flux de l’entreprise.
Si l’ID de groupe est fourni, le message est publié dans le groupe spécifié. Sinon, il est publié dans tous les flux de l’entreprise. ```POST: /messages.json```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|entrée| |yes|body|(aucun)|Publier une demande de message|


### Response

|Nom|Description|
|---|---|
|201|Date de création|



## Définitions d’objet

#### Message : message Yammer

| Nom | Type de données | Requis |
|---|---| --- | 
|id|integer|no|
|content\_excerpt|string|no|
|sender\_id|integer|no|
|replied\_to\_id|integer|no|
|created\_at|string|no|
|network\_id|integer|no|
|message\_type|string|no|
|sender\_type|string|no|
|url|string|no|
|web\_url|string|no|
|group\_id|integer|no|
|body|non défini|no|
|thread\_id|integer|no|
|direct\_message|booléenne|no|
|client\_type|string|no|
|client\_url|string|no|
|language|string|no|
|notified\_user\_ids|array|no|
|privacy|string|no|
|liked\_by|non défini|no|
|system\_message|booléenne|no|

#### PostOperationRequest : représente une demande de publication sur Yammer pour le connecteur Yammer

| Nom | Type de données | Requis |
|---|---| --- | 
|body|string|yes|
|group\_id|integer|no|
|replied\_to\_id|integer|no|
|direct\_to\_id|integer|no|
|broadcast|booléenne|no|
|topic1|string|no|
|topic2|string|no|
|topic3|string|no|
|topic4|string|no|
|topic5|string|no|
|topic6|string|no|
|topic7|string|no|
|topic8|string|no|
|topic9|string|no|
|topic10|string|no|
|topic11|string|no|
|topic12|string|no|
|topic13|string|no|
|topic14|string|no|
|topic15|string|no|
|topic16|string|no|
|topic17|string|no|
|topic18|string|no|
|topic19|string|no|
|topic20|string|no|

#### MessageList : liste des messages

| Nom | Type de données | Requis |
|---|---| --- | 
|messages|array|no|


#### MessageBody : corps du message

| Nom | Type de données | Requis |
|---|---| --- | 
|parsed|string|no|
|plain|string|no|
|rich|string|no|

#### LikedBy : aimé par

| Nom | Type de données | Requis |
|---|---| --- | 
|count|integer|no|
|names|array|no|

#### YammmerEntity : aimé par

| Nom | Type de données | Requis |
|---|---| --- | 
|type|string|no|
|id|integer|no|
|full\_name|string|no|


## Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

[1]: ./media/connectors-create-api-yammer/connectionconfig1.png
[2]: ./media/connectors-create-api-yammer/connectionconfig2.png
[3]: ./media/connectors-create-api-yammer/connectionconfig3.png
[4]: ./media/connectors-create-api-yammer/connectionconfig4.png
[5]: ./media/connectors-create-api-yammer/connectionconfig5.png

<!---HONumber=AcomDC_0518_2016-->