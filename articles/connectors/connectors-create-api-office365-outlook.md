<properties
    pageTitle="Ajouter le connecteur Office 365 Outlook à PowerApps Enterprise ou à des applications logiques | Microsoft Azure"
    description="Vue d’ensemble du connecteur Office 365 Outlook avec les paramètres de l’API REST"
    services=""    
    documentationCenter=""     
    authors="msftman"    
    manager="erikre"    
    editor="" 
    tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="05/18/2016"
ms.author="mandia"/>

# Prise en main du connecteur Office 365 Outlook 

Connectez-vous à Office 365 Outlook pour recevoir des messages électroniques, répondre à un message électronique, mettre à jour vos calendrier et contacts, et bien plus encore. Le connecteur Office 365 Outlook peut être utilisé dans :

- Logic Apps 
- PowerApps

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/connectors-create-api-office365-outlook.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-office365-outlook.md)

&nbsp;

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.

Avec Office 365 Outlook, vous pouvez effectuer les opérations suivantes :

- Créer votre flux d’activité en fonction des données que vous obtenez d’Office 365 Outlook. 
- Utiliser un déclencheur, par exemple quand il y a un nouveau message électronique ou que vous créez un contact.
- Utiliser des actions qui répondent à un message électronique, créent un événement de calendrier et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d'autres actions. Par exemple, quand il existe un nouvel objet dans Salesforce, vous pouvez prendre cet objet et mettre à jour vos contacts Office 365 Outlook. 
- Ajoutez le connecteur Office 365 Outlook à PowerApps Enterprise. Vos utilisateurs peuvent ensuite utiliser ce connecteur dans leurs applications. 

Pour plus d’informations sur l’ajout d’un connecteur à PowerApps Enterprise, consultez [Register connector in PowerApps](../power-apps/powerapps-register-from-available-apis.md) (Inscrire un connecteur dans PowerApps).

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions

Le connecteur Office 365 Outlook propose les déclencheurs et les actions suivants.

| Déclencheurs | Actions|
| --- | --- |
|<ul><li>Événement imminent</li><li>Arrivée d’un nouveau message électronique</li><li>Quand des éléments sont créés</li><li>Quand des éléments sont mis à jour</li></ul>| <ul><li>Créer un contact</li><li>Créer un événement</li><li>Envoyer un message électronique d'approbation</li><li>Envoyer un courrier électronique</li><li>Supprimer un contact</li><li>Supprimer un courrier électronique</li><li>Supprimer un événement</li><li>Obtenir une pièce jointe</li><li>Obtenir des calendriers</li><li>Obtenir un contact</li><li>Obtenir des dossiers de contacts</li><li>Obtenir des contacts</li><li>Obtenir des messages électroniques</li><li>Obtenir un événement</li><li>Obtenir les événements</li><li>Marquer comme lu</li><li>Événement imminent</li><li>Arrivée d’un nouveau message électronique</li><li>Quand des éléments sont créés</li><li>Quand des éléments sont mis à jour</li><li>Répondre à un message</li><li>Envoyer un message électronique avec des options</li><li>Mettre à jour un contact</li><li>Mettre à jour un événement</li></ul> |

Tous les connecteurs prennent en charge les données aux formats JSON et XML.


## Créer une connexion à Office 365

Quand vous ajoutez ce connecteur à vos applications logiques, vous devez vous connecter à votre compte Office 365 Outlook et autoriser les applications logiques à se connecter à votre compte.

1. Connectez-vous à votre compte Office 365 Outlook.
2. Autorisez vos applications logiques à se connecter à votre compte Office 365 et à l’utiliser. 

Après avoir créé la connexion, vous entrez les propriétés Office 365 Outlook, telles que le chemin du dossier de la boîte de réception ou le message électronique. La section **Informations de référence sur l’API REST** dans cette rubrique décrit ces propriétés.

>[AZURE.TIP] Vous pouvez utiliser cette même connexion Office 365 Outlook dans d’autres applications logiques.

## Informations de référence sur l'API REST Swagger
S’applique à la version 1.0.


### Événement imminent 
Déclenche un flux au démarrage d’un événement de calendrier à venir. ```GET: /Events/OnUpcomingEvents```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|query|(aucun)|Identificateur unique du calendrier|
|lookAheadTimeInMinutes|integer|no|query|15|Plage (en minutes) dans laquelle rechercher les événements à venir.|

#### Response
|Nom|Description|
|---|---|
|200|L’opération a réussi|
|202|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


### Obtenir des messages électroniques 
Récupère des messages électroniques à partir d’un dossier. ```GET: /Mail```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderPath|string|no|query|Inbox|Chemin du dossier duquel récupérer des messages (valeur par défaut : « Inbox »)|
|top|integer|no|query|10|Nombre de messages électroniques à récupérer (valeur par défaut : 10)|
|fetchOnlyUnread|booléenne|no|query|true|Récupérer uniquement les messages non lus ?|
|includeAttachments|booléenne|no|query|false|Si défini sur true, les pièces jointes sont également récupérées avec les messages électroniques.|
|searchQuery|string|no|query|(aucun)|Requête de recherche pour filtrer les messages électroniques|
|skip|integer|no|query|0|Nombre de messages électroniques à ignorer (valeur par défaut : 0)|
|skipToken|string|no|query|(aucun)|Jeton d’évitement pour récupérer une nouvelle page|

#### Response

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


### Envoyer un message électronique 
Envoie un message électronique. ```POST: /Mail```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|emailMessage| |yes|body|(aucun)|Instance du message électronique|


#### Response

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


### Supprimer un message électronique 
Supprime un message électronique en fonction de son identifiant. ```DELETE: /Mail/{messageId}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|messageId|string|yes|path|(aucun)|Identifiant du message à supprimer.|

#### Response

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


### Marquer comme lu 
Marque un message électronique comme lu. ```POST: /Mail/MarkAsRead/{messageId}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|messageId|string|yes|path|(aucun)|Identifiant du message à marquer comme lu|

#### Response

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


### Répondre à un message 
Répond à un message électronique. ```POST: /Mail/ReplyTo/{messageId}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|messageId|string|yes|path|(aucun)|Identifiant du message auquel répondre|
|comment|string|yes|query|(aucun)|Commentaire de réponse|
|replyAll|booléenne|no|query|false|Répondre à tous les destinataires|

#### Response

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


### Obtenir une pièce jointe 
Récupère la pièce jointe à un message en fonction de l’identifiant. ```GET: /Mail/{messageId}/Attachments/{attachmentId}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|messageId|string|yes|path|(aucun)|Identifiant du message|
|attachmentId|string|yes|path|(aucun)|Identifiant de la pièce jointe à télécharger|

#### Response

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


### Arrivée d’un nouveau message électronique 
Déclenche un flux à l’arrivée d’un nouveau message électronique. ```GET: /Mail/OnNewEmail```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderPath|string|no|query|Inbox|Dossier de courrier électronique à récupérer (valeur par défaut : Inbox)|
|to|string|no|query|(aucun)|Adresses de messagerie des destinataires|
|from|string|no|query|(aucun)|Adresse de l’expéditeur|
|importance|string|no|query|Normal|Importance du message (High, Normal, Low) (valeur par défaut : Normal)|
|fetchOnlyWithAttachment|booléenne|no|query|false|Récupérer uniquement les messages électroniques avec une pièce jointe|
|includeAttachments|booléenne|no|query|false|Inclure les pièces jointes|
|subjectFilter|string|no|query|(aucun)|Chaîne à rechercher dans l’objet.|

#### Response

|Nom|Description|
|---|---|
|200|L’opération a réussi.|
|202|Acceptée|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


### Envoyer un message électronique avec des options 
Envoie un message électronique avec plusieurs options et attend que le destinataire réponde avec une des options. ```POST: /mailwithoptions/$subscriptions```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|optionsEmailSubscription| |yes|body|(aucun)|Demande d’abonnement pour courrier électronique avec options|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|201|Abonnement créé|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


### Envoyer un message électronique d’approbation 
Envoie un message électronique d’approbation et attend une réponse du destinataire. ```POST: /approvalmail/$subscriptions```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|approvalEmailSubscription| |yes|body|(aucun)|Demande d’abonnement pour courrier électronique d’approbation|


#### Response

|Nom|Description|
|---|---|
|200|OK|
|201|Abonnement créé|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|




### Obtenir des calendriers 
Récupère des calendriers. ```GET: /datasets/calendars/tables```

Il n'existe aucun paramètre pour cet appel.

#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|




### Obtenir les événements 
Récupère des éléments d’un calendrier. ```GET: /datasets/calendars/tables/{table}/items```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique du calendrier à récupérer|
|$skip|integer|no|query|(aucun)|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|integer|no|query|(aucun)|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|
|$filter|string|no|query|(aucun)|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|string|no|query|(aucun)|Requête orderBy ODATA pour spécifier l’ordre des entrées|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Créer un événement 
Crée un événement. ```POST: /datasets/calendars/tables/{table}/items```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un calendrier|
|item| |yes|body|(aucun)|Élément de calendrier à créer|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir un événement 
Extrait un élément spécifique d’un calendrier. ```GET: /datasets/calendars/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un calendrier|
|id|string|yes|path|(aucun)|Identificateur unique d’un élément de calendrier à récupérer|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Supprimer un événement 
Supprime un élément de calendrier. ```DELETE: /datasets/calendars/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un calendrier|
|id|string|yes|path|(aucun)|Identificateur unique de l’élément de calendrier à supprimer|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Mettre à jour un événement 
Met à jour partiellement un élément de calendrier. ```PATCH: /datasets/calendars/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un calendrier|
|id|string|yes|path|(aucun)|Identificateur unique de l’élément de calendrier à mettre à jour|
|item| |yes|body|(aucun)|Élément de calendrier à mettre à jour|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Quand des éléments sont créés 
Déclenché à la création d’un élément de calendrier. ```GET: /datasets/calendars/tables/{table}/onnewitems```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un calendrier|
|$skip|integer|no|query|(aucun)|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|integer|no|query|(aucun)|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|
|$filter|string|no|query|(aucun)|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|string|no|query|(aucun)|Requête orderBy ODATA pour spécifier l’ordre des entrées|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Quand des éléments sont mis à jour 
Déclenché quand un élément de calendrier est modifié. ```GET: /datasets/calendars/tables/{table}/onupdateditems```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un calendrier|
|$skip|integer|no|query|(aucun)|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|integer|no|query|(aucun)|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|
|$filter|string|no|query|(aucun)|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|string|no|query|(aucun)|Requête orderBy ODATA pour spécifier l’ordre des entrées|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir des dossiers de contacts 
Récupère des dossiers de contacts. ```GET: /datasets/contacts/tables```

Il n'existe aucun paramètre pour cet appel.

#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir des contacts 
Récupère les contacts d’un dossier de contacts. ```GET: /datasets/contacts/tables/{table}/items```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique du dossier de contacts à récupérer|
|$skip|integer|no|query|(aucun)|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|integer|no|query|(aucun)|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|
|$filter|string|no|query|(aucun)|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|string|no|query|(aucun)|Requête orderBy ODATA pour spécifier l’ordre des entrées|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Créer un contact 
Crée un contact. ```POST: /datasets/contacts/tables/{table}/items```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un dossier de contacts|
|item| |yes|body|(aucun)|Contact à créer|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir un contact 
Récupère un contact spécifique d’un dossier de contacts. ```GET: /datasets/contacts/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un dossier de contacts|
|id|string|yes|path|(aucun)|Identificateur unique d’un contact à récupérer|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Supprimer un contact 
Supprime un contact. ```DELETE: /datasets/contacts/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un dossier de contacts.|
|id|string|yes|path|(aucun)|Identificateur unique du contact à supprimer|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Mettre à jour un contact 
Met à jour partiellement un contact. ```PATCH: /datasets/contacts/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un dossier de contacts|
|id|string|yes|path|(aucun)|Identificateur unique du contact à mettre à jour|
|item| |yes|body|(aucun)|Élément de contact à mettre à jour|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## Définitions d'objet

#### TriggerBatchResponse[IDictionary[String,Object]]

| Nom | Type de données |Requis|
|---|---|---|
|value|array|no|


#### SendMessage : envoyer un message électronique

| Nom | Type de données |Requis|
|---|---|---|
|Pièces jointes|array|no|
|À partir d'une base|string|no|
|Cc|string|no|
|Cci|string|no|
|Objet|string|yes|
|Corps|string|yes|
|Importance|string|no|
|IsHtml|booléenne|no|
|À|string|yes|

#### SendAttachment : pièce jointe

| Nom | Type de données |Requis|
|---|---|---|
|@odata.type|string|no|
|Nom|string|yes|
|ContentBytes|string|yes|


#### ReceiveMessage : recevoir un message électronique

| Nom | Type de données |Requis|
|---|---|---|
|ID|string|no|
|IsRead|booléenne|no|
|HasAttachment|booléenne|no|
|DateTimeReceived|string|no|
|Pièces jointes|array|no|
|À partir d'une base|string|no|
|Cc|string|no|
|Cci|string|no|
|Objet|string|yes|
|Corps|string|yes|
|Importance|string|no|
|IsHtml|booléenne|no|
|À|string|yes|


#### ReceiveAttachment : recevoir une pièce jointe

| Nom | Type de données |Requis|
|---|---|---|
|ID|string|yes|
|ContentType|string|yes|
|@odata.type|string|no|
|Nom|string|no|
|ContentBytes|string|yes|


#### DigestMessage : envoyer un message électronique

| Nom | Type de données |Requis|
|---|---|---|
|Objet|string|yes|
|Corps|string|no|
|Importance|string|no|
|Digest|array|yes|
|Pièces jointes|array|no|
|À|string|yes|

#### TriggerBatchResponse[ReceiveMessage]

| Nom | Type de données |Requis|
|---|---|---|
|value|array|no|


#### DataSetsMetadata

| Nom | Type de données |Requis|
|---|---|---|
|tabular|non défini|no|
|objet blob|non défini|no|


#### TabularDataSetsMetadata

| Nom | Type de données |Requis|
|---|---|---|
|source|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|


#### BlobDataSetsMetadata

| Nom | Type de données |Requis|
|---|---|---|
|source|string|no|
|displayName|string|no|
|urlEncoding|string|no|


#### TableMetadata

| Nom | Type de données |Requis|
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|non défini|no|


#### OptionsEmailSubscription : modèle d’abonnement au courrier électronique avec options

| Nom | Type de données |Requis|
|---|---|---|
|NotificationUrl|string|no|
|Message|non défini|no|

#### MessageWithOptions : message électronique avec options de l’utilisateur Message attendu dans le cadre de l’entrée de l’utilisateur

| Nom | Type de données |Requis|
|---|---|---|
|Objet|string|yes|
|Options|string|yes|
|Corps|string|no|
|Importance|string|no|
|Pièces jointes|array|no|
|À|string|yes|

#### SubscriptionResponse : modèle d’abonnement au courrier électronique d’approbation

| Nom | Type de données |Requis|
|---|---|---|
|id|string|no|
|resource|string|no|
|notificationType|string|no|
|notificationUrl|string|no|


#### ApprovalEmailSubscription : modèle d’abonnement au courrier électronique d’approbation

| Nom | Type de données |Requis|
|---|---|---|
|NotificationUrl|string|no|
|Message|non défini|no|


#### ApprovalMessage : message électronique d’approbation. Message attendu dans le cadre de l’entrée de l’utilisateur

| Nom | Type de données |Requis|
|---|---|---|
|Objet|string|yes|
|Options|string|yes|
|Corps|string|no|
|Importance|string|no|
|Pièces jointes|array|no|
|À|string|yes|

#### ApprovalEmailResponse : réponse par courrier électronique d’approbation

| Nom | Type de données |Requis|
|---|---|---|
|SelectedOption|string|no|

#### TablesList

| Nom | Type de données |Requis|
|---|---|---|
|value|array|no|


#### Table

| Nom | Type de données |Requis|
|---|---|---|
|Nom|string|no|
|DisplayName|string|no|


#### Item

| Nom | Type de données |Requis|
|---|---|---|
|ItemInternalId|string|no|


#### CalendarItemsList : liste des éléments de calendrier

| Nom | Type de données |Requis|
|---|---|---|
|value|array|no|


#### CalendarItem : représente un élément de table de calendrier

| Nom | Type de données |Requis|
|---|---|---|
|ItemInternalId|string|no|


#### ContactItemsList : liste des éléments de contact

| Nom | Type de données |Requis|
|---|---|---|
|value|array|no|


#### ContactItem : représente un élément de la table de contacts

| Nom | Type de données |Requis|
|---|---|---|
|ItemInternalId|string|no|


#### DataSetsList

| Nom | Type de données |Requis|
|---|---|---|
|value|array|no|


#### DataSet

| Nom | Type de données | Requis|
|---|---|---|
|Nom|string|no|
|DisplayName|string|no|


## Étapes suivantes

[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)

Revenir à la [liste des API](apis-list.md)

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-outlook/aad-tenant-applications.png
[8]: ./media/connectors-create-api-office365-outlook/aad-tenant-applications-add-appinfo.png
[9]: ./media/connectors-create-api-office365-outlook/aad-tenant-applications-add-app-properties.png
[10]: ./media/connectors-create-api-office365-outlook/contoso-aad-app.png
[11]: ./media/connectors-create-api-office365-outlook/contoso-aad-app-configure.png
[12]: ./media/connectors-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook.png
[13]: ./media/connectors-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook-permissions.png

<!---HONumber=AcomDC_0525_2016-->