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
ms.date: 12/21/2016
ms.author: zarizvi
translationtype: Human Translation
ms.sourcegitcommit: 078833a21cd309087b842e21e3fc730695c254dd
ms.openlocfilehash: 89fdeca4ffc1a36e8aff829b00483f208fa03e0f


---
# <a name="supported-move-configurations"></a>Configurations de déplacement prises en charge
Vous pouvez déplacer des ressources d’application web Azure à l’aide de l’[API de déplacement des ressources Azure Manager](../azure-resource-manager/resource-group-move-resources.md).

Les applications web Azure prennent actuellement en charge les scénarios de déplacement suivants :

* Déplacez tout le contenu d’un groupe de ressources (applications web, plans de service d’application et certificats) vers un autre groupe de ressources. 
   > [!Note]
   > Le groupe de ressources de destination ne peut pas contenir de ressources Microsoft.Web dans ce scénario.

* Déplacez des applications web individuelles vers un groupe de ressources différent, tout en les hébergeant toujours dans leur plan de service d’application actuel (le plan de service d’application reste dans l’ancien groupe de ressources).





<!--HONumber=Dec16_HO3-->


