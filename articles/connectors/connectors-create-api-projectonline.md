<properties
pageTitle="ProjectOnline | Microsoft Azure"
description="Créer des applications logiques avec Azure App Service. Project Online est une solution flexible en ligne pour la gestion de portefeuille de projets (PPM) et des tâches quotidiennes Microsoft. Fourni via Office 365, Project Online permet aux organisations de rapidement prendre en main des fonctionnalités de gestion de projets puissantes pour planifier, hiérarchiser et gérer des projets et des investissements de portefeuille de projets, à partir de presque n’importe où sur pratiquement tout appareil."
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="04/29/2016"
ms.author="deonhe"/>

# Prise en main du connecteur ProjectOnline

Project Online est une solution flexible en ligne pour la gestion de portefeuille de projets (PPM) et des tâches quotidiennes Microsoft. Fourni via Office 365, Project Online permet aux organisations de rapidement prendre en main des fonctionnalités de gestion de projets puissantes pour planifier, hiérarchiser et gérer des projets et des investissements de portefeuille de projets, à partir de presque n’importe où sur pratiquement tout appareil.

Le connecteur ProjectOnline peut être utilisé à partir de :

- [Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [Flux](http://flow.microsoft.com)  

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.

Vous pouvez commencer en créant une application logique. Consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions

Le connecteur ProjectOnline peut être utilisé en tant qu’action ; il possède un ou plusieurs déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML.

 Le connecteur ProjectOnline met à votre disposition les actions et/ou les déclencheurs ci-après.

### Actions de ProjectOnline
Vous pouvez effectuer les actions suivantes :

|Action|Description|
|--- | ---|
|[ListProjects](connectors-create-api-projectonline.md#listprojects)|Répertorie les projets de votre site Project Online|
|[CreateProject](connectors-create-api-projectonline.md#createproject)|Crée un nouveau projet sur votre site Project Online|
|[CreateTask](connectors-create-api-projectonline.md#createtask)|Crée une nouvelle tâche dans votre projet|
|[CreateResource](connectors-create-api-projectonline.md#createresource)|Crée des ressources d’entreprise dans votre site Project Online|
|[ListTasks](connectors-create-api-projectonline.md#listtasks)|Répertorie les tâches publiées dans un projet|
|[CheckoutProject](connectors-create-api-projectonline.md#checkoutproject)|Extrait un projet de votre site|
|[PublishProject](connectors-create-api-projectonline.md#publishproject)|Archive et publie un projet existant sur votre site|
### Déclencheurs de ProjectOnline
Vous pouvez écouter les événements suivants :

|Déclencheur | Description|
|--- | ---|
|Quand un nouveau projet est créé|Déclenche un flux lors de la création d’un nouveau projet|
|Quand une nouvelle ressource est créée|Déclenche un flux lors de la création d’une nouvelle ressource|
|Quand une nouvelle tâche est créée|Déclenche un flux lors de la création d’une nouvelle tâche|


## Créer une connexion à ProjectOnline
Pour créer des applications logiques avec ProjectOnline, vous devez d’abord créer une **connexion**, puis fournir les détails pour les propriétés suivantes :

|Propriété| Requis|Description|
| ---|---|---|
|Jeton|Oui|Fournir des informations d’identification ProjectOnline|
Après avoir créé la connexion, vous pouvez l’utiliser pour exécuter les actions et écouter les déclencheurs décrits dans cet article.

>[AZURE.TIP] Vous pouvez utiliser cette connexion dans d'autres applications logiques.

## Référence pour ProjectOnline
S’applique à la version : 1.0

## OnNewProject
Quand un nouveau projet est créé : déclenche un flux lors de la création d’un nouveau projet

```GET: /trigger/_api/ProjectData/Projects```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|string|yes|query|(aucun)|URL du site racine de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam )|

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


## OnNewResource
Quand une nouvelle ressource est créée : déclenche un nouveau flux lors de la création d’une nouvelle ressource

```GET: /trigger/_api/ProjectData/Resources```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|string|yes|query|(aucun)|URL du site racine de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam )|

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


## OnNewTask
Quand une nouvelle tâche est créée : déclenche un fluxlors de la création d’une nouvelle tâche

```GET: /trigger/_api/ProjectData/Tasks```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|string|yes|query|(aucun)|URL du site racine de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam )|

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


## ListProjects
Répertorier les projets : répertorie les projets de votre site Project Online

```GET: /_api/ProjectServer/Projects```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|string|yes|query|(aucun)|URL du site racine de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam )|

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


## CreateProject
Créer un nouveau projet : crée un nouveau projet sur votre site Project Online

```POST: /_api/ProjectServer/Projects```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|string|yes|query|(aucun)|URL du site racine de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam )|
|proj| |yes|body|(aucun)|Nouveau projet à créer|

#### Response

|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|401|Non autorisé|
|403|ForbIDden|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


## CreateTask
Créer une nouvelle tâche : crée une nouvelle tâche dans votre projet

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|string|yes|query|(aucun)|URL du site racine de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam )|
|project\_id|string|yes|path|(aucun)|ID unique du projet auquel ajouter la tâche|
|task| |yes|body|(aucun)|Nouvelle tâche à ajouter au projet|

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


## CreateResource
Créer une nouvelle ressource : crée des ressources d’entreprise dans votre site Project Online

```POST: /_api/ProjectServer/EnterpriseResources```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|string|yes|query|(aucun)|URL du site racine de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam )|
|resource| |yes|body|(aucun)|Nouvelle ressource d’entreprise à ajouter au projet|

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


## ListTasks
Répertorier les tâches : répertorie les tâches publiées dans un projet

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|string|yes|query|(aucun)|URL du site racine de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam )|
|project\_id|string|yes|path|(aucun)|ID unique du projet d’où extraire les tâches|

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


## CheckoutProject
Extraire un projet : extrait un projet de votre site

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|string|yes|query|(aucun)|URL du site racine de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam )|
|project\_id|string|yes|path|(aucun)|ID unique du projet auquel ajouter la tâche|

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


## PublishProject
Archiver et publier le projet : archive et publie un projet existant sur votre site

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|string|yes|query|(aucun)|URL du site racine de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam )|
|project\_id|string|yes|path|(aucun)|ID unique du projet à archiver|

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

### TriggerProjectsWrapper


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|value|array|Non |



### TriggerProject


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|ProjectStartDate|string|Non |
|ProjectFinishDate|string|Non |
|ProjectCreatedDate|string|Non |
|ProjectId|string|Non |
|ProjectModifiedDate|string|Non |
|ProjectType|integer|Non |
|ProjectName|string|Non |



### TriggerResourcesWrapper


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|value|array|Non |



### TriggerResource


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|ResourceId|string|Non |
|ResourceBaseCalendar|string|Non |
|ResourceBookingType|integer|Non |
|ResourceCanLevel|booléenne|Non |
|ResourceCostPerUse|number|Non |
|ResourceCreatedDate|string|Non |
|ResourceEarliestAvailableFrom|string|Non |
|ResourceEmail|string|Non |
|ResourceInitials|string|Non |
|ResourceIsActive|booléenne|Non |
|ResourceIsGeneric|booléenne|Non |
|ResourceLatestAvailableTo|string|Non |
|ResourceModifiedDate|string|Non |
|ResourceName|string|Non |
|ResourceStatsuName|string|Non |
|ResourceType|integer|Non |
|TypeDescription|string|Non |
|TypeName|string|Non |



### TriggerTasksWrapper


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|value|array|Non |



### TriggerTask


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|ProjectId|string|Non |
|TaskId|string|Non |
|ProjectName|string|Non |
|TaskName|string|Non |
|TaskCreatedDate|string|Non |
|TaskModifieddate|string|Non |
|TaskStartDate|string|Non |
|TaskFinishDate|string|Non |
|TaskPriority|integer|Non |
|TaskIsActive|booléenne|Non |



### NewProject


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|Nom|string|Oui |
|Description|string|Non |
|Démarrer|string|Non |



### NewReource


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|Nom|string|Oui |
|IsBudget|booléenne|Non |
|IsGeneric|booléenne|Non |
|IsInactive|booléenne|Non |



### projet


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|ApprovedStart|string|Non |
|ApprovedEnd|string|Non |
|CheckedOutDate|string|Non |
|CheckOutDescription|string|Non |
|CheckOutId|string|Non |
|CreatedDate|string|Non |
|ID|string|Non |
|IsCheckedOut|booléenne|Non |
|LastPublishedDate|string|Non |
|LastSavedDate|string|Non |
|OptimizerDecision|integer|Non |
|PlannerDecision|integer|Non |
|ProjectType|integer|Non |
|Nom|string|Non |
|WinprojVersion|string|Non |



### ProjectsWrapper


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|value|array|Non |



### NewTask


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|parameters|non défini|Oui |



### TaskParameters


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|Nom|string|Oui |
|Remarques|string|Non |
|Démarrer|string|Non |
|Durée|string|Non |



### EnterpriseResource


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|CanLevel|booléenne|Non |
|Code|string|Non |
|CostAccrual|integer|Non |
|CostCenter|string|Non |
|Date de création|string|Non |
|DefaultBookingType|integer|Non |
|Email|string|Non |
|ExternalId|string|Non |
|Groupe|string|Non |
|HireDate|string|Non |
|ID|string|Non |
|Initials|string|Non |
|IsActive|booléenne|Non |
|IsBudget|booléenne|Non |
|IsCheckedOut|booléenne|Non |
|IsGeneric|booléenne|Non |
|IsTeam|booléenne|Non |
|MaterialLabel|string|Non |
|Date de modification|string|Non |
|Nom|string|Non |
|Phonetics|string|Non |
|ResourceType|integer|Non |
|TerminationDate|string|Non |



### TasksWrapper


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|value|array|Non |



### Task


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|Date de création|string|Non |
|Date de modification|string|Non |
|Démarrer|string|Non |
|Terminer|string|Non |
|Nom|string|Non |
|ID|string|Non |
|Priorité|integer|Non |
|PercentComplete|integer|Non |
|Remarques|string|Non |
|Contact|string|Non |


## Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0504_2016-->