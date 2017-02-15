---
title: Outlook.com | Microsoft Docs
description: "Créez des applications logiques avec Azure App Service. Le connecteur Outlook.com vous permet de gérer votre messagerie, les calendriers et les contacts. Vous pouvez effectuer différentes actions comme envoyer un message, planifier des réunions, ajouter des contacts, etc."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 409e1a104fa73d911ea508cbff311cb48fc20f9f


---
# <a name="get-started-with-the-outlookcom-connector"></a>Prise en main du connecteur Outlook.com
Le connecteur Outlook.com vous permet de gérer votre messagerie, les calendriers et les contacts. Vous pouvez effectuer différentes actions comme envoyer un message, planifier des réunions, ajouter des contacts, etc.

> [!NOTE]
> Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.
>
>

Vous pouvez commencer par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Le connecteur Outlook.com peut être utilisé en tant qu’action ; il possède un ou plusieurs déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML.

 Le connecteur Outlook.com met à votre disposition les actions et/ou les déclencheurs ci-après.

### <a name="outlookcom-actions"></a>Actions de Outlook.com
Vous pouvez effectuer les actions suivantes :

| Action | Description |
| --- | --- |
| [GetEmails](connectors-create-api-outlook.md#getemails) |Récupère des messages électroniques à partir d’un dossier |
| [SendEmail](connectors-create-api-outlook.md#sendemail) |Envoie un message électronique |
| [DeleteEmail](connectors-create-api-outlook.md#deleteemail) |Supprime un message électronique par identifiant |
| [MarkAsRead](connectors-create-api-outlook.md#markasread) |Marque un message électronique comme lu |
| [ReplyTo](connectors-create-api-outlook.md#replyto) |Répond à un message électronique |
| [GetAttachment](connectors-create-api-outlook.md#getattachment) |Récupère la pièce jointe à un message en fonction de l’identifiant |
| [SendMailWithOptions](connectors-create-api-outlook.md#sendmailwithoptions) |Envoie un message électronique avec plusieurs options et attend que le destinataire réponde avec une des options |
| [SendApprovalMail](connectors-create-api-outlook.md#sendapprovalmail) |Envoie un message électronique d’approbation et attend une réponse du destinataire |
| [CalendarGetTables](connectors-create-api-outlook.md#calendargettables) |Récupère des calendriers |
| [CalendarGetItems](connectors-create-api-outlook.md#calendargetitems) |Récupère des éléments d’un calendrier |
| [CalendarPostItem](connectors-create-api-outlook.md#calendarpostitem) |Crée un événement |
| [CalendarGetItem](connectors-create-api-outlook.md#calendargetitem) |Extrait un élément spécifique d’un calendrier |
| [CalendarDeleteItem](connectors-create-api-outlook.md#calendardeleteitem) |Supprime un élément de calendrier |
| [CalendarPatchItem](connectors-create-api-outlook.md#calendarpatchitem) |Met à jour partiellement un élément de calendrier |
| [ContactGetTables](connectors-create-api-outlook.md#contactgettables) |Récupère des dossiers de contacts |
| [ContactGetItems](connectors-create-api-outlook.md#contactgetitems) |Récupère les contacts d’un dossier de contacts |
| [ContactPostItem](connectors-create-api-outlook.md#contactpostitem) |Crée un contact |
| [ContactGetItem](connectors-create-api-outlook.md#contactgetitem) |Récupère un contact spécifique d’un dossier de contacts |
| [ContactDeleteItem](connectors-create-api-outlook.md#contactdeleteitem) |Supprime un contact |
| [ContactPatchItem](connectors-create-api-outlook.md#contactpatchitem) |Met à jour partiellement un contact |

### <a name="outlookcom-triggers"></a>Déclencheurs de Outlook.com
Vous pouvez écouter les événements suivants :

| Déclencheur | Description |
| --- | --- |
| Événement imminent |Déclenche un flux au démarrage d’un événement de calendrier à venir |
| Arrivée d’un nouveau message électronique |Déclenche un flux à l’arrivée d’un nouveau message électronique |
| Quand des éléments sont créés |Déclenché à la création d’un élément de calendrier |
| Quand des éléments sont mis à jour |Déclenché quand un élément de calendrier est modifié |

## <a name="create-a-connection-to-outlookcom"></a>Créer une connexion à Outlook.com
Pour créer des applications logiques avec Outlook.com, vous devez d’abord créer une **connexion**, puis fournir les détails pour les propriétés suivantes :

| Propriété | Requis | Description |
| --- | --- | --- |
| Jeton |Oui |Fournir des informations d’identification Outlook.com |

Après avoir créé la connexion, vous pouvez l’utiliser pour exécuter les actions et écouter les déclencheurs décrits dans cet article.

> [!INCLUDE [Steps to create a connection to Outlook.com](../../includes/connectors-create-api-outlook.md)]
>
> [!TIP]
> Vous pouvez utiliser cette connexion dans d'autres applications logiques.  
>
>

## <a name="reference-for-outlookcom"></a>Référence pour Outlook.com
S’applique à la version 1.0.

## <a name="onupcomingevents"></a>OnUpcomingEvents
Événement imminent : déclenche un flux au démarrage d’un événement de calendrier à venir

```GET: /Events/OnUpcomingEvents```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| table |string |yes |query |(aucun) |Identificateur unique du calendrier |
| lookAheadTimeInMinutes |integer |no |query |15 |Plage (en minutes) dans laquelle rechercher les événements à venir |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 202 |L’opération a réussi |
| 400 |BadRequest |
| 401 |Non autorisé |
| 403 |Interdit |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

## <a name="getemails"></a>GetEmails
Obtenir des messages électroniques : récupère des messages électroniques à partir d’un dossier

```GET: /Mail```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |no |query |Inbox |Chemin du dossier duquel récupérer des messages électroniques (valeur par défaut : « Inbox ») |
| top |integer |no |query |10 |Nombre de messages électroniques à récupérer (valeur par défaut : 10) |
| fetchOnlyUnread |booléenne |no |query |true |Récupérer uniquement les messages électroniques non lus ? |
| includeAttachments |booléenne |no |query |false |Si défini sur true, les pièces jointes sont également récupérées avec les messages électroniques |
| searchQuery |string |no |query |(aucun) |Requête de recherche pour filtrer les messages électroniques |
| skip |integer |no |query |0 |Nombre de messages électroniques à ignorer (valeur par défaut : 0) |
| skipToken |string |no |query |(aucun) |Jeton d’évitement pour récupérer une nouvelle page |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 400 |BadRequest |
| 401 |Non autorisé |
| 403 |Interdit |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

## <a name="sendemail"></a>SendEmail
Envoyer un message électronique : envoie un message électronique

```POST: /Mail```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| emailMessage | |yes |body |(aucun) |Email |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 400 |BadRequest |
| 401 |Non autorisé |
| 403 |Interdit |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

## <a name="deleteemail"></a>DeleteEmail
Supprimer un message électronique : supprime un message électronique par identifiant

```DELETE: /Mail/{messageId}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| messageId |string |Oui |path |(aucun) |ID du message électronique à supprimer |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 400 |BadRequest |
| 401 |Non autorisé |
| 403 |Interdit |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

## <a name="markasread"></a>MarkAsRead
Marquer comme lu : marque un message électronique comme lu

```POST: /Mail/MarkAsRead/{messageId}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| messageId |string |Oui |path |(aucun) |Identifiant du message électronique à marquer comme lu |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 400 |BadRequest |
| 401 |Non autorisé |
| 403 |Interdit |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

## <a name="replyto"></a>ReplyTo
Répondre à un message électronique : répond à un message électronique

```POST: /Mail/ReplyTo/{messageId}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| messageId |string |Oui |path |(aucun) |ID du message électronique auquel répondre |
| comment |string |yes |query |(aucun) |Commentaire de réponse |
| replyAll |booléenne |no |query |false |Répondre à tous les destinataires |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 400 |BadRequest |
| 401 |Non autorisé |
| 403 |Interdit |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

## <a name="getattachment"></a>GetAttachment
Obtenir une pièce jointe : récupère la pièce jointe à un message en fonction de l’identifiant

```GET: /Mail/{messageId}/Attachments/{attachmentId}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| messageId |string |Oui |path |(aucun) |ID du message électronique |
| attachmentId |string |Oui |path |(aucun) |Identifiant de la pièce jointe à télécharger |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 400 |BadRequest |
| 401 |Non autorisé |
| 403 |Interdit |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

## <a name="onnewemail"></a>OnNewEmail
Arrivée d’un nouveau message électronique: déclenche un flux à l’arrivée d’un nouveau message électronique

```GET: /Mail/OnNewEmail```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |no |query |Inbox |Dossier de courrier électronique à récupérer (valeur par défaut : Inbox) |
| to |string |no |query |(aucun) |Adresses de messagerie des destinataires |
| from |string |no |query |(aucun) |Adresse de l’expéditeur |
| importance |string |no |query |Normal |Importance du message (High, Normal, Low) (valeur par défaut : Normal) |
| fetchOnlyWithAttachment |booléenne |no |query |false |Récupérer uniquement les messages électroniques avec une pièce jointe |
| includeAttachments |booléenne |no |query |false |Inclure les pièces jointes |
| subjectFilter |string |no |query |(aucun) |Chaîne à rechercher dans l’objet |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 202 |Acceptée |
| 400 |BadRequest |
| 401 |Non autorisé |
| 403 |Interdit |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

## <a name="sendmailwithoptions"></a>SendMailWithOptions
Envoyer un message électronique avec des options : envoie un message électronique avec plusieurs options et attend que le destinataire réponde avec une des options

```POST: /mailwithoptions/$subscriptions```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| optionsEmailSubscription | |yes |body |(aucun) |Demande d’abonnement pour courrier électronique avec options |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| 201 |Abonnement créé |
| 400 |BadRequest |
| 401 |Non autorisé |
| 403 |Interdit |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

## <a name="sendapprovalmail"></a>SendApprovalMail
Envoyer un message électronique d’approbation : envoie un message électronique d’approbation et attend une réponse du destinataire

```POST: /approvalmail/$subscriptions```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| approvalEmailSubscription | |yes |body |(aucun) |Demande d’abonnement pour courrier électronique d’approbation |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| 201 |Abonnement créé |
| 400 |BadRequest |
| 401 |Non autorisé |
| 403 |Interdit |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

## <a name="calendargettables"></a>CalendarGetTables
Obtenir des calendriers : récupère des calendriers

```GET: /datasets/calendars/tables```

Il n’existe aucun paramètre pour cet appel

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="calendargetitems"></a>CalendarGetItems
Obtenir des événements : récupère des éléments d’un calendrier

```GET: /datasets/calendars/tables/{table}/items```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| table |string |Oui |path |(aucun) |Identificateur unique du calendrier à récupérer |
| $filter |string |no |query |Aucun |Requête filter ODATA pour limiter le nombre d’entrées |
| $orderby |string |no |query |Aucun |Requête orderBy ODATA pour spécifier l’ordre des entrées |
| $skip |integer |no |query |Aucun |Nombre d’entrées à ignorer (valeur par défaut : 0) |
| $top |integer |no |query |Aucun |Nombre maximal d’entrées à récupérer (valeur par défaut : 256) |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="calendarpostitem"></a>CalendarPostItem
Créer un événement : crée un nouvel événement

```POST: /datasets/calendars/tables/{table}/items```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| table |string |Oui |path |(aucun) |Identificateur unique d’un calendrier |
| item | |Oui |body |(aucun) |Élément de calendrier à créer |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="calendargetitem"></a>CalendarGetItem
Obtenir un événement : extrait un élément spécifique d’un calendrier

```GET: /datasets/calendars/tables/{table}/items/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| table |string |Oui |path |(aucun) |Identificateur unique d’un calendrier |
| id |string |Oui |path |(aucun) |Identificateur unique d’un élément de calendrier à récupérer |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="calendardeleteitem"></a>CalendarDeleteItem
Supprimer un événement : supprime un élément de calendrier

```DELETE: /datasets/calendars/tables/{table}/items/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| table |string |Oui |path |(aucun) |Identificateur unique d’un calendrier |
| id |string |Oui |path |(aucun) |Identificateur unique de l’élément de calendrier à supprimer |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="calendarpatchitem"></a>CalendarPatchItem
Mettre à jour l’événement : met à jour partiellement un élément de calendrier

```PATCH: /datasets/calendars/tables/{table}/items/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| table |string |Oui |path |(aucun) |Identificateur unique d’un calendrier |
| id |string |Oui |path |(aucun) |Identificateur unique de l’élément de calendrier à mettre à jour |
| item | |Oui |body |(aucun) |Élément de calendrier à mettre à jour |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="calendargetonnewitems"></a>CalendarGetOnNewItems
Quand des éléments sont créés : déclenché à la création d’un élément de calendrier

```GET: /datasets/calendars/tables/{table}/onnewitems```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| table |string |Oui |path |(aucun) |Identificateur unique d’un calendrier |
| $filter |string |no |query |Aucun |Requête filter ODATA pour limiter le nombre d’entrées |
| $orderby |string |no |query |Aucun |Requête orderBy ODATA pour spécifier l’ordre des entrées |
| $skip |integer |no |query |Aucun |Nombre d’entrées à ignorer (valeur par défaut : 0) |
| $top |integer |no |query |Aucun |Nombre maximal d’entrées à récupérer (valeur par défaut : 256) |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="calendargetonupdateditems"></a>CalendarGetOnUpdatedItems
Quand des éléments sont mis à jour : déclenché quand un élément de calendrier est modifié

```GET: /datasets/calendars/tables/{table}/onupdateditems```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| table |string |Oui |path |(aucun) |Identificateur unique d’un calendrier |
| $filter |string |no |query |Aucun |Requête filter ODATA pour limiter le nombre d’entrées |
| $orderby |string |no |query |Aucun |Requête orderBy ODATA pour spécifier l’ordre des entrées |
| $skip |integer |no |query |Aucun |Nombre d’entrées à ignorer (valeur par défaut : 0) |
| $top |integer |no |query |Aucun |Nombre maximal d’entrées à récupérer (valeur par défaut : 256) |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="contactgettables"></a>ContactGetTables
Obtenir des dossiers de contacts : récupère des dossiers de contacts

```GET: /datasets/contacts/tables```

Il n’existe aucun paramètre pour cet appel

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="contactgetitems"></a>ContactGetItems
Obtenir des contacts : récupère les contacts d’un dossier de contacts

```GET: /datasets/contacts/tables/{table}/items```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| table |string |Oui |path |(aucun) |Identificateur unique du dossier de contacts à récupérer |
| $filter |string |no |query |Aucun |Requête filter ODATA pour limiter le nombre d’entrées |
| $orderby |string |no |query |Aucun |Requête orderBy ODATA pour spécifier l’ordre des entrées |
| $skip |integer |no |query |Aucun |Nombre d’entrées à ignorer (valeur par défaut : 0) |
| $top |integer |no |query |Aucun |Nombre maximal d’entrées à récupérer (valeur par défaut : 256) |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="contactpostitem"></a>ContactPostItem
Créer un contact : crée un nouveau contact

```POST: /datasets/contacts/tables/{table}/items```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| table |string |Oui |path |(aucun) |Identificateur unique d’un dossier de contacts |
| item | |Oui |body |(aucun) |Contact à créer |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="contactgetitem"></a>ContactGetItem
Obtenir un contact : récupère un contact spécifique d’un dossier de contacts

```GET: /datasets/contacts/tables/{table}/items/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| table |string |Oui |path |(aucun) |Identificateur unique d’un dossier de contacts |
| id |string |Oui |path |(aucun) |Identificateur unique d’un contact à récupérer |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="contactdeleteitem"></a>ContactDeleteItem
Supprimer un contact : supprime un contact

```DELETE: /datasets/contacts/tables/{table}/items/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| table |string |Oui |path |(aucun) |Identificateur unique d’un dossier de contacts |
| id |string |Oui |path |(aucun) |Identificateur unique du contact à supprimer |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="contactpatchitem"></a>ContactPatchItem
Mettre à jour un contact : met à jour partiellement un contact

```PATCH: /datasets/contacts/tables/{table}/items/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| table |string |Oui |path |(aucun) |Identificateur unique d’un dossier de contacts |
| id |string |Oui |path |(aucun) |Identificateur unique du contact à mettre à jour |
| item | |Oui |body |(aucun) |Élément de contact à mettre à jour |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="object-definitions"></a>Définitions d'objet
### <a name="triggerbatchresponseidictionarystringobject"></a>TriggerBatchResponse[IDictionary[String,Object]]
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| value |array |Non |

### <a name="object"></a>Object
### <a name="sendmessage"></a>SendMessage
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Pièces jointes |array |Non |
| À partir d'une base |string |Non |
| Cc |string |Non |
| Cci |string |Non |
| Objet |string |Oui |
| Corps |string |Oui |
| Importance |string |Non |
| IsHtml |booléenne |Non |
| À |string |Oui |

### <a name="sendattachment"></a>SendAttachment
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| @odata.type |string |Non |
| Nom |string |Oui |
| ContentBytes |string |Oui |

### <a name="receivemessage"></a>ReceiveMessage
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |Non |
| IsRead |booléenne |Non |
| HasAttachment |booléenne |Non |
| DateTimeReceived |string |Non |
| Pièces jointes |array |Non |
| À partir d'une base |string |Non |
| Cc |string |Non |
| Cci |string |Non |
| Objet |string |Oui |
| Corps |string |Oui |
| Importance |string |Non |
| IsHtml |booléenne |Non |
| À |string |Oui |

### <a name="receiveattachment"></a>ReceiveAttachment
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |Oui |
| ContentType |string |Oui |
| @odata.type |string |Non |
| Nom |string |Oui |
| ContentBytes |string |Oui |

### <a name="digestmessage"></a>DigestMessage
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Objet |string |Oui |
| Corps |string |Non |
| Importance |string |Non |
| Digest |array |Oui |
| Pièces jointes |array |Non |
| À |string |Oui |

### <a name="triggerbatchresponsereceivemessage"></a>TriggerBatchResponse[ReceiveMessage]
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| value |array |Non |

### <a name="datasetsmetadata"></a>DataSetsMetadata
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| tabular |non défini |Non |
| objet blob |non défini |Non |

### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| source |string |Non |
| displayName |string |Non |
| urlEncoding |string |Non |
| tableDisplayName |string |Non |
| tablePluralName |string |Non |

### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| source |string |Non |
| displayName |string |Non |
| urlEncoding |string |Non |

### <a name="tablemetadata"></a>TableMetadata
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Nom |string |Non |
| title |string |Non |
| x-ms-permission |string |Non |
| x-ms-capabilities |non défini |Non |
| schema |non défini |Non |

### <a name="tablecapabilitiesmetadata"></a>TableCapabilitiesMetadata
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| sortRestrictions |non défini |Non |
| filterRestrictions |non défini |Non |
| filterFunctions |array |Non |

### <a name="tablesortrestrictionsmetadata"></a>TableSortRestrictionsMetadata
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| sortable |booléenne |Non |
| unsortableProperties |array |Non |
| ascendingOnlyProperties |array |Non |

### <a name="tablefilterrestrictionsmetadata"></a>TableFilterRestrictionsMetadata
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| filterable |booléenne |Non |
| nonFilterableProperties |array |Non |
| requiredProperties |array |Non |

### <a name="optionsemailsubscription"></a>OptionsEmailSubscription
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| NotificationUrl |string |Non |
| Message |non défini |Non |

### <a name="messagewithoptions"></a>MessageWithOptions
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Objet |string |Oui |
| Options |string |Oui |
| Corps |string |Non |
| Importance |string |Non |
| Pièces jointes |array |Non |
| À |string |Oui |

### <a name="subscriptionresponse"></a>SubscriptionResponse
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |Non |
| resource |string |Non |
| notificationType |string |Non |
| notificationUrl |string |Non |

### <a name="approvalemailsubscription"></a>ApprovalEmailSubscription
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| NotificationUrl |string |Non |
| Message |non défini |Non |

### <a name="approvalmessage"></a>ApprovalMessage
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Objet |string |Oui |
| Options |string |Oui |
| Corps |string |Non |
| Importance |string |Non |
| Pièces jointes |array |Non |
| À |string |Oui |

### <a name="approvalemailresponse"></a>ApprovalEmailResponse
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| SelectedOption |string |Non |

### <a name="tableslist"></a>TablesList
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| value |array |Non |

### <a name="table"></a>Table
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Nom |string |Non |
| DisplayName |string |Non |

### <a name="item"></a>Item
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| ItemInternalId |string |Non |

### <a name="calendaritemslist"></a>CalendarItemsList
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| value |array |Non |

### <a name="calendaritem"></a>CalendarItem
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| ItemInternalId |string |Non |

### <a name="contactitemslist"></a>ContactItemsList
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| value |array |Non |

### <a name="contactitem"></a>ContactItem
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| ItemInternalId |string |Non |

### <a name="datasetslist"></a>DataSetsList
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| value |array |Non |

### <a name="dataset"></a>DataSet
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Nom |string |Non |
| DisplayName |string |Non |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md)



<!--HONumber=Feb17_HO2-->


