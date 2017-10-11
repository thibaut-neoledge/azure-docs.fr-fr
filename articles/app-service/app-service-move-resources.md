---
title: "Déplacer des ressources d’application web vers un autre groupe de ressources"
description: "Décrit les scénarios dans lesquels vous pouvez déplacer des applications web et services d’application d’un groupe de ressources vers un autre."
services: app-service
documentationcenter: 
author: ZainRizvi
manager: erikre
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: zarizvi
ms.openlocfilehash: 1b5059dc052005b6079f70ecf6771a3771df8d87
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="supported-move-configurations"></a>Configurations de déplacement prises en charge
Vous pouvez déplacer des ressources d’application web Azure à l’aide de l’[API de déplacement des ressources Azure Manager](../azure-resource-manager/resource-group-move-resources.md).

Les applications web Azure prennent actuellement en charge les scénarios de déplacement suivants :

* Déplacez tout le contenu d’un groupe de ressources (applications web, plans de service d’application et certificats) vers un autre groupe de ressources. 
   > [!Note]
   > Le groupe de ressources de destination ne peut pas contenir de ressources Microsoft.Web dans ce scénario.

* Déplacez des applications web individuelles vers un groupe de ressources différent, tout en les hébergeant toujours dans leur plan de service d’application actuel (le plan de service d’application reste dans l’ancien groupe de ressources).


