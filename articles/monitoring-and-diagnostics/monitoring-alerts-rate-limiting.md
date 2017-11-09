---
title: "Limitation de la fréquence des SMS, e-mails et Webhooks | Microsoft Docs"
description: "Découvrez comment Azure limite le nombre de notifications possibles par SMS, e-mail ou Webhook à partir d’un groupe d’actions."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: bde645624ab1860d19ba18470f55845855a7d1fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="rate-limiting-for-sms-messages-emails-and-webhook-posts"></a>Limitation de la fréquence des SMS, e-mails et publications Webhook
La limitation de la fréquence est une suspension des notifications qui se produit lorsque trop de notifications sont envoyées à une adresse e-mail ou un numéro de téléphone donné. Elle garantit que les alertes sont faciles à gérer et exploitables.

Les règles sont identiques pour les SMS et les e-mails. Voici le seuil de limitation de la fréquence :

 - **SMS** : 10 messages en une heure ;
 - **E-mail** : 100 messages en une heure.

## <a name="rate-limit-rules"></a>Règles de limitation de la fréquence
- Un numéro de téléphone ou une adresse e-mail donné est soumis à une limitation de la fréquence lorsqu’il reçoit plus de messages que ne l’autorise le seuil.
- Un numéro de téléphone ou une adresse de messagerie peut faire partie de groupes d’actions sur plusieurs abonnements. La limitation de la fréquence s’applique à tous les abonnements, dès lors que le seuil est atteint, même si les messages sont envoyés à partir de plusieurs abonnements.  
- Lorsqu’un numéro de téléphone ou une adresse e-mail fait l’objet d’une restriction, une notification supplémentaire est envoyée à ce sujet. Cette notification indique la fin de la restriction.

## <a name="rate-limit-of-webhooks"></a>Limite de fréquence des Webhooks ##
Aucune limitation de la fréquence n’est appliquée aux Webhooks.

## <a name="next-steps"></a>Étapes suivantes ##
* En savoir plus sur le [comportement des alertes SMS](monitoring-sms-alert-behavior.md).
* Obtenir une [vue d’ensemble des alertes du journal d’activité](monitoring-overview-alerts.md) et découvrir comment recevoir des alertes.  
* Découvrir comment [configurer des alertes lorsqu’une notification d’intégrité de service est publiée](monitoring-activity-log-alerts-on-service-notifications.md).
