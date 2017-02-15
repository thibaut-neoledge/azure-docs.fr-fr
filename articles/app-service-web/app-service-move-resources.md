---
title: "Déplacer des ressources d’application web vers un autre groupe de ressources"
description: "Décrit les scénarios dans lesquels vous pouvez déplacer des applications web et services d’application d’un groupe de ressources vers un autre."
services: app-service
documentationcenter: 
author: ZainRizvi
manager: wpickett
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2016
ms.author: zarizvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 08b8953b91a87a131f70309e41be3a9ce0123299


---
# <a name="supported-move-configurations"></a>Configurations de déplacement prises en charge
Vous pouvez déplacer des ressources d’application web Azure à l’aide de l’ [API de déplacement des ressources ARM](../resource-group-move-resources.md).

Les applications web Azure prennent actuellement en charge les scénarios de déplacement suivants :

* Déplacement de tout le contenu d’un groupe de ressources (applications web, plans de service d’application et certificats) vers un autre groupe de ressources 
  * Remarque : le groupe de ressources de destination ne peut pas contenir de ressources Microsoft.Web dans ce scénario
* Déplacement des applications web individuelles vers un groupe de ressources différent, tout en les hébergeant toujours dans leur plan de service d’application actuel (le plan de service d’application reste dans l’ancien groupe de ressources)




<!--HONumber=Nov16_HO3-->


