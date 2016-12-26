---
title: Protection de vos applications dans Azure Security Center | Microsoft Docs
description: "Ce document traite des recommandations d’Azure Security Center qui peuvent vous aider à protéger vos applications Azure et à rester en conformité avec les stratégies de sécurité."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: b5fc7a9e-24b1-415f-b3b5-62a53f5dd424
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 174546d59124296711731de6c8d8929bada56baf
ms.openlocfilehash: dda826daa18182c1d415037faed5e832680392e1


---
# <a name="protecting-your-applications-in-azure-security-center"></a>Protection de vos applications dans Azure Security Center
Le Centre de sécurité Azure analyse l’état de sécurité de vos ressources Azure. Lorsque Security Center identifie des failles de sécurité potentielles, il crée des recommandations qui vous guident tout au long du processus de configuration des contrôles nécessaires.  Ces recommandations s’appliquent aux types de ressources Azure : machines virtuelles, mise en réseau, applications et SQL.

Cet article traite des recommandations qui s’appliquent aux applications.  Les recommandations relatives aux applications se concentrent autour du déploiement d’un pare-feu d’applications web.  Utilisez le tableau ci-dessous pour mieux comprendre les recommandations disponibles pour les applications et leurs effets.

## <a name="available-application-recommendations"></a>Recommandations disponibles pour les applications
| Recommandation | Description |
| --- | --- |
| [Ajouter un pare-feu d’applications web](security-center-add-web-application-firewall.md) |Recommande le déploiement d’un pare-feu d’applications web (WAF) pour les points de terminaison web. Vous pouvez protéger plusieurs applications web dans le centre de sécurité en les ajoutant à vos déploiements WAF existants. Les dispositifs WAF (créés à l’aide du modèle de déploiement de Resource Manager) doivent être déployés sur un réseau virtuel distinct. Les dispositifs WAF (créés à l’aide du modèle de déploiement classique) sont limités à l’utilisation d’un groupe de sécurité réseau. Cette prise en charge sera étendue prochainement à un déploiement entièrement personnalisé d’un dispositif WAF (pour les machines virtuelles de type Classique). |
| [Finaliser la protection des applications](security-center-add-web-application-firewall.md#finalize-application-protection) |Pour terminer la configuration d’un pare-feu d’applications web, le trafic doit être redirigé vers l’appliance de pare-feu d’applications web. L’application de cette recommandation permet d’apporter les modifications nécessaires à la configuration. |

## <a name="see-also"></a>Voir aussi
Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, consultez les rubriques suivantes :

* [Protection de vos machines virtuelles dans Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Protection de votre réseau dans Azure Security Center](security-center-network-recommendations.md)
* [Protection de votre service SQL Azure dans Azure Security Center](security-center-sql-service-recommendations.md)

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.



<!--HONumber=Nov16_HO3-->


