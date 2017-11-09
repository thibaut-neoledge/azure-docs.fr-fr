---
title: "Déplacer une application web Azure vers un autre groupe de ressources | Microsoft Docs"
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
ms.date: 10/19/2017
ms.author: zarizvi
ms.openlocfilehash: 4d5d6540dd0b51a4f172649a1fd9a2343efeceee
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2017
---
# <a name="move-an-azure-web-app-to-another-resource-group"></a>Déplacer une application web Azure vers un autre groupe de ressources
Vous pouvez déplacer une application web et/ou ses ressources associées vers un autre groupe de ressources au sein d’un même abonnement, ou dans un abonnement différent. Cette opération s’effectue dans le cadre de la gestion des ressources standard dans Azure. Pour plus d’informations, consultez [Déplacer des ressources Azure vers un nouveau groupe de ressources ou abonnement](../azure-resource-manager/resource-group-move-resources.md).

## <a name="limitations-when-moving-within-the-same-subscription"></a>Limites à observer lors du déplacement au sein d’un même abonnement

Lorsque vous déplacez une application web _au sein du même abonnement_, vous ne pouvez pas déplacer les certificats SSL téléchargés. Toutefois, vous pouvez déplacer une application web vers le nouveau groupe de ressources sans emporter son certificat SSL téléchargé, et la fonctionnalité SSL de votre application peut continuer de fonctionner. 

Si vous souhaitez déplacer le certificat SSL avec l’application web, suivez ces étapes :

1.  Supprimer le certificat chargé à partir de l’application web
2.  Déplacer l’application web
3.  Charger le certificat sur l’application web déplacée

## <a name="limitations-when-moving-across-subscriptions"></a>Limites à observer lors du déplacement entre des abonnements

Lors du déplacement d’une application Web _entre des abonnements_, les limites suivantes s’appliquent :

- Le groupe de ressources de destination ne doit avoir aucune ressource App Service existante. Les ressources App Service comprennent :
    - Web Apps
    - Plans App Service
    - Certificats SSL chargés ou importés
    - Environnements App Service
- Toutes les ressources App Service du groupe de ressources doivent être déplacées simultanément.
- Les ressources App Service ne peuvent être déplacées qu’à partir du groupe de ressources dans lequel elles ont été créées à l’origine. Si une ressource App Service n’est plus dans son groupe de ressources d’origine, elle doit d’abord réintégrer ce groupe avant de pouvoir être déplacée entre les abonnements. 
