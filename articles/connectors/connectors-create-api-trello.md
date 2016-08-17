<properties
pageTitle="Trello | Microsoft Azure"
description="Créez des applications logiques avec Azure App Service. Trello vous donne une perspective sur tous vos projets, qu’ils soient personnels ou professionnels. Cet outil simple, gratuit, souple et visuel vous permet de gérer vos projets et d’organiser tout ce que vous souhaitez. Connectez-vous à Trello pour gérer vos tableaux, vos listes et vos cartes"
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
ms.date="05/18/2016"
ms.author="deonhe"/>

# Prise en main du connecteur Trello



Le connecteur Trello peut être utilisé à partir de :

- [Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md)
- [PowerApps](http://powerapps.microsoft.com)
- [Flux](http://flows.microsoft.com)

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.

Vous pouvez commencer en créant une application logique. Consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions

Le connecteur Trello peut être utilisé en tant qu’action ; il possède un ou plusieurs déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML.

 Le connecteur Trello met à votre disposition les actions et/ou les déclencheurs ci-après.

### Actions de Trello
Vous pouvez effectuer les actions suivantes :

|Action|Description|
|--- | ---|
|[ListCards](connectors-create-api-trello.md#listcards)|Répertorie les cartes dans un tableau|
|[GetCard](connectors-create-api-trello.md#getcard)|Obtient une carte par ID|
|[UpdateCard](connectors-create-api-trello.md#updatecard)|Met à jour une carte|
|[DeleteCard](connectors-create-api-trello.md#deletecard)|Supprime une carte|
|[CreateCard](connectors-create-api-trello.md#createcard)|Crée une carte dans votre compte Trello|
|[ListBoards](connectors-create-api-trello.md#listboards)|Répertorie les tableaux|
|[GetBoard](connectors-create-api-trello.md#getboard)|Obtient un tableau par ID|
|[ListLists](connectors-create-api-trello.md#listlists)|Répertorie les listes de cartes dans un tableau|
|[GetList](connectors-create-api-trello.md#getlist)|Obtient une liste par ID|
### Déclencheurs Trello
Vous pouvez écouter les événements suivants :

|Déclencheur | Description|
|--- | ---|
|Lorsqu’une nouvelle carte est ajoutée à un tableau|Déclenche un flux lorsqu’une nouvelle carte est ajoutée à un tableau|
|Lorsqu’une nouvelle carte est ajoutée à une liste|Déclenche un flux lorsqu’une nouvelle carte est ajoutée à une liste|


## Créer une connexion à Trello
Pour créer des applications logiques avec Trello, vous devez d’abord créer une **connexion**, puis fournir les détails pour les propriétés suivantes :

|Propriété| Requis|Description|
| ---|---|---|
|Jeton|Oui|Fournit les informations d’identification Trello|
Après avoir créé la connexion, vous pouvez l’utiliser pour exécuter les actions et écouter les déclencheurs décrits dans cet article.

>[AZURE.INCLUDE [Procédure de création d’une connexion à Trello](../../includes/connectors-create-api-trello.md)]

>[AZURE.TIP] Vous pouvez utiliser cette connexion dans d'autres applications logiques.

## Référence pour Trello
S’applique à la version 1.0.

## OnNewCardInBoard
Lorsqu’une nouvelle carte est ajoutée à un tableau : déclenche un flux lorsqu’une nouvelle carte est ajoutée à un tableau

```GET: /trigger/boards/{board_id}/cards```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|board\_id|string|yes|path|(aucun)|ID unique du tableau dans lequel extraire les cartes|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


## OnNewCardInList
Lorsqu’une nouvelle carte est ajoutée à une liste : déclenche un flux lorsqu’une nouvelle carte est ajoutée à une liste

```GET: /trigger/lists/{list_id}/cards```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|board\_id|string|yes|query|(aucun)|ID unique du tableau dans lequel extraire les cartes|
|list\_id|string|yes|path|(aucun)|ID unique de la liste dans laquelle extraire les cartes|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


## ListCards
Répertorier les cartes dans un tableau : répertorier les cartes dans un tableau

```GET: /boards/{board_id}/cards```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|board\_id|string|yes|path|(aucun)|ID du tableau dans lequel extraire toutes les cartes|
|actions|string|no|query|(aucun)|Répertorie les actions à retourner. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|attachments|booléenne|no|query|(aucun)|Affiche les pièces jointes|
|attachment\_fields|string|no|query|(aucun)|Répertorie les champs de pièce jointe à retourner. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|stickers|booléenne|no|query|(aucun)|Affiche les autocollants|
|membres|booléenne|no|query|(aucun)|Affiche les membres|
|memeber\_fields|string|no|query|(aucun)|Répertorie les champs de membre à retourner. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|CheckItemStates|booléenne|no|query|(aucun)|Retourne les états des cartes|
|Checklists|string|no|query|(aucun)|Affiche les checklists|
|limit|integer|no|query|(aucun)|Nombre maximal de résultats à retourner (entre 1 et 1000)|
|since|string|no|query|(aucun)|Extrait toutes les cartes après cette date|
|avant|string|no|query|(aucun)|Extrait toutes les cartes avant cette date|
|filter|string|no|query|(aucun)|Filtre la réponse|
|fields|string|no|query|(aucun)|Répertorie les champs de carte à retourner. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


## GetCard
Obtenir une carte par ID : obtenir une carte par ID

```GET: /cards/{card_id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|board\_id|string|yes|query|(aucun)|ID du tableau dans lequel extraire les cartes|
|card\_id|string|yes|path|(aucun)|ID de la carte à extraire|
|actions|string|no|query|(aucun)|Répertorie les actions à retourner. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|actions\_entities|booléenne|no|query|(aucun)|Retourne les entités d’action|
|actions\_display|booléenne|no|query|(aucun)|Retourne les affichages d’action|
|actions\_limit|integer|no|query|(aucun)|Nombre maximal d’actions à retourner|
|action\_fields|string|no|query|(aucun)|Liste des champs d’action à retourner pour chaque action. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|action\_memberCreator\_fields|string|no|query|(aucun)|Liste des champs de créateur de membre d’action à retourner. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|attachments|booléenne|no|query|(aucun)|Retourne les pièces jointes|
|attachement\_fields|string|no|query|(aucun)|Liste des champs de pièce jointe à retourner pour chaque pièce jointe. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|membres|booléenne|no|query|(aucun)|Retourne les membres|
|member\_fields|string|no|query|(aucun)|Liste des champs de membre à retourner pour chaque membre. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|membersVoted|booléenne|no|query|(aucun)|Retourne les membres avec vote|
|memberVoted\_fields|string|no|query|(aucun)|Liste des champs de membre avec vote à retourner pour chaque membre avec vote. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|checkItemStates|booléenne|no|query|(aucun)|Retourne les états des cartes|
|checkItemState\_fields|string|no|query|(aucun)|Liste des champs d’état à retourner pour chaque état d’élément de carte. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|checklists|string|no|query|(aucun)|Retourne les checklists|
|checklist\_fields|string|no|query|(aucun)|Liste des champs de checklist à retourner pour chaque checklist. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|board|booléenne|no|query|(aucun)|Retourne le tableau auquel appartient la carte|
|board\_fields|string|no|query|(aucun)|Répertorie les champs de tableau à retourner. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|list|booléenne|no|query|(aucun)|Retourne la liste à laquelle appartient la carte|
|list\_fields|string|no|query|(aucun)|Répertorie les champs de liste à retourner. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|stickers|booléenne|no|query|(aucun)|Retourne les autocollants|
|sticker\_fields|string|no|query|(aucun)|Répertorie les champs d’autocollant à retourner pour chaque autocollant. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|fields|string|no|query|(aucun)|Répertorie les champs de carte à retourner. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


## UpdateCard
Mettre à jour une carte : mettre à jour une carte

```PUT: /cards/{card_id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|board\_id|string|yes|query|(aucun)|ID du tableau à partir duquel extraire des cartes|
|card\_id|string|yes|path|(aucun)|ID de la carte à mettre à jour|
|updateCard| |yes|body|(aucun)|Valeurs de carte mises à jour|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


## DeleteCard
Supprimer la carte : supprimer la carte

```DELETE: /cards/{card_id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|board\_id|string|yes|query|(aucun)|ID du tableau à partir duquel extraire des cartes|
|card\_id|string|yes|path|(aucun)|ID de la carte à supprimer|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


## CreateCard
Crée une carte : crée une carte dans votre compte Trello

```POST: /cards```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|board\_id|string|yes|query|(aucun)|ID unique du tableau dans lequel créer les cartes|
|newCard| |yes|body|(aucun)|Nouvelle carte à ajouter au tableau Trello|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


## ListBoards
Répertorier les tableaux : répertorier les tableaux

```GET: /member/me/boards```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|filter|string|no|query|(aucun)|Répertorie les filtres à appliquer aux résultats du tableau. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|fields|string|no|query|(aucun)|Répertorie les champs de tableau à retourner. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|actions|string|no|query|(aucun)|Répertorie les champs d’action à retourner. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|actions\_entities|booléenne|no|query|(aucun)|Retourne les entités d’action|
|actions\_limit|integer|no|query|(aucun)|Nombre maximal d’actions à retourner|
|actions\_format|string|no|query|(aucun)|Spécifie le format des actions à retourner|
|actions\_since|string|no|query|(aucun)|Retourne les actions après la date spécifiée|
|action\_fields|string|no|query|(aucun)|Répertorie les champs de l’action à retourner. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|memberships|string|no|query|(aucun)|Spécifie les informations d’appartenance à retourner. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|organization|booléenne|no|query|(aucun)|Indique que les informations d’organisation doivent être retournées|
|organization\_fields|string|no|query|(aucun)|Répertorie les champs d’organisation à retourner. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|lists|string|no|query|(aucun)|Spécifie si les listes appartenant au tableau doivent être retournées|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


## GetBoard
Obtenir un tableau par ID : obtenir un tableau par ID

```GET: /boards/{board_id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|board\_id|string|yes|path|(aucun)|ID unique du tableau à obtenir|
|actions|string|no|query|(aucun)|Répertorie les actions à retourner. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|action\_entities|booléenne|no|query|(aucun)|Spécifie si les entités d’action doivent être retournées|
|actions\_display|booléenne|no|query|(aucun)|Spécifie si l’affichage d’actions doit être retourné|
|actions\_format|string|no|query|(aucun)|Spécifie le format des actions à retourner|
|actions\_since|string|no|query|(aucun)|Retourne uniquement les actions après cette date|
|actions\_limit|integer|no|query|(aucun)|Nombre maximal d’actions à retourner|
|action\_fields|string|no|query|(aucun)|Répertorie les champs à retourner avec chaque champ. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|action\_memeber|booléenne|no|query|(aucun)|Spécifie si les membres d’action doivent être retournés|
|action\_member\_fields|string|no|query|(aucun)|Répertorie les champs de membre à retourner avec chaque membre d’action. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|action\_memberCreator|booléenne|no|query|(aucun)|Spécifie si le créateur de membre d’action doit être retourné|
|action\_memberCreator\_fields|string|no|query|(aucun)|Répertorie les champs de créateur de membre d’action à retourner. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|cards|string|no|query|(aucun)|Spécifie les cartes à retourner|
|card\_fields|string|no|query|(aucun)|Répertorie les champs à retourner pour chaque carte. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|card\_attachments|booléenne|yes|query|(aucun)|Spécifie si les pièces jointes aux cartes doivent être retournées|
|card\_attachment\_fields|string|no|query|(aucun)|Répertorie les champs de pièce jointe à retourner pour chaque pièce jointe. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|card\_checklists|string|no|query|(aucun)|Spécifie les checklists à retourner pour chaque carte|
|card\_stickers|booléenne|no|query|(aucun)|Spécifie si les autocollants de carte doivent être retournés|
|boardStarts|string|no|query|(aucun)|Spécifie les étoiles de tableau à retourner|
|étiquettes|string|no|query|(aucun)|Spécifie les étiquettes à retourner|
|label\_fields|string|no|query|(aucun)|Répertorie les champs d’étiquette à retourner pour chaque étiquette. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|labels\_limits|integer|no|query|(aucun)|Nombre maximal d’étiquettes à retourner|
|lists|string|no|query|(aucun)|Spécifie les listes à retourner|
|list\_fields|string|no|query|(aucun)|Répertorie les champs de liste à retourner pour chaque liste. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|memberships|string|no|query|(aucun)|Répertorie les appartenances à retourner. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|memberships\_member|booléenne|no|query|(aucun)|Spécifie si les membres d’appartenance doivent être retournés|
|memberships\_member\_fields|string|no|query|(aucun)|Répertorie les champs de membre à retourner pour chaque membre d’appartenance. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|membres|string|no|query|(aucun)|Répertorie les membres à retourner|
|member\_fields|string|no|query|(aucun)|Répertorie les champs de membre à retourner pour chaque membre. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|membersInvited|string|no|query|(aucun)|Spécifie les membres invités à retourner|
|membersInvited\_fields|string|no|query|(aucun)|Répertorie les champs à retourner pour chacun. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|checklists|string|no|query|(aucun)|Spécifie les checklists à retourner|
|checklist\_fields|string|no|query|(aucun)|Répertorie les champs de checklist à retourner pour chaque checklist. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|organization|booléenne|no|query|(aucun)|Spécifie si les informations d’organisation doivent être retournées|
|organization\_fields|string|no|query|(aucun)|Répertorie les champs d’organisation à retourner pour chaque organisation. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|organization\_memberships|string|no|query|(aucun)|Répertorie les appartenances d’organisation à retourner. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|myPerfs|booléenne|no|query|(aucun)|Spécifie si les performances doivent être retournées|
|fields|string|no|query|(aucun)|Répertorie les champs à retourner. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


## ListLists
Répertorier les listes de cartes dans un tableau : répertorier les listes de cartes dans un tableau

```GET: /boards/{board_id}/lists```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|board\_id|string|yes|path|(aucun)|ID unique du tableau dans lequel extraire les listes|
|cards|string|no|query|(aucun)|Spécifie les cartes à retourner|
|card\_fields|string|no|query|(aucun)|Répertorie les champs de carte à partir desquels effectuer les retours. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|filter|string|no|query|(aucun)|Spécifie la propriété de filtre pour les listes|
|fields|string|no|query|(aucun)|Répertorie les champs à retourner. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


## GetList
Obtenir une liste par ID : obtenir une liste par ID

```GET: /lists/{list_id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|board\_id|string|yes|query|(aucun)|ID unique du tableau à partir duquel extraire les listes|
|list\_id|string|yes|path|(aucun)|ID unique de la liste à extraire|
|cards|string|no|query|(aucun)|Spécifie les cartes à retourner|
|card\_fields|string|no|query|(aucun)|Répertorie les champs de carte à retourner pour chaque carte. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|board|booléenne|no|query|(aucun)|Spécifie si les informations de tableau doivent être retournées|
|board\_fields|string|no|query|(aucun)|Répertorie les champs de tableau à retourner. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|
|fields|string|no|query|(aucun)|Répertorie les champs de liste à retourner. Spécifiez « all » ou une liste de valeurs valides séparées par des virgules.|

#### Response

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

### Card


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|id|string|Non |
|checkItemStates|array|Non |
|closed|booléenne|Non |
|dateLastActivity|string|Non |
|desc|string|Non |
|idBoard|string|Non |
|idList|string|Non |
|idMembersVoted|array|Non |
|idShort|integer|Non |
|idAttachmentCover|string|Non |
|manualCoverAttachment|booléenne|Non |
|idLabels|array|Non |
|name|string|Non |
|pos|integer|Non |
|shortLink|string|Non |
|badges|non défini|Non |
|due|string|Non |
|email|string|Non |
|shortUrl|string|Non |
|subscribed|booléenne|Non |
|url|string|Non |



### Badges


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|Votes|integer|Non |
|ViewingMemberVoted|booléenne|Non |
|Subscribed|booléenne|Non |
|Fogbugz|string|Non |
|CheckItems|integer|Non |
|CheckItemsChecked|integer|Non |
|Commentaires|integer|Non |
|Pièces jointes|integer|Non |
|Description|booléenne|Non |
|Due|string|Non |



### Object


| Nom de la propriété | Type de données | Requis |
|---|---|---|



### CreateCard


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|idList|string|Oui |
|name|string|Oui |
|desc|string|Non |
|pos|string|Non |
|idMembers|string|Non |
|idLabels|string|Non |
|urlSource|string|Non |
|fileSource|string|Non |
|idCardSource|string|Non |
|keepFromSource|string|Non |



### UpdateCard


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|name|string|Non |
|desc|string|Non |
|closed|booléenne|Non |
|idMembers|string|Non |
|idAttachmentCover|string|Non |
|idList|string|Non |
|idBoard|string|Non |
|pos|string|Non |
|due|string|Non |
|subscribed|booléenne|Non |



### Board


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|id|string|Non |
|closed|booléenne|Non |
|dateLastActivity|string|Non |
|dateLastView|string|Non |
|desc|string|Non |
|idOrganization|string|Non |
|invitations|array|Non |
|invited|booléenne|Non |
|labelNames|non défini|Non |
|memberships|array|Non |
|name|string|Non |
|pinned|booléenne|Non |
|powerUps|array|Non |
|perfs|non défini|Non |
|shortLink|string|Non |
|shortUrl|string|Non |
|starred|string|Non |
|subscribed|string|Non |
|url|string|Non |



### Étiquette


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|green|string|Non |
|yellow|string|Non |
|orange|string|Non |
|red|string|Non |
|purple|string|Non |
|blue|string|Non |
|sky|string|Non |
|lime|string|Non |
|pink|string|Non |
|black|string|Non |



### Membership


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|id|string|Non |
|idMember|string|Non |
|memberType|string|Non |
|unconfirmed|booléenne|Non |



### Perfs


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|permissionLevel|string|Non |
|voting|string|Non |
|commentaires|string|Non |
|invitations|string|Non |
|selfJoin|booléenne|Non |
|cardCovers|booléenne|Non |
|calendarFeedEnabled|booléenne|Non |
|background|string|Non |
|backgroundColor|string|Non |
|backgroundImage|string|Non |
|backgroundImageScaled|string|Non |
|backgroundTile|booléenne|Non |
|backgroundBrightness|string|Non |
|canBePublic|booléenne|Non |
|canBeOrg|booléenne|Non |
|canBePrivate|booléenne|Non |
|canInvite|booléenne|Non |



### Énumérer


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|id|string|Non |
|name|string|Non |
|closed|booléenne|Non |
|idBoard|string|Non |
|pos|number|Non |
|subscribed|booléenne|Non |
|cards|array|Non |
|board|non défini|Non |


## Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0803_2016-->