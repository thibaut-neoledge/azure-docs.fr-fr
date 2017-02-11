---
title: Autorisations dans Azure Security Center | Microsoft Docs
description: "Cet article explique comment Azure Security Center utilise le contrôle d’accès en fonction du rôle pour affecter des autorisations aux utilisateurs et identifie les actions autorisées pour chaque rôle."
services: security-center
cloud: na
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
ms.assetid: 
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 5c4a08d96175d431e0a29dfc5927b64567c40117
ms.openlocfilehash: d1c8a39bbcda7d22fdce08d122098e994ca87451


---

# <a name="permissions-in-azure-security-center"></a>Autorisations dans Azure Security Center

Azure Security Center utilise le [contrôle d’accès en fonction du rôle (RBAC)](../active-directory/role-based-access-control-configure.md) qui fournit des [rôles intégrés](../active-directory/role-based-access-built-in-roles.md) susceptibles d’être affectés à des utilisateurs, des groupes et des services dans Azure.

Security Center évalue la configuration de vos ressources pour identifier les vulnérabilités et les problèmes de sécurité. Dans Security Center, vous ne voyez les informations relatives à une ressource que lorsque vous avez reçu le rôle de propriétaire, de collaborateur ou de lecteur pour l’abonnement ou le groupe de ressources auquel appartient la ressource.

## <a name="roles-and-allowed-actions"></a>Rôles et actions autorisées

Le tableau suivant affiche les rôles et les actions autorisées dans Security Center. Un X indique que l’action est autorisée pour ce rôle.

| Rôle | Modifier une stratégie de sécurité | Appliquer des recommandations de sécurité à une ressource | Corriger ou ignorer des alertes | Afficher les alertes d’un abonnement | Afficher les alertes d’une ressource donnée |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Propriétaire de l’abonnement | X | X | X | X | X |
| Collaborateur de l’abonnement | X | X | X | X | X |
| Propriétaire du groupe de ressources | -- | X | -- | -- | X |
| Collaborateur du groupe de ressources | -- | X | -- | -- | X |
| Lecteur | -- | -- | -- | X | X |

> [!NOTE]
> Nous vous recommandons d’attribuer le rôle le moins permissif permettant aux utilisateurs d’effectuer leurs tâches. Par exemple, affectez le rôle Lecteur aux utilisateurs qui n’ont besoin que de consulter des informations sur l’intégrité de la sécurité d’une ressource sans effectuer aucune action, telles que l’application des recommandations ou la modification des stratégies.
>
>

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a expliqué comment Security Center utilise le contrôle d’accès en fonction du rôle pour affecter des autorisations aux utilisateurs et a identifié les actions autorisées pour chaque rôle. Maintenant que vous êtes familiarisé avec les affectations de rôles nécessaires pour surveiller l’état de sécurité de votre abonnement, modifier les stratégies de sécurité et appliquer les recommandations, découvrez comment :

- [Définir des stratégies de sécurité dans Security Center](security-center-policies.md)
- [Gérer les recommandations de sécurité dans Security Center](security-center-recommendations.md)
- [Surveiller l’intégrité de la sécurité de vos ressources Azure](security-center-monitoring.md)
- [Gérer et répondre aux alertes de sécurité dans Security Center](security-center-managing-and-responding-alerts.md)
- [Surveiller les solutions de sécurité des partenaires](security-center-partner-solutions.md)



<!--HONumber=Dec16_HO2-->


