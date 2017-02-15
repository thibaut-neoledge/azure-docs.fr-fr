---
title: Wunderlist | Microsoft Docs
description: "Créez des applications logiques avec Azure App Service. Wunderlist fournit un gestionnaire de tâches et de listes de tâches pour aider les utilisateurs à travailler efficacement.  Si vous partagez une liste de courses avec un proche, si vous travaillez sur un projet ou planifiez des vacances, Wunderlist facilite la capture, le partage et le suivi de vos listes de tâches. Wunderlist est instantanément synchronisé entre votre téléphone, votre tablette et votre ordinateur, pour vous permettre d’accéder à toutes vos tâches à partir de n’importe quel endroit."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7a528a47373d7e9ae2cdc7d62328ed8e83112863


---
# <a name="get-started-with-the-wunderlist-connector"></a>Prise en main du connecteur Wunderlist
Wunderlist fournit un gestionnaire de tâches et de listes de tâches pour aider les utilisateurs à travailler efficacement.  Si vous partagez une liste de courses avec un proche, si vous travaillez sur un projet ou planifiez des vacances, Wunderlist facilite la capture, le partage et le suivi de vos listes de tâches. Wunderlist est instantanément synchronisé entre votre téléphone, votre tablette et votre ordinateur, pour vous permettre d’accéder à toutes vos tâches à partir de n’importe quel endroit.

> [!NOTE]
> Cette version de l’article s’applique à la version de schéma 2015-08-01-preview des applications logiques.
> 
> 

Vous pouvez commencer par créer une application logique. Pour cela, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Le connecteur Wunderlist peut être utilisé en tant qu’action ; il possède un ou plusieurs déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML.

 Le connecteur Wunderlist met à votre disposition les actions et/ou les déclencheurs ci-après.

### <a name="wunderlist-actions"></a>Actions de Wunderlist
Vous pouvez effectuer les actions suivantes :

| Action | Description |
| --- | --- |
| [RetrieveLists](connectors-create-api-wunderlist.md#retrievelists) |Récupérer les listes associées à votre compte. |
| [CreateList](connectors-create-api-wunderlist.md#createlist) |Créer une liste. |
| [ListTasks](connectors-create-api-wunderlist.md#listtasks) |Extraire des tâches d’une liste spécifique. |
| [CreateTask](connectors-create-api-wunderlist.md#createtask) |Créer une tâche |
| [ListSubTasks](connectors-create-api-wunderlist.md#listsubtasks) |Récupérer les sous-tâches d’une liste spécifique ou d’une tâche spécifique. |
| [CreateSubTask](connectors-create-api-wunderlist.md#createsubtask) |Créer une sous-tâche dans une tâche spécifique |
| [ListNotes](connectors-create-api-wunderlist.md#listnotes) |Extraire des notes pour une liste spécifique ou une tâche spécifique. |
| [CreateNote](connectors-create-api-wunderlist.md#createnote) |Ajouter une note à une tâche spécifique |
| [ListComments](connectors-create-api-wunderlist.md#listcomments) |Extraire des commentaires de tâches pour une liste spécifique ou une tâche spécifique. |
| [CreateComment](connectors-create-api-wunderlist.md#createcomment) |Ajouter un commentaire à une tâche spécifique |
| [RetrieveReminders](connectors-create-api-wunderlist.md#retrievereminders) |Extraire des rappels pour une liste spécifique ou une tâche spécifique. |
| [CreateReminder](connectors-create-api-wunderlist.md#createreminder) |Définir un rappel. |
| [RetrieveFiles](connectors-create-api-wunderlist.md#retrievefiles) |Extraire des fichiers pour une liste spécifique ou une tâche spécifique. |
| [GetList](connectors-create-api-wunderlist.md#getlist) |Extrait une liste spécifique |
| [DeleteList](connectors-create-api-wunderlist.md#deletelist) |Supprime une liste |
| [UpdateList](connectors-create-api-wunderlist.md#updatelist) |Met à jour une liste spécifique |
| [GetTask](connectors-create-api-wunderlist.md#gettask) |Extrait une tâche spécifique |
| [UpdateTask](connectors-create-api-wunderlist.md#updatetask) |Met à jour une tâche spécifique |
| [DeleteTask](connectors-create-api-wunderlist.md#deletetask) |Supprime une tâche spécifique |
| [GetSubTask](connectors-create-api-wunderlist.md#getsubtask) |Extrait une sous-tâche spécifique |
| [UpdateSubTask](connectors-create-api-wunderlist.md#updatesubtask) |Met à jour une sous-tâche spécifique |
| [DeleteSubTask](connectors-create-api-wunderlist.md#deletesubtask) |Supprime une sous-tâche spécifique |
| [GetNote](connectors-create-api-wunderlist.md#getnote) |Récupérer une note spécifique |
| [UpdateNote](connectors-create-api-wunderlist.md#updatenote) |Mettre à jour une note spécifique |
| [DeleteNote](connectors-create-api-wunderlist.md#deletenote) |Supprimer une note spécifique |
| [GetComment](connectors-create-api-wunderlist.md#getcomment) |Récupérer un commentaire spécifique |
| [UpdateReminder](connectors-create-api-wunderlist.md#updatereminder) |Mettre à jour un rappel spécifique |
| [DeleteReminder](connectors-create-api-wunderlist.md#deletereminder) |Supprimer un rappel spécifique |

### <a name="wunderlist-triggers"></a>Déclencheurs de Wunderlist
Vous pouvez écouter les événements suivants :

| Déclencheur | Description |
| --- | --- |
| Quand une tâche est arrivée à échéance |Déclenche un nouveau flux quand une tâche dans la liste est arrivée à échéance |
| Quand une nouvelle tâche est créée |Déclenche un nouveau flux lors de la création d’une nouvelle tâche dans la liste |
| Quand un rappel se produit |Déclenche un nouveau flux lorsqu’un rappel se produit |

## <a name="create-a-connection-to-wunderlist"></a>Créer une connexion à Wunderlist
Pour créer des applications logiques avec Wunderlist, vous devez d’abord créer une **connexion**, puis fournir les détails pour les propriétés suivantes :

| Propriété | Requis | Description |
| --- | --- | --- |
| Jeton |Oui |Fournir des informations d’identification Wunderlist |

Après avoir créé la connexion, vous pouvez l’utiliser pour exécuter les actions et écouter les déclencheurs décrits dans cet article.

> [!INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)]
> 
> [!TIP]
> Vous pouvez utiliser cette connexion dans d'autres applications logiques.
> 
> 

## <a name="reference-for-wunderlist"></a>Référence pour Wunderlist
S’applique à la version 1.0.

## <a name="triggertaskdue"></a>TriggerTaskDue
Quand une tâche est arrivée à échéance : déclenche un nouveau flux quand une tâche dans la liste est arrivée à échéance

```GET: /trigger/tasksdue```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |(aucun) |ID de la liste |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |

## <a name="triggertasknew"></a>TriggerTaskNew
Quand une nouvelle tâche est créée : déclenche un nouveau flux lors de la création d’une nouvelle tâche dans la liste

```GET: /trigger/tasksnew```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |(aucun) |ID de la liste |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |

## <a name="triggerreminder"></a>TriggerReminder
Quand un rappel se produit : déclenche un nouveau flux lorsqu’un rappel se produit

```GET: /trigger/reminders```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |(aucun) |ID de la liste |
| task_id |integer |no |query |(aucun) |ID de la tâche |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |

## <a name="retrievelists"></a>RetrieveLists
Obtenir des listes : récupérer les listes associées à votre compte.

```GET: /lists```

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 400 |Demande incorrecte |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="createlist"></a>CreateList
Créer une liste : créer une liste.

```POST: /lists```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |(aucun) |Nouvelle liste à créer |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| default |L’opération a échoué. |

## <a name="listtasks"></a>ListTasks
Obtenir des tâches : extraire des tâches d’une liste spécifique.

```GET: /tasks```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |(aucun) |ID de la liste |
| Terminé |booléenne |no |query |(aucun) |Completed |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 400 |Demande incorrecte |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="createtask"></a>CreateTask
Créer une tâche : créer une tâche

```POST: /tasks```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |(aucun) |Nouvelle tâche à créer |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 201 |Date de création |

## <a name="listsubtasks"></a>ListSubTasks
Obtenir des sous-tâches : récupérer les sous-tâches d’une liste spécifique ou d’une tâche spécifique.

```GET: /subtasks```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |(aucun) |ID de la liste |
| task_id |integer |no |query |(aucun) |ID de la tâche |
| Terminé |booléenne |no |query |(aucun) |Completed |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 400 |Demande incorrecte |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="createsubtask"></a>CreateSubTask
Créer une sous-tâche : créer une sous-tâche dans une tâche spécifique

```POST: /subtasks```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |(aucun) |Nouvelle sous-tâche à créer |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 201 |Date de création |

## <a name="listnotes"></a>ListNotes
Obtenir des notes : extraire des notes pour une liste spécifique ou une tâche spécifique.

```GET: /notes```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |(aucun) |ID de la liste |
| task_id |integer |no |query |(aucun) |ID de la tâche |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 400 |Demande incorrecte |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="createnote"></a>CreateNote
Créer une note : ajouter une note à une tâche spécifique

```POST: /notes```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |(aucun) |Nouvelle note à créer |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 201 |Date de création |

## <a name="listcomments"></a>ListComments
Obtenir des commentaires : extraire des commentaires de tâches pour une liste spécifique ou une tâche spécifique.

```GET: /task_comments```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |(aucun) |ID de la liste |
| task_id |integer |no |query |(aucun) |ID de la tâche |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 400 |Demande incorrecte |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="createcomment"></a>CreateComment
Ajouter un commentaire à une tâche : ajouter un commentaire à une tâche spécifique

```POST: /task_comments```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |(aucun) |Nouveau commentaire à créer |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 201 |Date de création |

## <a name="retrievereminders"></a>RetrieveReminders
Obtenir des rappels : extraire des rappels pour une liste spécifique ou une tâche spécifique.

```GET: /reminders```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |(aucun) |ID de la liste |
| task_id |integer |no |query |(aucun) |ID de la tâche |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 400 |Demande incorrecte |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="createreminder"></a>CreateReminder
Définir un rappel : définir un rappel.

```POST: /reminders```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |(aucun) |Nouveau rappel à créer |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| default |L’opération a échoué. |

## <a name="retrievefiles"></a>RetrieveFiles
Obtenir des fichiers : extraire des fichiers pour une liste spécifique ou une tâche spécifique.

```GET: /files```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |(aucun) |ID de la liste |
| task_id |integer |no |query |(aucun) |ID de la tâche |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 400 |Demande incorrecte |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="getlist"></a>GetList
Obtenir une liste : extrait une liste spécifique

```GET: /lists/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |string |Oui |path |(aucun) |ID de la liste |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |

## <a name="deletelist"></a>DeleteList
Supprimer la liste : supprime une liste

```DELETE: /lists/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |integer |Oui |path |(aucun) |ID de la liste |
| revision |integer |yes |query |(aucun) |Revision |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 204 |Pas de contenu |

## <a name="updatelist"></a>UpdateList
Mettre à jour une liste : mettre à jour une liste spécifique

```PATCH: /lists/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |integer |Oui |path |(aucun) |ID de la liste |
| post | |yes |body |(aucun) |Détails de la liste |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |

## <a name="gettask"></a>GetTask
Obtenir une tâche : extrait une tâche spécifique

```GET: /tasks/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |(aucun) |ID de la liste |
| id |integer |Oui |path |(aucun) |ID de la tâche |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |

## <a name="updatetask"></a>UpdateTask
Mettre à jour une tâche : met à jour une tâche spécifique

```PATCH: /tasks/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |(aucun) |ID de la liste |
| id |integer |Oui |path |(aucun) |ID de la tâche |
| post | |yes |body |(aucun) |Détails de la tâche |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |

## <a name="deletetask"></a>DeleteTask
Supprimer une tâche : supprime une tâche spécifique

```DELETE: /tasks/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |(aucun) |ID de la liste |
| id |integer |Oui |path |(aucun) |ID de la tâche |
| revision |integer |yes |query |(aucun) |Revision |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 204 |Pas de contenu |

## <a name="getsubtask"></a>GetSubTask
Obtenir une sous-tâche : extrait une sous-tâche spécifique

```GET: /subtasks/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |string |Oui |path |(aucun) |ID de sous-tâche |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |

## <a name="updatesubtask"></a>UpdateSubTask
Mettre à jour une sous-tâche : met à jour une sous-tâche spécifique

```PATCH: /subtasks/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |integer |Oui |path |(aucun) |ID de sous-tâche |
| post | |yes |body |(aucun) |Détails de la sous-tâche |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |

## <a name="deletesubtask"></a>DeleteSubTask
Supprimer une sous-tâche : supprime une sous-tâche spécifique

```DELETE: /subtasks/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |integer |Oui |path |(aucun) |ID de sous-tâche |
| revision |integer |yes |query |(aucun) |Revision |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 204 |Pas de contenu |

## <a name="getnote"></a>GetNote
Obtenir une note : récupérer une note spécifique

```GET: /notes/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |string |Oui |path |(aucun) |ID de note |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |

## <a name="updatenote"></a>UpdateNote
Mettre à jour une note : mettre à jour une note spécifique

```PATCH: /notes/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |integer |Oui |path |(aucun) |ID de note |
| post | |yes |body |(aucun) |Détails de la note |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |

## <a name="deletenote"></a>DeleteNote
Supprimer une note : supprimer une note spécifique

```DELETE: /notes/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |integer |Oui |path |(aucun) |ID de note |
| revision |integer |yes |query |(aucun) |Revision |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 204 |Pas de contenu |

## <a name="getcomment"></a>GetComment
Obtenir un commentaire : récupérer un commentaire spécifique

```GET: /task_comments/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |string |Oui |path |(aucun) |ID du commentaire |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |

## <a name="updatereminder"></a>UpdateReminder
Mettre à jour un rappel : mettre à jour un rappel spécifique

```PATCH: /reminders/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |integer |Oui |path |(aucun) |ID de rappel |
| post | |yes |body |(aucun) |Détails du rappel |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |

## <a name="deletereminder"></a>DeleteReminder
Supprimer un rappel : supprimer un rappel spécifique

```DELETE: /reminders/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |integer |Oui |path |(aucun) |ID du rappel. |
| revision |integer |yes |query |(aucun) |Revision |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 204 |Pas de contenu |

## <a name="object-definitions"></a>Définitions d’objet
### <a name="list"></a>Énumérer
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |integer |Non |
| created_at |string |Non |
| title |string |Non |
| list_type |string |Non |
| type |string |Non |
| revision |integer |Non |

### <a name="createdlist"></a>CreatedList
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |integer |Non |
| created_at |string |Non |
| title |string |Non |
| revision |integer |Non |
| type |string |Non |

### <a name="task"></a>Task
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |integer |Non |
| assignee_id |integer |Non |
| assigner_id |integer |Non |
| created_at |string |Non |
| created_by_id |integer |Non |
| due_date |string |Non |
| list_id |integer |Non |
| revision |integer |Non |
| starred |booléenne |Non |
| title |string |Non |

### <a name="subtask"></a>Subtask
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |integer |Non |
| task_id |integer |Non |
| created_at |string |Non |
| created_by_id |integer |Non |
| revision |string |Non |
| title |string |Non |

### <a name="note"></a>Remarque
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |integer |Non |
| task_id |integer |Non |
| Contenu |string |Non |
| created_at |string |Non |
| updated_at |string |Non |
| revision |integer |Non |

### <a name="comment"></a>Commentaire
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |integer |Non |
| task_id |integer |Non |
| revision |integer |Non |
| texte |string |Non |
| type |string |Non |
| created_at |string |Non |

### <a name="reminder"></a>Rappel
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |integer |Non |
| date |string |Non |
| task_id |integer |Non |
| revision |integer |Non |
| type |string |Non |
| created_at |string |Non |
| updated_at |string |Non |

### <a name="createdreminder"></a>CreatedReminder
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |integer |Non |
| date |string |Non |
| task_id |integer |Non |
| revision |integer |Non |
| created_at |string |Non |
| updated_at |string |Non |

### <a name="file"></a>Fichier
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |integer |Non |
| url |string |Non |
| task_id |integer |Non |
| list_id |integer |Non |
| user_id |integer |Non |
| file_name |string |Non |
| content_type |string |Non |
| file_size |integer |Non |
| local_created_at |string |Non |
| created_at |string |Non |
| updated_at |string |Non |
| type |string |Non |
| revision |integer |Non |

### <a name="newtask"></a>NewTask
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| list_id |integer |Oui |
| title |string |Oui |
| assignee_id |integer |Non |
| Terminé |booléenne |Non |
| recurrence_type |string |Non |
| recurrence_count |integer |Non |
| due_date |string |Non |
| starred |booléenne |Non |

### <a name="newlist"></a>NewList
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| title |string |Oui |

### <a name="newsubtask"></a>NewSubtask
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| list_id |integer |Oui |
| task_id |integer |Oui |
| title |string |Oui |
| Terminé |booléenne |Non |

### <a name="newnote"></a>NewNote
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| list_id |integer |Oui |
| task_id |integer |Oui |
| Contenu |string |Oui |

### <a name="newcomment"></a>NewComment
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| list_id |integer |Oui |
| task_id |integer |Oui |
| texte |string |Oui |

### <a name="newreminder"></a>NewReminder
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| list_id |integer |Oui |
| task_id |integer |Oui |
| date |string |Oui |

### <a name="updatetask"></a>UpdateTask
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| revision |integer |Non |
| title |string |Non |
| assignee_id |integer |Non |
| Terminé |booléenne |Non |
| recurrence_type |string |Non |
| recurrence_count |integer |Non |
| due_date |string |Non |
| starred |booléenne |Non |

### <a name="updatelist"></a>UpdateList
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| revision |integer |Non |
| title |string |Non |

### <a name="updatesubtask"></a>UpdateSubtask
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| revision |integer |Non |
| title |string |Non |
| Terminé |booléenne |Non |

### <a name="updatenote"></a>UpdateNote
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| revision |integer |Non |
| Contenu |string |Non |

### <a name="updatereminder"></a>UpdateReminder
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| date |string |Non |
| revision |integer |Non |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


