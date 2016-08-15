<properties
pageTitle="GitHub | Microsoft Azure"
description="Créer des applications logiques avec Azure App Service. GitHub est un servie d’hébergement de dépôt Git sur le web. Il offre toutes les fonctionnalités distribuées de contrôle de révision et de gestion du code source (SCM) de Git, ainsi que ses propres fonctionnalités."
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
ms.date="05/17/2016"
ms.author="deonhe"/>

# Prise en main du connecteur GitHub



Le connecteur GitHub peut être utilisé à partir de :

- [Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md)
- [PowerApps](http://powerapps.microsoft.com)
- [Flux](http://flows.microsoft.com)

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.

Vous pouvez commencer en créant une application logique. Consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions

Le connecteur GitHub peut être utilisé en tant qu’action ; il possède un ou plusieurs déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML.

 Le connecteur GitHub met à votre disposition les actions et/ou les déclencheurs ci-après.

### Actions de GitHub
Vous pouvez effectuer les actions suivantes :

|Action|Description|
|--- | ---|
|[CreateIssue](connectors-create-api-github.md#createissue)|Crée un problème|
### Déclencheurs de GitHub
Vous pouvez écouter les événements suivants :

|Déclencheur | Description|
|--- | ---|
|Lorsqu’un problème est ouvert|Un problème est ouvert|
|Lorsqu’un problème est fermé|Un problème est fermé|
|Lorsqu’un problème est assigné|Un problème est assigné|


## Créer une connexion à GitHub
Pour créer des applications logiques avec GitHub, vous devez d’abord créer une **connexion**, puis fournir les détails pour les propriétés suivantes :

|Propriété| Requis|Description|
| ---|---|---|
|Jeton|Oui|Fournir des informations d’identification GitHub|
Après avoir créé la connexion, vous pouvez l’utiliser pour exécuter les actions et écouter les déclencheurs décrits dans cet article.

>[AZURE.INCLUDE [Procédure de création d’une connexion à GitHub](../../includes/connectors-create-api-github.md)]

>[AZURE.TIP] Vous pouvez utiliser cette connexion dans d'autres applications logiques.

## Référence pour GitHub
S’applique à la version : 1.0

## CreateIssue
Créer un problème : crée un problème

```POST: /repos/{repositoryOwner}/{repositoryName}/issues```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|repositoryOwner|string|yes|path|(aucun)|Propriétaire du dépôt|
|repositoryName|string|yes|path|(aucun)|Nom du dépôt|
|issueBasicDetails| |yes|body|(aucun)|Publication des détails|

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


## IssueOpened
Lorsqu’un problème est ouvert : un problème est ouvert

```GET: /trigger/issueOpened```

Il n’existe aucun paramètre pour cet appel
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


## IssueClosed
Lorsqu’un problème est fermé : un problème est fermé

```GET: /trigger/issueClosed```

Il n’existe aucun paramètre pour cet appel
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


## IssueAssigned
Lorsqu’un problème est assigné : un problème est assigné

```GET: /trigger/issueAssigned```

Il n’existe aucun paramètre pour cet appel
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

### IssueBasicDetailsModel


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|title|string|Oui |
|body|string|Oui |
|assignee|string|Oui |



### IssueDetailsModel


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|title|string|Oui |
|body|string|Oui |
|assignee|string|Oui |
|number|string|Non |
|state|string|Non |
|created\_at|string|Non |
|repository\_url|string|Non |


## Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0803_2016-->