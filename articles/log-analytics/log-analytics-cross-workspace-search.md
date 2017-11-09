---
title: "Exécuter des requêtes sur différents espaces de travail Azure Log Analytics | Microsoft Docs"
description: "Cet article explique comment interroger plusieurs espaces de travail d’un abonnement avec des exemples à suivre."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: magoedte
ms.openlocfilehash: 6b6dc6f472a87178c006301f4f692aeff15e257e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="perform-queries-across-multiple-log-analytics-workspaces"></a>Exécuter des requêtes sur différents espaces de travail Log Analytics

Auparavant, avec Azure Log Analytics, on ne pouvait analyser les données que dans l’espace de travail actif, ce qui limitait la capacité à interroger plusieurs espaces de travail définis dans l’abonnement.  

Il est maintenant possible d’interroger plusieurs espaces de travail, et ainsi d’obtenir une vue d’ensemble des données du système.  Ce type de requête n’est possible que sur le [Portail avancé](log-analytics-log-search-portals.md#advanced-analytics-portal), et non sur le Portail Azure.  

## <a name="querying-across-log-analytics-workspaces"></a>Interroger plusieurs espaces de travail Log Analytics
Pour faire référence à un autre espace de travail dans votre requête, utilisez l’identificateur *workspace*.  Par exemple, la requête suivante retourne les nombres synthétiques de mises à jour requises par leur classement dans la table Update à partir de l’espace de travail actif et d’un autre espace de travail nommé *contosoretail-it*.  


## <a name="identifying-resources"></a>Identifier les ressources
Il est possible d’identifier un espace de travail de plusieurs manières :

* Nom de la ressource : il s’agit d’un nom convivial de l’espace de travail, parfois appelé *nom du composant*. 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >L’identification d’un espace de travail par son nom suppose que celui-ci soit unique parmi tous les abonnements accessibles. Si plusieurs applications portent le nom spécifié, la requête échoue en raison de l’ambiguïté. Dans ce cas, vous devez utiliser l’un des autres identificateurs.

* Nom qualifié : c’est le « nom complet » de l’espace de travail, composé du nom de l’abonnement, du groupe de ressources et du nom du composant au format suivant : *nomAbonnement/groupeRessources/nomComposant*. 

    `workspace('contoso/contosoretail/development').requests | count `

    >[!NOTE]
    >Étant donné que les noms des abonnements Azure ne sont pas uniques, cet identificateur peut être ambigu. 
    >

* ID de l’espace de travail : il s’agit de l’identificateur unique et immuable affecté à chaque espace de travail, représenté sous la forme d’un identificateur global unique (GUID).

    `workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count`

* ID de ressource Azure : c’est l’identité unique de l’espace de travail, définie par Azure. Utilisez-le lorsque le nom de la ressource est ambigu.  Pour les espaces de travail, le format est : */subscriptions/idAbonnement/resourcegroups/groupeRessources/providers/microsoft.OperationalInsights/workspaces/nomComposant*.  

    Par exemple :
    ``` 
    `workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

## <a name="next-steps"></a>Étapes suivantes

Pour connaître toutes les options de syntaxe des requêtes disponibles dans Log Analytics, consultez les [Informations de référence sur la Recherche dans les journaux avec Log Analytics](https://docs.loganalytics.io/docs/Language-Reference).    