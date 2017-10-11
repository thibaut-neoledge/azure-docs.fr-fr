---
title: "Ajouter un pare-feu de nouvelle génération dans Azure Security Center | Microsoft Docs"
description: "Ce document vous montre comment implémenter les recommandations du Centre de sécurité **Ajouter un pare-feu de nouvelle génération** et **Acheminer le trafic uniquement via un pare-feu de nouvelle génération**."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 48b99015-4db8-4ce8-85e4-b544c0fa203e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 30589d0a943517c03394a3aae7c03c8094e78c1f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Ajouter un pare-feu de nouvelle génération dans le Centre de sécurité Azure
Azure Security Center peut vous recommander l’ajout d’un pare-feu de nouvelle génération d’un partenaire Microsoft afin de renforcer vos protections de sécurité. Ce document inclut un exemple vous expliquant comment procéder.

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement.  Il ne s’agit pas d’un guide pas à pas.
>
>

## <a name="implement-the-recommendation"></a>Implémenter la recommandation
1. Dans le panneau **Recommandations**, sélectionnez **Ajouter un pare-feu de nouvelle génération**.
   ![Ajouter un pare-feu de nouvelle génération][1]
2. Dans le panneau **Ajouter un pare-feu de nouvelle génération** , sélectionnez un point de terminaison.
   ![Sélectionner un point de terminaison][2]
3. Un second panneau **Ajouter un pare-feu de nouvelle génération** s’ouvre. Vous pouvez choisir d'utiliser une solution existante le cas échéant, ou en créer une. Dans cet exemple, aucune solution existante n’est disponible ; nous allons donc créer un pare-feu de nouvelle génération.
   ![Créer un pare-feu de nouvelle génération][3]
4. Pour créer un pare-feu de nouvelle génération, sélectionnez une solution dans la liste des partenaires intégrés. Dans cet exemple, nous sélectionnons **Check Point**.
   ![Sélectionner une solution de pare-feu de nouvelle génération][4]
5. Le panneau **Check Point** s’ouvre et affiche des informations sur la solution du partenaire. Sélectionnez **Créer** dans le panneau d’informations.
   ![Panneau d’informations du pare-feu][5]
6. Le panneau **Créer une machine virtuelle** s’ouvre. Vous pouvez y entrer les informations nécessaires pour créer une machine virtuelle qui exécute le pare-feu de nouvelle génération. Suivez les étapes et fournissez les informations requises concernant le pare-feu de nouvelle génération. Sélectionnez OK pour appliquer les modifications.
   ![Créer une machine virtuelle pour exécuter un pare-feu de nouvelle génération][6]

## <a name="route-traffic-through-ngfw-only"></a>Acheminer le trafic uniquement via un pare-feu de nouvelle génération
Retournons au panneau **Recommandations** . Une nouvelle entrée nommée **Acheminer le trafic uniquement via un pare-feu de nouvelle génération** a été générée une fois que vous avez ajouté un pare-feu de nouvelle génération via Security Center. Cette recommandation est créée uniquement si vous avez installé votre pare-feu de nouvelle génération via le Centre de sécurité. Si vous utilisez des points de terminaison accessibles sur Internet, Security Center vous recommande de configurer des règles de groupe de sécurité réseau qui forcent le trafic entrant vers votre machine virtuelle via votre pare-feu de nouvelle génération.

1. Dans le **panneau Recommandations**, sélectionnez **Acheminer le trafic uniquement via un pare-feu de nouvelle génération**.
   ![Acheminer le trafic uniquement via un pare-feu de nouvelle génération][7]
2. Cette opération ouvre le panneau **Acheminer le trafic uniquement via un pare-feu de nouvelle génération** qui répertorie les machines virtuelles vers lesquelles vous pouvez acheminer le trafic. Sélectionnez une machine virtuelle dans la liste.
   ![Sélectionner une machine virtuelle][8]
3. Un panneau pour la machine virtuelle sélectionnée s’ouvre et affiche les règles de trafic entrant associées. Une description vous fournit plus d’informations sur les étapes suivantes possibles. Sélectionnez **Modifier les règles de trafic entrant** pour poursuivre la modification d’une règle de trafic entrant. La valeur **Source** ne doit pas être définie sur **Quelconque** pour les points de terminaison accessibles sur Internet liés au pare-feu de nouvelle génération. Pour en savoir plus sur les propriétés de la règle de trafic entrant, consultez [Règles de groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md#nsg-rules).
   ![Configurer des règles pour limiter l’accès][9]
   ![Modifier une règle de trafic entrant][10]

## <a name="see-also"></a>Voir aussi
Ce document vous a montré comment implémenter la recommandation du Centre de sécurité « Add a Next Generation Firewall » (Ajouter un pare-feu de nouvelle génération). Pour en savoir plus sur les pare-feu de nouvelle génération et la solution du partenaire Check Point, consultez les rubriques suivantes :

* [Pare-feu de nouvelle génération](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
* [Check Point vSEC](https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/)

Pour plus d’informations sur Security Center, consultez :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez comment les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions partenaires.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : recherchez des billets de blog sur la sécurité et la conformité Azure.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png
[7]: ./media/security-center-add-next-gen-firewall/route-traffic-through-ngfw.png
[8]: ./media/security-center-add-next-gen-firewall/select-vm.png
[9]: ./media/security-center-add-next-gen-firewall/configure-rules-to-limit-access.png
[10]: ./media/security-center-add-next-gen-firewall/edit-inbound-rule.png
