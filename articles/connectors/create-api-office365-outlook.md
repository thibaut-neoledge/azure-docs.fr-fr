<properties
pageTitle="Ajouter l’API Office 365 Outlook à vos applications logiques | Microsoft Azure"
description="Vue d’ensemble de l’API Office 365 Outlook avec les paramètres de l’API REST"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="dwrede"	
editor="" tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="02/22/2016"
ms.author="deonhe"/>

# Prendre en main l’API Office 365 Outlook

L’API Office 365 permet d’interagir avec Office 365. Par exemple : création, modification et mise à jour de contacts et d’éléments de calendrier.

>[AZURE.NOTE] Cette version de l’article s’applique à la version de schéma 2015-08-01-preview des applications logiques. Pour la version de schéma 2014-12-01-preview, cliquez sur [API Office 365](../app-service-logic/app-service-logic-connector-Office365.md).

Avec Office 365, vous pouvez effectuer les opérations suivantes :

* Créer des applications logiques
* Créer des applications PowerApps

Pour plus d’informations sur l’ajout d’une API à PowerApps Enterprise, consultez [Inscrire une API dans PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Pour ajouter une opération à des applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## À propos des déclencheurs et des actions

L’API Office 365 peut être utilisée en tant qu’action ; elle possède un ou plusieurs déclencheurs. Toutes les API prennent en charge les données aux formats JSON et XML.

 L’API Office 365 met à votre disposition les actions et/ou les déclencheurs ci-après.

### Actions Office 365
Vous pouvez effectuer les actions suivantes :

|Action|Description|
|--- | ---|
|GetEmails|Récupère des messages électroniques à partir d’un dossier|
|SendEmail|Envoie un message électronique|
|DeleteEmail|Supprime un message électronique en fonction de son identifiant|
|MarkAsRead|Marque un message électronique comme lu|
|ReplyTo|Répond à un message électronique|
|GetAttachment|Récupère la pièce jointe à un message en fonction de l’identifiant|
|SendMailWithOptions|Envoie un message électronique avec plusieurs options et attend que le destinataire réponde avec une des options.|
|SendApprovalMail|Envoie un message électronique d’approbation et attend une réponse du destinataire.|
|CalendarGetTables|Récupère des calendriers|
|CalendarGetItems|Récupère des éléments d’un calendrier|
|CalendarPostItem|Crée un événement|
|CalendarGetItem|Extrait un élément spécifique d’un calendrier|
|CalendarDeleteItem|Supprime un élément de calendrier|
|CalendarPatchItem|Met à jour partiellement un élément de calendrier|
|ContactGetTables|Récupère des dossiers de contacts|
|ContactGetItems|Récupère les contacts d’un dossier de contacts|
|ContactPostItem|Crée un contact|
|ContactGetItem|Récupère un contact spécifique d’un dossier de contacts|
|ContactDeleteItem|Supprime un contact|
|ContactPatchItem|Met à jour partiellement un contact|
### Déclencheurs Office 365
Vous pouvez écouter les événements suivants :

|Déclencheur | Description|
|--- | ---|
|OnUpcomingEvents|Déclenche un flux au démarrage d’un événement de calendrier à venir|
|OnNewEmail|Déclenche un flux à l’arrivée d’un nouveau message électronique|
|CalendarGetOnNewItems|Déclenché à la création d’un élément de calendrier|
|CalendarGetOnUpdatedItems|Déclenché quand un élément de calendrier est modifié|


## Créer une connexion à Office 365
Pour utiliser l’API Office 365, vous devez créer une **connexion**, puis fournir les détails de ces propriétés :

|Propriété| Requis|Description|
| ---|---|---|
|Jeton|Oui|Fournir les informations d’identification Office 365|


>[AZURE.TIP] Vous pouvez utiliser cette connexion dans d’autres applications logiques.

## Informations de référence sur l’API REST Office 365
#### Cette documentation concerne la version 1.0.


### Événement imminent 


 Déclenche un flux au démarrage d’un événement de calendrier à venir ```GET: /Events/OnUpcomingEvents```



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
------



### Obtenir des messages électroniques 


 Récupère des messages électroniques à partir d’un dossier ```GET: /Mail```



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderPath|string|no|query|Inbox|Chemin du dossier duquel récupérer des messages (valeur par défaut : « Inbox »)|
|top|integer|no|query|10|Nombre de messages électroniques à récupérer (valeur par défaut : 10)|
|fetchOnlyUnread|booléenne|no|query|true|Récupérer uniquement les messages non lus ?|
|includeAttachments|booléenne|no|query|false|Si défini sur true, les pièces jointes sont également récupérées avec les messages électroniques.|
|searchQuery|string|no|query|(aucun)|Requête de recherche pour filtrer les messages électroniques|
|skip|integer|no|query|0|Nombre de messages électroniques à ignorer (valeur par défaut : 0)|
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
------



### Envoyer un message électronique 


 Envoie un message électronique ```POST: /Mail```



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
------



### Supprimer un message électronique 


 Supprime un message électronique en fonction de son identifiant ```DELETE: /Mail/{messageId}```



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
------



### Marquer comme lu 


 Marque un message électronique comme lu ```POST: /Mail/MarkAsRead/{messageId}```



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
------



### Répondre à un message 


 Répond à un message électronique ```POST: /Mail/ReplyTo/{messageId}```



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
------



### Obtenir une pièce jointe 


 Récupère la pièce jointe à un message en fonction de l’identifiant ```GET: /Mail/{messageId}/Attachments/{attachmentId}```



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
------



### Arrivée d’un nouveau message électronique 


 Déclenche un flux à l’arrivée d’un nouveau message électronique ```GET: /Mail/OnNewEmail```



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderPath|string|no|query|Inbox|Dossier de courrier électronique à récupérer (valeur par défaut : Inbox)|
|to|string|no|query|(aucun)|Adresses de messagerie des destinataires|
|from|string|no|query|(aucun)|Adresse de l’expéditeur|
|importance|string|no|query|Normal|Importance du message (High, Normal, Low) (valeur par défaut : Normal)|
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
------



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
------



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
------



### Obtenir des calendriers 


 Récupère des calendriers ```GET: /datasets/calendars/tables```

Il n’existe aucun paramètre pour cet appel
#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Obtenir les événements 


 Récupère des éléments d’un calendrier ```GET: /datasets/calendars/tables/{table}/items```



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique du calendrier à récupérer|
|$skip|integer|no|query|(aucun)|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|integer|no|query|(aucun)|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|
|$filter|string|no|query|(aucun)|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|string|no|query|(aucun)|Requête orderBy ODATA pour spécifier l’ordre des entrées|


#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Créer un événement 


 Crée un événement ```POST: /datasets/calendars/tables/{table}/items```



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un calendrier|
|item| |yes|body|(aucun)|Élément de calendrier à créer|


#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Obtenir un événement 


 Extrait un élément spécifique d’un calendrier ```GET: /datasets/calendars/tables/{table}/items/{id}```



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un calendrier|
|id|string|yes|path|(aucun)|Identificateur unique d’un élément de calendrier à récupérer|


#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Supprimer un événement 


 Supprime un élément de calendrier ```DELETE: /datasets/calendars/tables/{table}/items/{id}```



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un calendrier|
|id|string|yes|path|(aucun)|Identificateur unique de l’élément de calendrier à supprimer|


#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Mettre à jour un événement 


 Met à jour partiellement un élément de calendrier ```PATCH: /datasets/calendars/tables/{table}/items/{id}```



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
------



### Quand des éléments sont créés 


 Déclenché à la création d’un élément de calendrier ```GET: /datasets/calendars/tables/{table}/onnewitems```



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un calendrier|
|$skip|integer|no|query|(aucun)|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|integer|no|query|(aucun)|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|
|$filter|string|no|query|(aucun)|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|string|no|query|(aucun)|Requête orderBy ODATA pour spécifier l’ordre des entrées|


#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Quand des éléments sont mis à jour 


 Déclenché quand un élément de calendrier est modifié ```GET: /datasets/calendars/tables/{table}/onupdateditems```



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un calendrier|
|$skip|integer|no|query|(aucun)|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|integer|no|query|(aucun)|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|
|$filter|string|no|query|(aucun)|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|string|no|query|(aucun)|Requête orderBy ODATA pour spécifier l’ordre des entrées|


#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Obtenir des dossiers de contacts 


 Récupère des dossiers de contacts ```GET: /datasets/contacts/tables```

Il n’existe aucun paramètre pour cet appel
#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Obtenir des contacts 


 Récupère les contacts d’un dossier de contacts ```GET: /datasets/contacts/tables/{table}/items```



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique du dossier de contacts à récupérer|
|$skip|integer|no|query|(aucun)|Nombre d’entrées à ignorer (valeur par défaut : 0)|
|$top|integer|no|query|(aucun)|Nombre maximal d’entrées à récupérer (valeur par défaut : 256)|
|$filter|string|no|query|(aucun)|Requête filter ODATA pour limiter le nombre d’entrées|
|$orderby|string|no|query|(aucun)|Requête orderBy ODATA pour spécifier l’ordre des entrées|


#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Créer un contact 


 Crée un contact ```POST: /datasets/contacts/tables/{table}/items```



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un dossier de contacts|
|item| |yes|body|(aucun)|Contact à créer|


#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Obtenir un contact 


 Récupère un contact spécifique d’un dossier de contacts ```GET: /datasets/contacts/tables/{table}/items/{id}```



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un dossier de contacts|
|id|string|yes|path|(aucun)|Identificateur unique d’un contact à récupérer|


#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Supprimer un contact 


 Supprime un contact ```DELETE: /datasets/contacts/tables/{table}/items/{id}```



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|table|string|yes|path|(aucun)|Identificateur unique d’un dossier de contacts.|
|id|string|yes|path|(aucun)|Identificateur unique du contact à supprimer|


#### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Mettre à jour un contact 


 Met à jour partiellement un contact ```PATCH: /datasets/contacts/tables/{table}/items/{id}```



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
------



## Définition(s) d’objet : 

 **TriggerBatchResponse[IDictionary[String,Object]]**:

Propriétés requises pour TriggerBatchResponse[IDictionary[String,Object]] :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|value|array|



 **SendMessage**: envoyer un message électronique

Propriétés requises pour SendMessage :

Subject, Body, To

**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|Pièces jointes|array|
|À partir|string|
|Cc|string|
|Cci|string|
|Objet|string|
|Corps|string|
|Importance|string|
|IsHtml|booléenne|
|À|string|



 **SendAttachment**: pièce jointe

Propriétés requises pour SendAttachment :

Name, ContentBytes

**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|@odata.type|string|
|Nom|string|
|ContentBytes|string|



 **ReceiveMessage**: recevoir un message électronique

Propriétés requises pour ReceiveMessage :

Subject, Body, To

**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|ID|string|
|IsRead|booléenne|
|HasAttachment|booléenne|
|DateTimeReceived|string|
|Pièces jointes|array|
|À partir|string|
|Cc|string|
|Cci|string|
|Objet|string|
|Corps|string|
|Importance|string|
|IsHtml|booléenne|
|À|string|



 **ReceiveAttachment**: recevoir une pièce jointe

Propriétés requises pour ReceiveAttachment :

Id, ContentType, Name, ContentBytes

**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|ID|string|
|ContentType|string|
|@odata.type|string|
|Nom|string|
|ContentBytes|string|



 **DigestMessage**: envoyer un message électronique

Propriétés requises pour DigestMessage :

Subject, Digest, To

**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|Objet|string|
|Corps|string|
|Importance|string|
|Digest|array|
|Pièces jointes|array|
|À|string|



 **TriggerBatchResponse[ReceiveMessage]**:

Propriétés requises pour TriggerBatchResponse[ReceiveMessage] :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|value|array|



 **DataSetsMetadata**:

Propriétés requises pour DataSetsMetadata :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|tabular|non défini|
|objet blob|non défini|



 **TabularDataSetsMetadata**:

Propriétés requises pour TabularDataSetsMetadata :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|source|string|
|displayName|string|
|urlEncoding|string|
|tableDisplayName|string|
|tablePluralName|string|



 **BlobDataSetsMetadata**:

Propriétés requises pour BlobDataSetsMetadata :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|source|string|
|displayName|string|
|urlEncoding|string|



 **TableMetadata**:

Propriétés requises pour TableMetadata :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|name|string|
|title|string|
|x-ms-permission|string|
|schema|non défini|



 **OptionsEmailSubscription**: modèle d’abonnement au courrier électronique avec options

Propriétés requises pour OptionsEmailSubscription :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|NotificationUrl|string|
|Message|non défini|



 **MessageWithOptions**: message électronique avec options de l’utilisateur Message attendu dans le cadre de l’entrée de l’utilisateur

Propriétés requises pour MessageWithOptions :

Subject, Options, To

**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|Objet|string|
|Options|string|
|Corps|string|
|Importance|string|
|Pièces jointes|array|
|À|string|



 **SubscriptionResponse**: modèle d’abonnement au courrier électronique d’approbation

Propriétés requises pour SubscriptionResponse :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|id|string|
|resource|string|
|notificationType|string|
|notificationUrl|string|



 **ApprovalEmailSubscription**: modèle d’abonnement au courrier électronique d’approbation

Propriétés requises pour ApprovalEmailSubscription :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|NotificationUrl|string|
|Message|non défini|



 **ApprovalMessage**: message électronique d’approbation. Message attendu dans le cadre de l’entrée de l’utilisateur

Propriétés requises pour ApprovalMessage :

Subject, Options, To

**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|Objet|string|
|Options|string|
|Corps|string|
|Importance|string|
|Pièces jointes|array|
|À|string|



 **ApprovalEmailResponse**: réponse par courrier électronique d’approbation

Propriétés requises pour ApprovalEmailResponse :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|SelectedOption|string|



 **TablesList**:

Propriétés requises pour TablesList :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|value|array|



 **Table**:

Propriétés requises pour Table :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|Nom|string|
|DisplayName|string|



 **Item**:

Propriétés requises pour Item :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|ItemInternalId|string|



 **CalendarItemsList**: liste des éléments de calendrier

Propriétés requises pour CalendarItemsList :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|value|array|



 **CalendarItem**: représente un élément de table de calendrier

Propriétés requises pour CalendarItem :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|ItemInternalId|string|



 **ContactItemsList**: liste des éléments de contact

Propriétés requises pour ContactItemsList :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|value|array|



 **ContactItem**: représente un élément de la table de contacts

Propriétés requises pour ContactItem :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|ItemInternalId|string|



 **DataSetsList**:

Propriétés requises pour DataSetsList :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|value|array|



 **DataSet**:

Propriétés requises pour DataSet :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|Nom|string|
|DisplayName|string|


## Étapes suivantes
Après avoir ajouté l’API Office 365 à PowerApps Enterprise, [donnez aux utilisateurs des autorisations](../power-apps/powerapps-manage-api-connection-user-access.md) pour qu’ils puissent utiliser l’API dans leurs applications.

[Créez une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->