---
title: "Hubs de tâches dans Fonctions durables - Azure"
description: "Découvrez ce que représente un hub de tâches dans l’extension Fonctions durables pour Azure Functions. Découvrez comment configurer des hubs de tâches."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 313daf1c105caa8569ed43e59d9e18f184599214
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Hubs de tâches dans Fonctions durables (Azure Functions)

A *hub de tâches* pour [Fonctions durables](durable-functions-overview.md) est un conteneur logique pour les orchestrations et les activités dans le contexte d’un compte de stockage Azure unique. Plusieurs fonctions ou applications peuvent exister sur le même hub de tâches, et ce hub sert souvent de conteneur d’applications.

Les hubs de tâches n’ont pas besoin d’être créés explicitement. Ils sont automatiquement initialisés par le runtime, en utilisant un nom déclaré dans le fichier *host.json*. Chaque hub de tâches possède son propre ensemble de files d’attente de stockage, tables et objets blob au sein d’un même compte de stockage. Toutes les applications de fonction qui s’exécutent dans un hub de tâches donné partagent les mêmes ressources de stockage. Les fonctions d’orchestrateur et d’activité peuvent interagir entre elles uniquement si elles appartiennent au même hub de tâches.

## <a name="configuring-a-task-hub-in-hostjson"></a>Configuration d’un hub de tâches dans host.json

Un nom de hub de tâches peut être configuré dans le fichier *host.json* d’une application de fonction.

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

Les noms de hubs de tâches doivent commencer par une lettre et contenir uniquement des lettres et des chiffres. S’il n’est pas spécifié, le nom du hub de tâches par défaut d’une application de fonction est **DurableFunctionsHub**.

> [!NOTE]
> Si plusieurs de vos applications de fonction partagent un compte de stockage, il est recommandé de configurer un nom de hub de tâches différent pour chaque application de fonction. Ainsi, ces applications de fonction sont correctement isolées les unes des autres.

## <a name="azure-storage-resources"></a>Ressources Azure Storage

Un hub de tâches se compose de plusieurs ressources Stockage Azure :

* Une ou plusieurs files d’attente de contrôle.
* Une file d’attente des éléments de travail.
* Une table d’historique.
* Un conteneur de stockage comprenant un ou plusieurs objets blob de bail.

Toutes ces ressources sont automatiquement créées dans le compte Stockage Azure par défaut lorsque des fonctions d’orchestrateur ou d’activité sont exécutées ou planifiées pour l’être. L’article [Performances et mise à l’échelle](durable-functions-perf-and-scale.md) explique comment ces ressources sont utilisées.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez comment gérer les versions](durable-functions-versioning.md)

