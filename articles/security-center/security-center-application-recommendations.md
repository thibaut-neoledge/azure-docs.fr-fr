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
ms.date: 12/01/2016
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: b5bc02082fa8c2681aa67910729870921fec329d
ms.contentlocale: fr-fr
ms.lasthandoff: 09/20/2017

---
# <a name="protecting-your-applications-in-azure-security-center"></a>Protection de vos applications dans Azure Security Center
Le Centre de sécurité Azure analyse l’état de sécurité de vos ressources Azure. Lorsque Security Center identifie des failles de sécurité potentielles, il crée des recommandations qui vous guident tout au long du processus de configuration des contrôles nécessaires.  Ces recommandations s’appliquent aux types de ressources Azure : machines virtuelles, mise en réseau, applications et SQL.

Cet article traite des recommandations qui s’appliquent aux applications.  Les recommandations relatives aux applications se concentrent autour du déploiement d’un pare-feu d’applications web.  Utilisez le tableau ci-dessous pour mieux comprendre les recommandations disponibles pour les applications et leurs effets.

## <a name="available-application-recommendations"></a>Recommandations disponibles pour les applications
| Recommandation | Description |
| --- | --- |
| [Ajouter un pare-feu d’applications web](security-center-add-web-application-firewall.md) |Recommande le déploiement d’un pare-feu d’applications web (WAF) pour les points de terminaison web. Une recommandation WAF est indiquée pour n’importe quelle IP publique (adresse IP de niveau d’instance ou adresse IP à équilibrage de charge) ayant un groupe de sécurité réseau associé avec des ports web entrants ouverts (80, 443).</br></br>Security Center recommande d’approvisionner un WAF pour vous défendre contre les attaques ciblant vos applications web sur les machines virtuelles et sur l’environnement App Service (ASE). Un environnement App Service (ASE) est une option de plan de service [Premium](https://azure.microsoft.com/pricing/details/app-service/) d'Azure App Service qui fournit un environnement totalement isolé et dédié pour l'exécution sécurisée de vos applications Azure App Service. Pour en savoir plus sur ASE, voir [Documentation sur l’environnement App Service](../app-service/environment/intro.md).</br></br>Vous pouvez protéger plusieurs applications web dans le centre de sécurité en les ajoutant à vos déploiements WAF existants. |
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

