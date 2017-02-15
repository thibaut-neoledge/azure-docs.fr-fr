---
title: ProjectOnline | Microsoft Docs
description: "Créez des applications logiques avec Azure App Service. Project Online est une solution flexible en ligne pour la gestion de portefeuille de projets (PPM) et des tâches quotidiennes Microsoft. Fourni via Office 365, Project Online permet aux organisations de rapidement prendre en main des fonctionnalités de gestion de projets puissantes pour planifier, hiérarchiser et gérer des projets et des investissements de portefeuille de projets, à partir de presque n’importe où sur pratiquement tout appareil."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 40ce621e-4925-4653-93bb-71ab9abcbdf1
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2e30909f2917fc01f6066d8489961ca94f34f429


---
# <a name="get-started-with-the-projectonline-connector"></a>Prise en main du connecteur ProjectOnline
Project Online est une solution flexible en ligne pour la gestion de portefeuille de projets (PPM) et des tâches quotidiennes Microsoft. Fourni via Office 365, Project Online permet aux organisations de rapidement prendre en main des fonctionnalités de gestion de projets puissantes pour planifier, hiérarchiser et gérer des projets et des investissements de portefeuille de projets, à partir de presque n’importe où sur pratiquement tout appareil.

> [!NOTE]
> Cette version de l’article s’applique à la version de schéma 2015-08-01-preview des applications logiques. 
> 
> 

Vous pouvez commencer par créer une application logique. Pour cela, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Le connecteur ProjectOnline peut être utilisé en tant qu’action ; il possède un ou plusieurs déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML. 

 Le connecteur ProjectOnline met à votre disposition les actions et/ou les déclencheurs ci-après.

### <a name="projectonline-actions"></a>Actions de ProjectOnline
Vous pouvez effectuer les actions suivantes :

| Action | Description |
| --- | --- |
| [ListProjects](connectors-create-api-projectonline.md#listprojects) |Répertorie les projets de votre site Project Online |
| [CreateProject](connectors-create-api-projectonline.md#createproject) |Crée un nouveau projet sur votre site Project Online |
| [CreateTask](connectors-create-api-projectonline.md#createtask) |Crée une nouvelle tâche dans votre projet |
| [CreateResource](connectors-create-api-projectonline.md#createresource) |Crée des ressources d’entreprise dans votre site Project Online |
| [ListTasks](connectors-create-api-projectonline.md#listtasks) |Répertorie les tâches publiées dans un projet |
| [CheckoutProject](connectors-create-api-projectonline.md#checkoutproject) |Extrait un projet de votre site |
| [PublishProject](connectors-create-api-projectonline.md#publishproject) |Archive et publie un projet existant sur votre site |

### <a name="projectonline-triggers"></a>Déclencheurs de ProjectOnline
Vous pouvez écouter les événements suivants :

| Déclencheur | Description |
| --- | --- |
| Quand un nouveau projet est créé |Déclenche un flux lors de la création d’un nouveau projet |
| Quand une nouvelle ressource est créée |Déclenche un flux lors de la création d’une nouvelle ressource |
| Quand une nouvelle tâche est créée |Déclenche un flux lors de la création d’une nouvelle tâche |

## <a name="create-a-connection-to-projectonline"></a>Créer une connexion à ProjectOnline
Pour créer des applications logiques avec ProjectOnline, vous devez d’abord créer une **connexion**, puis fournir les détails pour les propriétés suivantes : 

| Propriété | Requis | Description |
| --- | --- | --- |
| Jeton |Oui |Fournir des informations d’identification ProjectOnline |

> [!INCLUDE [Steps to create a connection to ProjectOnline](../../includes/connectors-create-api-projectonline.md)]
> 
> [!TIP]
> Vous pouvez utiliser cette connexion dans d'autres applications logiques.
> 
> 

## <a name="reference-for-projectonline"></a>Référence pour ProjectOnline
S’applique à la version 1.0.

## <a name="onnewproject"></a>OnNewProject
Quand un nouveau projet est créé : déclenche un flux lors de la création d’un nouveau projet 

```GET: /trigger/_api/ProjectData/Projects``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |(aucun) |URL du site racine de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam) |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="onnewresource"></a>OnNewResource
Quand une nouvelle ressource est créée : déclenche un nouveau flux lors de la création d’une nouvelle ressource 

```GET: /trigger/_api/ProjectData/Resources``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |(aucun) |URL du site racine de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam) |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="onnewtask"></a>OnNewTask
Quand une nouvelle tâche est créée : déclenche un fluxlors de la création d’une nouvelle tâche 

```GET: /trigger/_api/ProjectData/Tasks``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |(aucun) |URL du site racine de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam) |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="listprojects"></a>ListProjects
Répertorier les projets : répertorie les projets de votre site Project Online 

```GET: /_api/ProjectServer/Projects``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |(aucun) |URL du site racine de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam) |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="createproject"></a>CreateProject
Créer un nouveau projet : crée un nouveau projet sur votre site Project Online 

```POST: /_api/ProjectServer/Projects``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |(aucun) |URL du site racine de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam) |
| proj | |yes |body |(aucun) |Nouveau projet à créer |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |ForbIDden |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="createtask"></a>CreateTask
Créer une nouvelle tâche : crée une nouvelle tâche dans votre projet 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |(aucun) |URL du site racine de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |Oui |path |(aucun) |ID unique du projet auquel ajouter la tâche |
| task | |yes |body |(aucun) |Nouvelle tâche à ajouter au projet |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="createresource"></a>CreateResource
Créer une nouvelle ressource : crée des ressources d’entreprise dans votre site Project Online 

```POST: /_api/ProjectServer/EnterpriseResources``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |(aucun) |URL du site racine de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam) |
| resource | |yes |body |(aucun) |Nouvelle ressource d’entreprise à ajouter au projet |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="listtasks"></a>ListTasks
Répertorier les tâches : répertorie les tâches publiées dans un projet 

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |(aucun) |URL du site racine de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |Oui |path |(aucun) |ID unique du projet d’où extraire les tâches |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="checkoutproject"></a>CheckoutProject
Extraire un projet : extrait un projet de votre site 

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |(aucun) |URL du site racine de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |Oui |path |(aucun) |ID unique du projet auquel ajouter la tâche |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="publishproject"></a>PublishProject
Archiver et publier le projet : archive et publie un projet existant sur votre site 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |(aucun) |URL du site racine de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |Oui |path |(aucun) |ID unique du projet à archiver |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="object-definitions"></a>Définitions d’objet
### <a name="triggerprojectswrapper"></a>TriggerProjectsWrapper
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| value |array |Non |

### <a name="triggerproject"></a>TriggerProject
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| ProjectStartDate |string |Non |
| ProjectFinishDate |string |Non |
| ProjectCreatedDate |string |Non |
| ProjectId |string |Non |
| ProjectModifiedDate |string |Non |
| ProjectType |integer |Non |
| ProjectName |string |Non |

### <a name="triggerresourceswrapper"></a>TriggerResourcesWrapper
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| value |array |Non |

### <a name="triggerresource"></a>TriggerResource
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| ResourceId |string |Non |
| ResourceBaseCalendar |string |Non |
| ResourceBookingType |integer |Non |
| ResourceCanLevel |booléenne |Non |
| ResourceCostPerUse |number |Non |
| ResourceCreatedDate |string |Non |
| ResourceEarliestAvailableFrom |string |Non |
| ResourceEmail |string |Non |
| ResourceInitials |string |Non |
| ResourceIsActive |booléenne |Non |
| ResourceIsGeneric |booléenne |Non |
| ResourceLatestAvailableTo |string |Non |
| ResourceModifiedDate |string |Non |
| ResourceName |string |Non |
| ResourceStatsuName |string |Non |
| ResourceType |integer |Non |
| TypeDescription |string |Non |
| TypeName |string |Non |

### <a name="triggertaskswrapper"></a>TriggerTasksWrapper
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| value |array |Non |

### <a name="triggertask"></a>TriggerTask
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| ProjectId |string |Non |
| TaskId |string |Non |
| ProjectName |string |Non |
| TaskName |string |Non |
| TaskCreatedDate |string |Non |
| TaskModifieddate |string |Non |
| TaskStartDate |string |Non |
| TaskFinishDate |string |Non |
| TaskPriority |integer |Non |
| TaskIsActive |booléenne |Non |

### <a name="newproject"></a>NewProject
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Nom |string |Oui |
| Description |string |Non |
| Démarrer |string |Non |

### <a name="newreource"></a>NewReource
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Nom |string |Oui |
| IsBudget |booléenne |Non |
| IsGeneric |booléenne |Non |
| IsInactive |booléenne |Non |

### <a name="project"></a>projet
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| ApprovedStart |string |Non |
| ApprovedEnd |string |Non |
| CheckedOutDate |string |Non |
| CheckOutDescription |string |Non |
| CheckOutId |string |Non |
| CreatedDate |string |Non |
| ID |string |Non |
| IsCheckedOut |booléenne |Non |
| LastPublishedDate |string |Non |
| LastSavedDate |string |Non |
| OptimizerDecision |integer |Non |
| PlannerDecision |integer |Non |
| ProjectType |integer |Non |
| Nom |string |Non |
| WinprojVersion |string |Non |

### <a name="projectswrapper"></a>ProjectsWrapper
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| value |array |Non |

### <a name="newtask"></a>NewTask
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| parameters |non défini |Oui |

### <a name="taskparameters"></a>TaskParameters
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Nom |string |Oui |
| Remarques |string |Non |
| Démarrer |string |Non |
| Durée |string |Non |

### <a name="enterpriseresource"></a>EnterpriseResource
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| CanLevel |booléenne |Non |
| Code |string |Non |
| CostAccrual |integer |Non |
| CostCenter |string |Non |
| Date de création |string |Non |
| DefaultBookingType |integer |Non |
| Email |string |Non |
| ExternalId |string |Non |
| Groupe |string |Non |
| HireDate |string |Non |
| ID |string |Non |
| Initials |string |Non |
| IsActive |booléenne |Non |
| IsBudget |booléenne |Non |
| IsCheckedOut |booléenne |Non |
| IsGeneric |booléenne |Non |
| IsTeam |booléenne |Non |
| MaterialLabel |string |Non |
| Date de modification |string |Non |
| Nom |string |Non |
| Phonetics |string |Non |
| ResourceType |integer |Non |
| TerminationDate |string |Non |

### <a name="taskswrapper"></a>TasksWrapper
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| value |array |Non |

### <a name="task"></a>Task
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Date de création |string |Non |
| Date de modification |string |Non |
| Démarrer |string |Non |
| Terminer |string |Non |
| Nom |string |Non |
| ID |string |Non |
| Priorité |integer |Non |
| PercentComplete |integer |Non |
| Remarques |string |Non |
| Contact |string |Non |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


