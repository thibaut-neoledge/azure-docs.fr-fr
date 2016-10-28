<properties
pageTitle="Outlook.com | Microsoft Azure"
description="Créez des applications logiques avec Azure App Service. Le connecteur Outlook.com vous permet de gérer votre messagerie, les calendriers et les contacts. Vous pouvez effectuer différentes actions comme envoyer un message, planifier des réunions, ajouter des contacts, etc."
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

# Prise en main du connecteur Outlook.com

Le connecteur Outlook.com vous permet de gérer votre messagerie, les calendriers et les contacts. Vous pouvez effectuer différentes actions comme envoyer un message, planifier des réunions, ajouter des contacts, etc.

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.

Vous pouvez commencer par créer une application logique. Pour cela, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions

Le connecteur Outlook.com peut être utilisé en tant qu’action ; il possède un ou plusieurs déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML.

 Le connecteur Outlook.com met à votre disposition les actions et/ou les déclencheurs ci-après.

### Actions de Outlook.com
Vous pouvez effectuer les actions suivantes :

|Action|Description|
|--- | ---|
|[GetEmails](connectors-create-api-outlook.md#GetEmails)|Récupère des messages électroniques à partir d’un dossier|
|[SendEmail](connectors-create-api-outlook.md#SendEmail)|Envoie un message électronique|
|[DeleteEmail](connectors-create-api-outlook.md#DeleteEmail)|Supprime un message électronique par identifiant|
|[MarkAsRead](connectors-create-api-outlook.md#MarkAsRead)|Marque un message électronique comme lu|
|[ReplyTo](connectors-create-api-outlook.md#ReplyTo)|Répond à un message électronique|
|[GetAttachment](connectors-create-api-outlook.md#GetAttachment)|Récupère la pièce jointe à un message en fonction de l’identifiant|
|[SendMailWithOptions](connectors-create-api-outlook.md#SendMailWithOptions)|Envoie un message électronique avec plusieurs options et attend que le destinataire réponde avec une des options|
|[SendApprovalMail](connectors-create-api-outlook.md#SendApprovalMail)|Envoie un message électronique d’approbation et attend une réponse du destinataire|
|[CalendarGetTables](connectors-create-api-outlook.md#CalendarGetTables)|Récupère des calendriers|
|[CalendarGetItems](connectors-create-api-outlook.md#CalendarGetItems)|Récupère des éléments d’un calendrier|
|[CalendarPostItem](connectors-create-api-outlook.md#CalendarPostItem)|Crée un événement|
|[CalendarGetItem](connectors-create-api-outlook.md#CalendarGetItem)|Extrait un élément spécifique d’un calendrier|
|[CalendarDeleteItem](connectors-create-api-outlook.md#CalendarDeleteItem)|Supprime un élément de calendrier|
|[CalendarPatchItem](connectors-create-api-outlook.md#CalendarPatchItem)|Met à jour partiellement un élément de calendrier|
|[ContactGetTables](connectors-create-api-outlook.md#ContactGetTables)|Récupère des dossiers de contacts|
|[ContactGetItems](connectors-create-api-outlook.md#ContactGetItems)|Récupère les contacts d’un dossier de contacts|
|[ContactPostItem](connectors-create-api-outlook.md#ContactPostItem)|Crée un contact|
|[ContactGetItem](connectors-create-api-outlook.md#ContactGetItem)|Récupère un contact spécifique d’un dossier de contacts|
|[ContactDeleteItem](connectors-create-api-outlook.md#ContactDeleteItem)|Supprime un contact|
|[ContactPatchItem](connectors-create-api-outlook.md#ContactPatchItem)|Met à jour partiellement un contact|
### Déclencheurs de Outlook.com
Vous pouvez écouter les événements suivants :

|Déclencheur | Description|
|--- | ---|
|Événement imminent|Déclenche un flux au démarrage d’un événement de calendrier à venir|
|Arrivée d’un nouveau message électronique|Déclenche un flux à l’arrivée d’un nouveau message électronique|
|Quand des éléments sont créés|Déclenché à la création d’un élément de calendrier|
|Quand des éléments sont mis à jour|Déclenché quand un élément de calendrier est modifié|


## Créer une connexion à Outlook.com
Pour créer des applications logiques avec Outlook.com, vous devez d’abord créer une **connexion**, puis fournir les détails pour les propriétés suivantes :

|Propriété| Requis|Description|
| ---|---|---|
|Jeton|Oui|Fournir des informations d’identification Outlook.com|
Après avoir créé la connexion, vous pouvez l’utiliser pour exécuter les actions et écouter les déclencheurs décrits dans cet article.

>[AZURE.INCLUDE [Procédure de création d’une connexion à Outlook.com](../../includes/connectors-create-api-outlook.md)]

>[AZURE.TIP] Vous pouvez utiliser cette connexion dans d’autres applications logiques.

## Référence pour Outlook.com
S’applique à la version 1.0.

## OnUpcomingEvents
Événement imminent : déclenche un flux au démarrage d’un événement de calendrier à venir

```GET: /Events/OnUpcomingEvents```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|query|(aucun)|Identificateur unique du calendrier|
|lookAheadTimeInMinutes|integer|no|query|15|Plage (en minutes) dans laquelle rechercher les événements à venir|

#### Réponse

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|202|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


## GetEmails
Obtenir des messages électroniques : récupère des messages électroniques à partir d’un dossier

```GET: /Mail```

| Name| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderPath|string|no|query|Inbox|Chemin du dossier duquel récupérer des messages électroniques (valeur par défaut : « Inbox »)|
|top|integer|no|query|10|Nombre de messages électroniques à récupérer (valeur par défaut : 10)|
|fetchOnlyUnread|booléenne|no|query|true|Récupérer uniquement les messages électroniques non lus ?|
|includeAttachments|booléenne|no|query|false|Si défini sur true, les pièces jointes sont également récupérées avec les messages électroniques|
|searchQuery|string|no|query|(aucun)|Requête de recherche pour filtrer les messages électroniques|
|skip|integer|no|query|0|Nombre de messages électroniques à ignorer (valeur par défaut : 0)|
|skipToken|string|no|query|(aucun)|Jeton d’évitement pour récupérer une nouvelle page|

#### Réponse

|Name|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


## SendEmail
Envoyer un message électronique : envoie un message électronique

```POST: /Mail```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|emailMessage| |yes|body|(aucun)|Email|

#### Réponse

|Name|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


## DeleteEmail
Supprimer un message électronique : supprime un message électronique par identifiant

```DELETE: /Mail/{messageId}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|messageId|string|yes|path|(aucun)|ID du message électronique à supprimer|

#### Réponse

|Name|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


## MarkAsRead
Marquer comme lu : marque un message électronique comme lu

```POST: /Mail/MarkAsRead/{messageId}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|messageId|string|yes|path|(aucun)|Identifiant du message électronique à marquer comme lu|

#### Réponse

|Name|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


## ReplyTo
Répondre à un message électronique : répond à un message électronique

```POST: /Mail/ReplyTo/{messageId}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|messageId|string|yes|path|(aucun)|ID du message électronique auquel répondre|
|comment|string|yes|query|(aucun)|Commentaire de réponse|
|replyAll|booléenne|no|query|false|Répondre à tous les destinataires|

#### Réponse

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


## GetAttachment
Obtenir une pièce jointe : récupère la pièce jointe à un message en fonction de l’identifiant

```GET: /Mail/{messageId}/Attachments/{attachmentId}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|messageId|string|yes|path|(aucun)|ID du message électronique|
|attachmentId|string|yes|path|(aucun)|Identifiant de la pièce jointe à télécharger|

#### Réponse

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


## OnNewEmail
Arrivée d’un nouveau message électronique: déclenche un flux à l’arrivée d’un nouveau message électronique

```GET: /Mail/OnNewEmail```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderPath|string|no|query|Inbox|Dossier de courrier électronique à récupérer (valeur par défaut : Inbox)|
|to|string|no|query|(aucun)|Adresses de messagerie des destinataires|
|from|string|no|query|(aucun)|Adresse de l’expéditeur|
|importance|string|no|query|Normal|Importance du message (High, Normal, Low) (valeur par défaut : Normal)|
|fetchOnlyWithAttachment|booléenne|no|query|false|Récupérer uniquement les messages électroniques avec une pièce jointe|
|includeAttachments|booléenne|no|query|false|Inclure les pièces jointes|
|subjectFilter|string|no|query|(aucun)|Chaîne à rechercher dans l’objet|

#### Réponse

|Name|Description|
|---|---|
|200|L’opération a réussi|
|202|Acceptée|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


## SendMailWithOptions
Envoyer un message électronique avec des options : envoie un message électronique avec plusieurs options et attend que le destinataire réponde avec une des options

```POST: /mailwithoptions/$subscriptions```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|optionsEmailSubscription| |yes|body|(aucun)|Demande d’abonnement pour courrier électronique avec options|

#### Réponse

|Nom|Description|
|---|---|
|200|OK|
|201|Abonnement créé|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


## SendApprovalMail
Envoyer un message électronique d’approbation : envoie un message électronique d’approbation et attend une réponse du destinataire

```POST: /approvalmail/$subscriptions```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|approvalEmailSubscription| |yes|body|(aucun)|Demande d’abonnement pour courrier électronique d’approbation|

#### Réponse

|Nom|Description|
|---|---|
|200|OK|
|201|Abonnement créé|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


## CalendarGetTables
Obtenir des calendriers : récupère des calendriers

```GET: /datasets/calendars/tables```

Il n’existe aucun paramètre pour cet appel
#### Réponse

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## CalendarGetItems
Obtenir des événements : récupère des éléments d’un calendrier

```GET: /datasets/calendars/tables/{table}/items```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique du calendrier à récupérer|
|$filter|string|no|query|(aucun)|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|string|no|query|(aucun)|Requête orderBy ODATA pour spécifier l’ordre des entrées|
|$skip|integer|no|query|(aucun)|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|integer|no|query|(aucun)|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|

#### Réponse

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## CalendarPostItem
Créer un événement : crée un nouvel événement

```POST: /datasets/calendars/tables/{table}/items```

| Name| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un calendrier|
|item| |yes|body|(aucun)|Élément de calendrier à créer|

#### Réponse

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## CalendarGetItem
Obtenir un événement : extrait un élément spécifique d’un calendrier

```GET: /datasets/calendars/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un calendrier|
|id|string|yes|path|(aucun)|Identificateur unique d’un élément de calendrier à récupérer|

#### Réponse

|Name|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## CalendarDeleteItem
Supprimer un événement : supprime un élément de calendrier

```DELETE: /datasets/calendars/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un calendrier|
|id|string|yes|path|(aucun)|Identificateur unique de l’élément de calendrier à supprimer|

#### Réponse

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## CalendarPatchItem
Mettre à jour l’événement : met à jour partiellement un élément de calendrier

```PATCH: /datasets/calendars/tables/{table}/items/{id}```

| Name| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un calendrier|
|id|string|yes|path|(aucun)|Identificateur unique de l’élément de calendrier à mettre à jour|
|item| |yes|body|(aucun)|Élément de calendrier à mettre à jour|

#### Réponse

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## CalendarGetOnNewItems
Quand des éléments sont créés : déclenché à la création d’un élément de calendrier

```GET: /datasets/calendars/tables/{table}/onnewitems```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un calendrier|
|$filter|string|no|query|(aucun)|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|string|no|query|(aucun)|Requête orderBy ODATA pour spécifier l’ordre des entrées|
|$skip|integer|no|query|(aucun)|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|integer|no|query|(aucun)|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|

#### Réponse

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## CalendarGetOnUpdatedItems
Quand des éléments sont mis à jour : déclenché quand un élément de calendrier est modifié

```GET: /datasets/calendars/tables/{table}/onupdateditems```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un calendrier|
|$filter|string|no|query|(aucun)|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|string|no|query|(aucun)|Requête orderBy ODATA pour spécifier l’ordre des entrées|
|$skip|integer|no|query|(aucun)|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|integer|no|query|(aucun)|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|

#### Réponse

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## ContactGetTables
Obtenir des dossiers de contacts : récupère des dossiers de contacts

```GET: /datasets/contacts/tables```

Il n’existe aucun paramètre pour cet appel
#### Réponse

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## ContactGetItems
Obtenir des contacts : récupère les contacts d’un dossier de contacts

```GET: /datasets/contacts/tables/{table}/items```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique du dossier de contacts à récupérer|
|$filter|string|no|query|(aucun)|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|string|no|query|(aucun)|Requête orderBy ODATA pour spécifier l’ordre des entrées|
|$skip|integer|no|query|(aucun)|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|integer|no|query|(aucun)|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|

#### Réponse

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## ContactPostItem
Créer un contact : crée un nouveau contact

```POST: /datasets/contacts/tables/{table}/items```

| Name| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un dossier de contacts|
|item| |yes|body|(aucun)|Contact à créer|

#### Réponse

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## ContactGetItem
Obtenir un contact : récupère un contact spécifique d’un dossier de contacts

```GET: /datasets/contacts/tables/{table}/items/{id}```

| Name| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un dossier de contacts|
|id|string|yes|path|(aucun)|Identificateur unique d’un contact à récupérer|

#### Réponse

|Name|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## ContactDeleteItem
Supprimer un contact : supprime un contact

```DELETE: /datasets/contacts/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un dossier de contacts|
|id|string|yes|path|(aucun)|Identificateur unique du contact à supprimer|

#### Réponse

|Name|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## ContactPatchItem
Mettre à jour un contact : met à jour partiellement un contact

```PATCH: /datasets/contacts/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un dossier de contacts|
|id|string|yes|path|(aucun)|Identificateur unique du contact à mettre à jour|
|item| |yes|body|(aucun)|Élément de contact à mettre à jour|

#### Réponse

|Name|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## Définitions d’objet 

### TriggerBatchResponse[IDictionary[String,Object]]


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|value|array|Non |



### Object


| Nom de la propriété | Type de données | Requis |
|---|---|---|



### SendMessage


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|Pièces jointes|array|Non |
|À partir d'une base|string|Non |
|Cc|string|Non |
|Cci|string|Non |
|Objet|string|Oui |
|Corps|string|Oui |
|Importance|string|Non |
|IsHtml|booléenne|Non |
|À|string|Oui |



### SendAttachment


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|@odata.type|string|Non |
|Name|string|Oui |
|ContentBytes|string|Oui |



### ReceiveMessage


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|ID|string|Non |
|IsRead|booléenne|Non |
|HasAttachment|booléenne|Non |
|DateTimeReceived|string|Non |
|Pièces jointes|array|Non |
|À partir d'une base|string|Non |
|Cc|string|Non |
|Cci|string|Non |
|Objet|string|Oui |
|Corps|string|Oui |
|Importance|string|Non |
|IsHtml|booléenne|Non |
|À|string|Oui |



### ReceiveAttachment


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|ID|string|Oui |
|ContentType|string|Oui |
|@odata.type|string|Non |
|Nom|string|Oui |
|ContentBytes|string|Oui |



### DigestMessage


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|Objet|string|Oui |
|Corps|string|Non |
|Importance|string|Non |
|Digest|array|Oui |
|Pièces jointes|array|Non |
|À|string|Oui |



### TriggerBatchResponse[ReceiveMessage]


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|value|array|Non |



### DataSetsMetadata


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|tabular|non défini|Non |
|objet blob|non défini|Non |



### TabularDataSetsMetadata


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|source|string|Non |
|displayName|string|Non |
|urlEncoding|string|Non |
|tableDisplayName|string|Non |
|tablePluralName|string|Non |



### BlobDataSetsMetadata


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|source|string|Non |
|displayName|string|Non |
|urlEncoding|string|Non |



### TableMetadata


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|name|string|Non |
|title|string|Non |
|x-ms-permission|string|Non |
|x-ms-capabilities|non défini|Non |
|schema|non défini|Non |



### TableCapabilitiesMetadata


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|sortRestrictions|non défini|Non |
|filterRestrictions|non défini|Non |
|filterFunctions|array|Non |



### TableSortRestrictionsMetadata


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|sortable|booléenne|Non |
|unsortableProperties|array|Non |
|ascendingOnlyProperties|array|Non |



### TableFilterRestrictionsMetadata


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|filterable|booléenne|Non |
|nonFilterableProperties|array|Non |
|requiredProperties|array|Non |



### OptionsEmailSubscription


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|NotificationUrl|string|Non |
|Message|non défini|Non |



### MessageWithOptions


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|Objet|string|Oui |
|Options|string|Oui |
|Corps|string|Non |
|Importance|string|Non |
|Pièces jointes|array|Non |
|À|string|Oui |



### SubscriptionResponse


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|id|string|Non |
|resource|string|Non |
|notificationType|string|Non |
|notificationUrl|string|Non |



### ApprovalEmailSubscription


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|NotificationUrl|string|Non |
|Message|non défini|Non |



### ApprovalMessage


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|Objet|string|Oui |
|Options|string|Oui |
|Corps|string|Non |
|Importance|string|Non |
|Pièces jointes|array|Non |
|À|string|Oui |



### ApprovalEmailResponse


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|SelectedOption|string|Non |



### TablesList


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|value|array|Non |



### Table


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|Nom|string|Non |
|DisplayName|string|Non |



### Item


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|ItemInternalId|string|Non |



### CalendarItemsList


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|value|array|Non |



### CalendarItem


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|ItemInternalId|string|Non |



### ContactItemsList


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|value|array|Non |



### ContactItem


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|ItemInternalId|string|Non |



### DataSetsList


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|value|array|Non |



### DataSet


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|Nom|string|Non |
|DisplayName|string|Non |


## Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->