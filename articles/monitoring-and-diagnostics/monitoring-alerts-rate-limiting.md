---
title: Restriction des SMS, e-mails et webhooks | Microsoft Docs
description: "Recevez des notifications par SMS, webhook et e-mail lors de la survenue d’événements précis dans le journal d’activité."
author: anirudhcavale
manager: carmonm
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 1b4196d3b1d41458c7dd20b6986cc09100ae318c
ms.contentlocale: fr-fr
ms.lasthandoff: 05/02/2017


---

# <a name="rate-limiting-for-sms-emails-and-webhooks"></a>Restriction des SMS, e-mails et webhooks
La restriction est une suspension des notifications qui se produit lorsqu’un trop grand nombre d’éléments est envoyé à une adresse de messagerie ou un numéro de téléphone particulier. Elle garantit que la communication autour des alertes de journal d’activité et des alertes d’intégrité du service peut être gérée et contrôlée.

Les règles sont identiques pour les SMS et les e-mails. Les seuils de restriction sont :
 - SMS - 10 messages en une heure
 - E-mail - 100 messages en une heure

## <a name="rate-limit-rules"></a>Règles de restriction
- Un numéro de téléphone ou une adresse de messagerie est soumis à restriction lorsqu’il reçoit plus de messages que le seuil.
- Un numéro de téléphone ou une adresse de messagerie peut faire partie de groupes d’actions sur plusieurs abonnements. La restriction s’applique sur l’ensemble des abonnements, c’est-à-dire qu’elle s’applique dès que le seuil est atteint même si l’envoi provient de plusieurs abonnements.  
- Lorsqu’un numéro de téléphone ou une adresse de messagerie est soumis à restriction, un message supplémentaire du même type est envoyé pour signaler la restriction. Le SMS ou l’e-mail indique la fin de la restriction.

## <a name="rate-limit-of-webhooks"></a>Restriction de webhooks ##
Aucune restriction n’est appliquée aux webhooks actuellement.

## <a name="next-steps"></a>Étapes suivantes ##
En savoir plus sur le [comportement des alertes SMS](monitoring-sms-alert-behavior.md)  
Obtenir une [vue d’ensemble des alertes de journal d’activité](monitoring-overview-alerts.md) et découvrir comment recevoir des alertes  
[Configuration d’alertes lorsqu’une notification d’intégrité de service est publiée](monitoring-activity-log-alerts-on-service-notifications.md)

