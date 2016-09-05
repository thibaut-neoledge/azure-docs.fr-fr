<properties
pageTitle="SendGrid | Microsoft Azure"
description="Créez des applications logiques avec Azure App Service. Le fournisseur de connexion SendGrid vous permet d’envoyer un message électronique et de gérer les listes de destinataires."
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# Prise en main du connecteur SendGrid

Le fournisseur de connexion SendGrid vous permet d’envoyer un message électronique et de gérer les listes de destinataires.

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.

Vous pouvez commencer par créer une application logique. Pour cela, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions

Le connecteur SendGrid peut être utilisé en tant qu’action ; il possède un ou plusieurs déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML.

 Le connecteur SendGrid met à votre disposition les actions ci-après. Il n'y a aucun déclencheur.

### Actions de SendGrid
Vous pouvez effectuer les actions suivantes :

|Action|Description|
|--- | ---|
|[SendEmail](connectors-create-api-sendgrid.md#sendemail)|Envoie un message électronique à l’aide de l’API SendGrid (limité à 10 000 destinataires)|
|[AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist)|Ajouter un destinataire individuel à une liste de destinataires|


## Créer une connexion à SendGrid
Pour créer des applications logiques avec SendGrid, vous devez d’abord créer une **connexion**, puis fournir les détails pour les propriétés suivantes :

|Propriété| Requis|Description|
| ---|---|---|
|ApiKey|Oui|Fournir votre clé d’API SendGrid|
 

>[AZURE.INCLUDE [Procédure de création d’une connexion à SendGrid](../../includes/connectors-create-api-sendgrid.md)]

>[AZURE.TIP] Vous pouvez utiliser cette connexion dans d’autres applications logiques.

Après avoir créé la connexion, vous pouvez l’utiliser pour exécuter les actions et écouter les déclencheurs décrits dans cet article.

## Référence pour SendGrid
S’applique à la version 1.0.

## SendEmail
Envoyer un message électronique : envoie un message électronique à l’aide de l’API SendGrid (limité à 10 000 destinataires)

```POST: /api/mail.send.json```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|request| |yes|body|(aucun)|Message électronique à envoyer|

#### Réponse

|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|429|Trop de demandes|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


## AddRecipientToList
Ajouter un destinataire à une liste : ajouter un destinataire individuel à une liste de destinataires

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}```

| Name| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|listId|string|yes|path|(aucun)|ID unique de la liste des destinataires|
|recipientId|string|yes|path|(aucun)|ID unique du destinataire|

#### Réponse

|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


## Définitions d’objet 

### EmailRequest


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|from|string|Oui |
|fromname|string|Non |
|to|string|Oui |
|toname|string|Non |
|subject|string|Oui |
|body|string|Oui |
|ishtml|booléenne|Non |
|cc|string|Non |
|ccname|string|Non |
|bcc|string|Non |
|bccname|string|Non |
|replyto|string|Non |
|date|string|Non |
|headers|string|Non |
|fichiers d'entrée|array|Non |
|filenames|array|Non |



### EmailResponse


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|message|string|Non |



### RecipientLists


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|lists|array|Non |



### RecipientList


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|id|integer|Non |
|name|string|Non |
|recipient\_count|integer|Non |



### Recipients


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|recipients|array|Non |



### Recipient


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|email|string|Non |
|last\_name|string|Non |
|first\_name|string|Non |
|id|string|Non |


## Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->