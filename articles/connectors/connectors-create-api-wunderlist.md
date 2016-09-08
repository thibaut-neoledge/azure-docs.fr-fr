<properties
pageTitle="Wunderlist | Microsoft Azure"
description="Créez des applications logiques avec Azure App Service. Wunderlist fournit un gestionnaire de tâches et de listes de tâches pour aider les utilisateurs à travailler efficacement. Si vous partagez une liste de courses avec un proche, si vous travaillez sur un projet ou planifiez des vacances, Wunderlist facilite la capture, le partage et le suivi de vos listes de tâches. Wunderlist est instantanément synchronisé entre votre téléphone, votre tablette et votre ordinateur, pour vous permettre d’accéder à toutes vos tâches à partir de n’importe quel endroit."
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

# Prise en main du connecteur Wunderlist

Wunderlist fournit un gestionnaire de tâches et de listes de tâches pour aider les utilisateurs à travailler efficacement. Si vous partagez une liste de courses avec un proche, si vous travaillez sur un projet ou planifiez des vacances, Wunderlist facilite la capture, le partage et le suivi de vos listes de tâches. Wunderlist est instantanément synchronisé entre votre téléphone, votre tablette et votre ordinateur, pour vous permettre d’accéder à toutes vos tâches à partir de n’importe quel endroit.

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.

Vous pouvez commencer par créer une application logique. Pour cela, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions

Le connecteur Wunderlist peut être utilisé en tant qu’action ; il possède un ou plusieurs déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML.

 Le connecteur Wunderlist met à votre disposition les actions et/ou les déclencheurs ci-après.

### Actions de Wunderlist
Vous pouvez effectuer les actions suivantes :

|Action|Description|
|--- | ---|
|[RetrieveLists](connectors-create-api-wunderlist.md#retrievelists)|Récupérer les listes associées à votre compte.|
|[CreateList](connectors-create-api-wunderlist.md#createlist)|Créer une liste.|
|[ListTasks](connectors-create-api-wunderlist.md#listtasks)|Extraire des tâches d’une liste spécifique.|
|[CreateTask](connectors-create-api-wunderlist.md#createtask)|Créer une tâche|
|[ListSubTasks](connectors-create-api-wunderlist.md#listsubtasks)|Récupérer les sous-tâches d’une liste spécifique ou d’une tâche spécifique.|
|[CreateSubTask](connectors-create-api-wunderlist.md#createsubtask)|Créer une sous-tâche dans une tâche spécifique|
|[ListNotes](connectors-create-api-wunderlist.md#listnotes)|Extraire des notes pour une liste spécifique ou une tâche spécifique.|
|[CreateNote](connectors-create-api-wunderlist.md#createnote)|Ajouter une note à une tâche spécifique|
|[ListComments](connectors-create-api-wunderlist.md#listcomments)|Extraire des commentaires de tâches pour une liste spécifique ou une tâche spécifique.|
|[CreateComment](connectors-create-api-wunderlist.md#createcomment)|Ajouter un commentaire à une tâche spécifique|
|[RetrieveReminders](connectors-create-api-wunderlist.md#retrievereminders)|Extraire des rappels pour une liste spécifique ou une tâche spécifique.|
|[CreateReminder](connectors-create-api-wunderlist.md#createreminder)|Définir un rappel.|
|[RetrieveFiles](connectors-create-api-wunderlist.md#retrievefiles)|Extraire des fichiers pour une liste spécifique ou une tâche spécifique.|
|[GetList](connectors-create-api-wunderlist.md#getlist)|Extrait une liste spécifique|
|[DeleteList](connectors-create-api-wunderlist.md#deletelist)|Supprime une liste|
|[UpdateList](connectors-create-api-wunderlist.md#updatelist)|Met à jour une liste spécifique|
|[GetTask](connectors-create-api-wunderlist.md#gettask)|Extrait une tâche spécifique|
|[UpdateTask](connectors-create-api-wunderlist.md#updatetask)|Met à jour une tâche spécifique|
|[DeleteTask](connectors-create-api-wunderlist.md#deletetask)|Supprime une tâche spécifique|
|[GetSubTask](connectors-create-api-wunderlist.md#getsubtask)|Extrait une sous-tâche spécifique|
|[UpdateSubTask](connectors-create-api-wunderlist.md#updatesubtask)|Met à jour une sous-tâche spécifique|
|[DeleteSubTask](connectors-create-api-wunderlist.md#deletesubtask)|Supprime une sous-tâche spécifique|
|[GetNote](connectors-create-api-wunderlist.md#getnote)|Récupérer une note spécifique|
|[UpdateNote](connectors-create-api-wunderlist.md#updatenote)|Mettre à jour une note spécifique|
|[DeleteNote](connectors-create-api-wunderlist.md#deletenote)|Supprimer une note spécifique|
|[GetComment](connectors-create-api-wunderlist.md#getcomment)|Récupérer un commentaire spécifique|
|[UpdateReminder](connectors-create-api-wunderlist.md#updatereminder)|Mettre à jour un rappel spécifique|
|[DeleteReminder](connectors-create-api-wunderlist.md#deletereminder)|Supprimer un rappel spécifique|
### Déclencheurs de Wunderlist
Vous pouvez écouter les événements suivants :

|Déclencheur | Description|
|--- | ---|
|Quand une tâche est arrivée à échéance|Déclenche un nouveau flux quand une tâche dans la liste est arrivée à échéance|
|Quand une nouvelle tâche est créée|Déclenche un nouveau flux lors de la création d’une nouvelle tâche dans la liste|
|Quand un rappel se produit|Déclenche un nouveau flux lorsqu’un rappel se produit|


## Créer une connexion à Wunderlist
Pour créer des applications logiques avec Wunderlist, vous devez d’abord créer une **connexion**, puis fournir les détails pour les propriétés suivantes :

|Propriété| Requis|Description|
| ---|---|---|
|Jeton|Oui|Fournir des informations d’identification Wunderlist|
Après avoir créé la connexion, vous pouvez l’utiliser pour exécuter les actions et écouter les déclencheurs décrits dans cet article.


>[AZURE.INCLUDE [Procédure de création d’une connexion à Wunderlist](../../includes/connectors-create-api-wunderlist.md)]


>[AZURE.TIP] Vous pouvez utiliser cette connexion dans d’autres applications logiques.

## Référence pour Wunderlist
S’applique à la version 1.0.

## TriggerTaskDue
Quand une tâche est arrivée à échéance : déclenche un nouveau flux quand une tâche dans la liste est arrivée à échéance

```GET: /trigger/tasksdue```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|(aucun)|ID de la liste|

#### Réponse

|Name|Description|
|---|---|
|200|L’opération a réussi|


## TriggerTaskNew
Quand une nouvelle tâche est créée : déclenche un nouveau flux lors de la création d’une nouvelle tâche dans la liste

```GET: /trigger/tasksnew```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|(aucun)|ID de la liste|

#### Réponse

|Nom|Description|
|---|---|
|200|L’opération a réussi|


## TriggerReminder
Quand un rappel se produit : déclenche un nouveau flux lorsqu’un rappel se produit

```GET: /trigger/reminders```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|(aucun)|ID de la liste|
|task\_id|integer|no|query|(aucun)|ID de la tâche|

#### Réponse

|Nom|Description|
|---|---|
|200|L’opération a réussi|


## RetrieveLists
Obtenir des listes : récupérer les listes associées à votre compte.

```GET: /lists```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|

#### Réponse

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|Demande incorrecte|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


## CreateList
Créer une liste : créer une liste.

```POST: /lists```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|post| |yes|body|(aucun)|Nouvelle liste à créer|

#### Réponse

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|default|L’opération a échoué.|


## ListTasks
Obtenir des tâches : extraire des tâches d’une liste spécifique.

```GET: /tasks```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|(aucun)|ID de la liste|
|Terminé|booléenne|no|query|(aucun)|Completed|

#### Réponse

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|Demande incorrecte|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


## CreateTask
Créer une tâche : créer une tâche

```POST: /tasks```

| Name| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|post| |yes|body|(aucun)|Nouvelle tâche à créer|

#### Réponse

|Nom|Description|
|---|---|
|201|Date de création|


## ListSubTasks
Obtenir des sous-tâches : récupérer les sous-tâches d’une liste spécifique ou d’une tâche spécifique.

```GET: /subtasks```

| Name| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|(aucun)|ID de la liste|
|task\_id|integer|no|query|(aucun)|ID de la tâche|
|Terminé|booléenne|no|query|(aucun)|Completed|

#### Réponse

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|Demande incorrecte|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


## CreateSubTask
Créer une sous-tâche : créer une sous-tâche dans une tâche spécifique

```POST: /subtasks```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|post| |yes|body|(aucun)|Nouvelle sous-tâche à créer|

#### Réponse

|Name|Description|
|---|---|
|201|Date de création|


## ListNotes
Obtenir des notes : extraire des notes pour une liste spécifique ou une tâche spécifique.

```GET: /notes```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|(aucun)|ID de la liste|
|task\_id|integer|no|query|(aucun)|ID de la tâche|

#### Réponse

|Name|Description|
|---|---|
|200|L’opération a réussi|
|400|Demande incorrecte|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


## CreateNote
Créer une note : ajouter une note à une tâche spécifique

```POST: /notes```

| Name| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|post| |yes|body|(aucun)|Nouvelle note à créer|

#### Réponse

|Nom|Description|
|---|---|
|201|Date de création|


## ListComments
Obtenir des commentaires : extraire des commentaires de tâches pour une liste spécifique ou une tâche spécifique.

```GET: /task_comments```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|(aucun)|ID de la liste|
|task\_id|integer|no|query|(aucun)|ID de la tâche|

#### Réponse

|Name|Description|
|---|---|
|200|L’opération a réussi|
|400|Demande incorrecte|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


## CreateComment
Ajouter un commentaire à une tâche : ajouter un commentaire à une tâche spécifique

```POST: /task_comments```

| Name| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|post| |yes|body|(aucun)|Nouveau commentaire à créer|

#### Réponse

|Name|Description|
|---|---|
|201|Date de création|


## RetrieveReminders
Obtenir des rappels : extraire des rappels pour une liste spécifique ou une tâche spécifique.

```GET: /reminders```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|(aucun)|ID de la liste|
|task\_id|integer|no|query|(aucun)|ID de la tâche|

#### Réponse

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|400|Demande incorrecte|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


## CreateReminder
Définir un rappel : définir un rappel.

```POST: /reminders```

| Name| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|post| |yes|body|(aucun)|Nouveau rappel à créer|

#### Réponse

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|default|L’opération a échoué.|


## RetrieveFiles
Obtenir des fichiers : extraire des fichiers pour une liste spécifique ou une tâche spécifique.

```GET: /files```

| Name| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|(aucun)|ID de la liste|
|task\_id|integer|no|query|(aucun)|ID de la tâche|

#### Réponse

|Name|Description|
|---|---|
|200|L’opération a réussi|
|400|Demande incorrecte|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


## GetList
Obtenir une liste : extrait une liste spécifique

```GET: /lists/{id}```

| Name| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun)|ID de la liste|

#### Réponse

|Name|Description|
|---|---|
|200|OK|


## DeleteList
Supprimer la liste : supprime une liste

```DELETE: /lists/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|integer|yes|path|(aucun)|ID de la liste|
|revision|integer|yes|query|(aucun)|Revision|

#### Réponse

|Nom|Description|
|---|---|
|204|Pas de contenu|


## UpdateList
Mettre à jour une liste : mettre à jour une liste spécifique

```PATCH: /lists/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|integer|yes|path|(aucun)|ID de la liste|
|post| |yes|body|(aucun)|Détails de la liste|

#### Réponse

|Nom|Description|
|---|---|
|200|OK|


## GetTask
Obtenir une tâche : extrait une tâche spécifique

```GET: /tasks/{id}```

| Name| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|(aucun)|ID de la liste|
|id|integer|yes|path|(aucun)|ID de la tâche|

#### Réponse

|Name|Description|
|---|---|
|200|OK|


## UpdateTask
Mettre à jour une tâche : met à jour une tâche spécifique

```PATCH: /tasks/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|(aucun)|ID de la liste|
|id|integer|yes|path|(aucun)|ID de la tâche|
|post| |yes|body|(aucun)|Détails de la tâche|

#### Réponse

|Name|Description|
|---|---|
|200|OK|


## DeleteTask
Supprimer une tâche : supprime une tâche spécifique

```DELETE: /tasks/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|(aucun)|ID de la liste|
|id|integer|yes|path|(aucun)|ID de la tâche|
|revision|integer|yes|query|(aucun)|Revision|

#### Réponse

|Name|Description|
|---|---|
|204|Pas de contenu|


## GetSubTask
Obtenir une sous-tâche : extrait une sous-tâche spécifique

```GET: /subtasks/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun)|ID de sous-tâche|

#### Réponse

|Nom|Description|
|---|---|
|200|OK|


## UpdateSubTask
Mettre à jour une sous-tâche : met à jour une sous-tâche spécifique

```PATCH: /subtasks/{id}```

| Name| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|integer|yes|path|(aucun)|ID de sous-tâche|
|post| |yes|body|(aucun)|Détails de la sous-tâche|

#### Réponse

|Name|Description|
|---|---|
|200|OK|


## DeleteSubTask
Supprimer une sous-tâche : supprime une sous-tâche spécifique

```DELETE: /subtasks/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|integer|yes|path|(aucun)|ID de sous-tâche|
|revision|integer|yes|query|(aucun)|Revision|

#### Réponse

|Nom|Description|
|---|---|
|204|Pas de contenu|


## GetNote
Obtenir une note : récupérer une note spécifique

```GET: /notes/{id}```

| Name| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun)|ID de note|

#### Réponse

|Nom|Description|
|---|---|
|200|OK|


## UpdateNote
Mettre à jour une note : mettre à jour une note spécifique

```PATCH: /notes/{id}```

| Name| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|integer|yes|path|(aucun)|ID de note|
|post| |yes|body|(aucun)|Détails de la note|

#### Réponse

|Nom|Description|
|---|---|
|200|OK|


## DeleteNote
Supprimer une note : supprimer une note spécifique

```DELETE: /notes/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|integer|yes|path|(aucun)|ID de note|
|revision|integer|yes|query|(aucun)|Revision|

#### Réponse

|Name|Description|
|---|---|
|204|Pas de contenu|


## GetComment
Obtenir un commentaire : récupérer un commentaire spécifique

```GET: /task_comments/{id}```

| Name| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun)|ID du commentaire|

#### Réponse

|Name|Description|
|---|---|
|200|OK|


## UpdateReminder
Mettre à jour un rappel : mettre à jour un rappel spécifique

```PATCH: /reminders/{id}```

| Name| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|integer|yes|path|(aucun)|ID de rappel|
|post| |yes|body|(aucun)|Détails du rappel|

#### Réponse

|Nom|Description|
|---|---|
|200|OK|


## DeleteReminder
Supprimer un rappel : supprimer un rappel spécifique

```DELETE: /reminders/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|integer|yes|path|(aucun)|ID du rappel.|
|revision|integer|yes|query|(aucun)|Revision|

#### Réponse

|Name|Description|
|---|---|
|204|Pas de contenu|


## Définitions d’objet 

### Énumérer


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|id|integer|Non |
|created\_at|string|Non |
|title|string|Non |
|list\_type|string|Non |
|type|string|Non |
|revision|integer|Non |



### CreatedList


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|id|integer|Non |
|created\_at|string|Non |
|title|string|Non |
|revision|integer|Non |
|type|string|Non |



### Task


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|id|integer|Non |
|assignee\_id|integer|Non |
|assigner\_id|integer|Non |
|created\_at|string|Non |
|created\_by\_id|integer|Non |
|due\_date|string|Non |
|list\_id|integer|Non |
|revision|integer|Non |
|starred|booléenne|Non |
|title|string|Non |



### Subtask


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|id|integer|Non |
|task\_id|integer|Non |
|created\_at|string|Non |
|created\_by\_id|integer|Non |
|revision|string|Non |
|title|string|Non |



### Remarque


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|id|integer|Non |
|task\_id|integer|Non |
|Contenu|string|Non |
|created\_at|string|Non |
|updated\_at|string|Non |
|revision|integer|Non |



### Commentaire


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|id|integer|Non |
|task\_id|integer|Non |
|revision|integer|Non |
|texte|string|Non |
|type|string|Non |
|created\_at|string|Non |



### Rappel


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|id|integer|Non |
|date|string|Non |
|task\_id|integer|Non |
|revision|integer|Non |
|type|string|Non |
|created\_at|string|Non |
|updated\_at|string|Non |



### CreatedReminder


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|id|integer|Non |
|date|string|Non |
|task\_id|integer|Non |
|revision|integer|Non |
|created\_at|string|Non |
|updated\_at|string|Non |



### Fichier


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|id|integer|Non |
|url|string|Non |
|task\_id|integer|Non |
|list\_id|integer|Non |
|user\_id|integer|Non |
|file\_name|string|Non |
|content\_type|string|Non |
|file\_size|integer|Non |
|local\_created\_at|string|Non |
|created\_at|string|Non |
|updated\_at|string|Non |
|type|string|Non |
|revision|integer|Non |



### NewTask


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|list\_id|integer|Oui |
|title|string|Oui |
|assignee\_id|integer|Non |
|Terminé|booléenne|Non |
|recurrence\_type|string|Non |
|recurrence\_count|integer|Non |
|due\_date|string|Non |
|starred|booléenne|Non |



### NewList


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|title|string|Oui |



### NewSubtask


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|list\_id|integer|Oui |
|task\_id|integer|Oui |
|title|string|Oui |
|Terminé|booléenne|Non |



### NewNote


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|list\_id|integer|Oui |
|task\_id|integer|Oui |
|Contenu|string|Oui |



### NewComment


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|list\_id|integer|Oui |
|task\_id|integer|Oui |
|texte|string|Oui |



### NewReminder


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|list\_id|integer|Oui |
|task\_id|integer|Oui |
|date|string|Oui |



### UpdateTask


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|revision|integer|Non |
|title|string|Non |
|assignee\_id|integer|Non |
|Terminé|booléenne|Non |
|recurrence\_type|string|Non |
|recurrence\_count|integer|Non |
|due\_date|string|Non |
|starred|booléenne|Non |



### UpdateList


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|revision|integer|Non |
|title|string|Non |



### UpdateSubtask


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|revision|integer|Non |
|title|string|Non |
|Terminé|booléenne|Non |



### UpdateNote


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|revision|integer|Non |
|Contenu|string|Non |



### UpdateReminder


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|date|string|Non |
|revision|integer|Non |


## Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->